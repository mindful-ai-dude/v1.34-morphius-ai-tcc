# Analysis of Prompts in `v1.28-morphius-ai-full-codebase.txt`.

Based on the analysis, the following files contain prompts or system prompts used to instruct the LLM for specific tasks within the application:

1.  `lib/agents/generate-related-questions.ts`
2.  `lib/agents/manual-researcher.ts`
3.  `lib/agents/researcher.ts`
4.  `lib/streaming/tool-execution.ts`

Here is the precise file name, the full and complete code for each file, and an explanation of what each prompt does:

---

**1. File: `lib/agents/generate-related-questions.ts`**

```typescript
import { relatedSchema } from '@/lib/schema/related'
import { CoreMessage, generateObject } from 'ai'
import {
    getModel,
    getToolCallModel,
    isToolCallSupported
} from '../utils/registry'

export async function generateRelatedQuestions(
  messages: CoreMessage[],
  model: string
) {
  const lastMessages = messages.slice(-1).map(message => ({
    ...message,
    role: 'user'
  })) as CoreMessage[]

  const supportedModel = isToolCallSupported(model)
  const currentModel = supportedModel
    ? getModel(model)
    : getToolCallModel(model)

  try {
    const result = await generateObject({
      model: currentModel,
      // --- START OF PROMPT ---
      system: `As a professional web researcher, your task is to generate a set of three queries that explore the subject matter more deeply, building upon the initial query and the information uncovered in its search results.

      For instance, if the original query was "Starship's third test flight key milestones", your output should follow this format:

      Aim to create queries that progressively delve into more specific aspects, implications, or adjacent topics related to the initial query. The goal is to anticipate the user's potential information needs and guide them towards a more comprehensive understanding of the subject matter.

      YOUR RESPONSE MUST CONTAIN EXACTLY THREE RELATED QUERIES.

      Please match the language of the response to the user's language.`,
      // --- END OF PROMPT ---
      messages: lastMessages,
      schema: relatedSchema
    })

    return result
  } catch (error) {
    console.error('Error generating related questions:', error)
    // Return a default object that matches the schema
    return {
      object: {
        items: [
          { query: 'More information about this topic' },
          { query: 'Recent developments on this subject' },
          { query: 'Expert opinions on this matter' }
        ]
      }
    }
  }
}
```

**Explanation of Prompt(s):**

*   **`system` prompt within `generateObject`:** This prompt instructs the LLM to act as a "professional web researcher". Its specific task is to analyze the last user message and the preceding context (implicitly provided by the `messages` array, although only the last message is explicitly passed here) and generate exactly three follow-up search queries. These queries should delve deeper into the topic, explore related aspects, or anticipate the user's next questions. It emphasizes matching the language of the generated queries to the user's language and strictly requires three queries in the output, formatted according to the `relatedSchema`.

---

**2. File: `lib/agents/manual-researcher.ts`**

```typescript
import { CoreMessage, smoothStream, streamText } from 'ai'
import { getModel } from '../utils/registry'

// --- START OF PROMPT 1 ---
const BASE_SYSTEM_PROMPT = `
Instructions:

You are a helpful AI assistant providing accurate information.

1. Provide comprehensive and detailed responses to user questions
2. Use markdown to structure your responses with appropriate headings
3. Acknowledge when you are uncertain about specific details
4. Focus on maintaining high accuracy in your responses
`
// --- END OF PROMPT 1 ---

// --- START OF PROMPT 2 ---
const SEARCH_ENABLED_PROMPT = `
${BASE_SYSTEM_PROMPT}

When analyzing search results:
1. Analyze the provided search results carefully to answer the user's question
2. Always cite sources using the [number](url) format, matching the order of search results
3. If multiple sources are relevant, include all of them using comma-separated citations
4. Only use information that has a URL available for citation
5. If the search results don't contain relevant information, acknowledge this and provide a general response

Citation Format:
[number](url)
`
// --- END OF PROMPT 2 ---

// --- START OF PROMPT 3 ---
const SEARCH_DISABLED_PROMPT = `
${BASE_SYSTEM_PROMPT}

Important:
1. Provide responses based on your general knowledge
2. Be clear about any limitations in your knowledge
3. Suggest when searching for additional information might be beneficial
`
// --- END OF PROMPT 3 ---

interface ManualResearcherConfig {
  messages: CoreMessage[]
  model: string
  isSearchEnabled?: boolean
}

type ManualResearcherReturn = Parameters<typeof streamText>[0]

export function manualResearcher({
  messages,
  model,
  isSearchEnabled = true
}: ManualResearcherConfig): ManualResearcherReturn {
  try {
    const currentDate = new Date().toLocaleString()
    // Selects which prompt to use based on isSearchEnabled flag
    const systemPrompt = isSearchEnabled
      ? SEARCH_ENABLED_PROMPT
      : SEARCH_DISABLED_PROMPT

    return {
      model: getModel(model),
      // The selected prompt is combined with the current date/time
      system: `${systemPrompt}\nCurrent date and time: ${currentDate}`,
      messages,
      temperature: 0.6,
      topP: 1,
      topK: 40,
      experimental_transform: smoothStream({ chunking: 'word' })
    }
  } catch (error) {
    console.error('Error in manualResearcher:', error)
    throw error
  }
}
```

**Explanation of Prompt(s):**

*   **`BASE_SYSTEM_PROMPT`:** This defines the core persona and basic instructions for the AI assistant when it's operating in "manual" mode (not using native tool calling). It emphasizes helpfulness, accuracy, detailed responses, markdown formatting, and acknowledging uncertainty.
*   **`SEARCH_ENABLED_PROMPT`:** This prompt builds upon the `BASE_SYSTEM_PROMPT`. It's used when the manual researcher *is* expected to receive and process search results (passed in via messages). It adds specific instructions on how to analyze search results, cite sources using a strict `[number](url)` format, handle multiple relevant sources, use only citable information, and respond appropriately if search results are irrelevant.
*   **`SEARCH_DISABLED_PROMPT`:** This prompt also builds upon the `BASE_SYSTEM_PROMPT`. It's used when the manual researcher should *not* expect search results and should rely solely on its internal knowledge. It instructs the AI to be clear about knowledge limitations and suggest when web searching might be helpful.
*   **Combined `system` prompt in `manualResearcher` function:** The function dynamically selects either `SEARCH_ENABLED_PROMPT` or `SEARCH_DISABLED_PROMPT` based on the `isSearchEnabled` flag and appends the current date/time before passing it to the `streamText` function. This provides temporal context and sets the specific operational mode (with or without search result analysis) for the LLM.

---

**3. File: `lib/agents/researcher.ts`**

```typescript
import { CoreMessage, smoothStream, streamText } from 'ai'
import { retrieveTool } from '../tools/retrieve'
import { searchTool } from '../tools/search'
import { videoSearchTool } from '../tools/video-search'
import { getModel } from '../utils/registry'

// --- START OF PROMPT ---
const SYSTEM_PROMPT = `
Instructions:

You are a helpful AI assistant with access to real-time web search, content retrieval, and video search capabilities.
When asked a question, you should:
1. Search for relevant information using the search tool when needed
2. Use the retrieve tool to get detailed content from specific URLs
3. Use the video search tool when looking for video content
4. Analyze all search results to provide accurate, up-to-date information
5. Always cite sources using the [number](url) format, matching the order of search results. If multiple sources are relevant, include all of them, and comma separate them. Only use information that has a URL available for citation.
6. If results are not relevant or helpful, rely on your general knowledge
7. Provide comprehensive and detailed responses based on search results, ensuring thorough coverage of the user's question
8. Use markdown to structure your responses. Use headings to break up the content into sections.
9. **Use the retrieve tool only with user-provided URLs.**

Citation Format:
[number](url)
`
// --- END OF PROMPT ---

type ResearcherReturn = Parameters<typeof streamText>[0]

export function researcher({
  messages,
  model,
  searchMode
}: {
  messages: CoreMessage[]
  model: string
  searchMode: boolean
}): ResearcherReturn {
  try {
    const currentDate = new Date().toLocaleString()

    return {
      model: getModel(model),
      // The main system prompt combined with the current date/time
      system: `${SYSTEM_PROMPT}\nCurrent date and time: ${currentDate}`,
      messages,
      tools: {
        search: searchTool,
        retrieve: retrieveTool,
        videoSearch: videoSearchTool
      },
      // Conditionally activate tools based on searchMode flag
      experimental_activeTools: searchMode
        ? ['search', 'retrieve', 'videoSearch']
        : [],
      maxSteps: searchMode ? 5 : 1, // Limit steps if search is disabled
      experimental_transform: smoothStream({ chunking: 'word' })
    }
  } catch (error) {
    console.error('Error in chatResearcher:', error)
    throw error
  }
}
```

**Explanation of Prompt(s):**

*   **`SYSTEM_PROMPT`:** This is the main system prompt for the primary chat agent when operating with native tool-calling capabilities enabled. It defines the AI as a helpful assistant with access to specific tools (`search`, `retrieve`, `video_search`). It provides detailed instructions on *when* and *how* to use these tools, how to analyze the results, the mandatory citation format (`[number](url)`), how to handle irrelevant results (fall back to general knowledge), the expected response format (markdown, headings), and a crucial constraint for the `retrieve` tool (only use it with URLs explicitly provided by the user, not URLs found in search results).
*   **Combined `system` prompt in `researcher` function:** Similar to the manual researcher, the current date/time is appended to the main `SYSTEM_PROMPT` before being passed to the `streamText` function, providing temporal context.

---

**4. File: `lib/streaming/tool-execution.ts`**

```typescript
import {
    CoreMessage,
    DataStreamWriter,
    generateId,
    generateText,
    JSONValue
} from 'ai'
import { z } from 'zod'
import { searchSchema } from '../schema/search' // Ensure this uses the updated optional schema
import { search } from '../tools/search'
import { ExtendedCoreMessage } from '../types'
import { getModel } from '../utils/registry'
import { parseToolCallXml } from './parse-tool-call'

interface ToolExecutionResult {
  toolCallDataAnnotation: ExtendedCoreMessage | null
  toolCallMessages: CoreMessage[]
}

export async function executeToolCall(
  coreMessages: CoreMessage[],
  dataStream: DataStreamWriter,
  model: string, // This is the model ID string for the tool-calling model
  searchMode: boolean
): Promise<ToolExecutionResult> {
  // If search mode is disabled, return empty tool call
  if (!searchMode) {
    console.log("executeToolCall: Search mode disabled, skipping tool call generation.");
    return { toolCallDataAnnotation: null, toolCallMessages: [] }
  }

  // Convert Zod schema to string representation for the prompt
  // Uses the updated searchSchema with optional fields
  const searchSchemaString = Object.entries(searchSchema.shape)
    .map(([key, value]) => {
      // Access description from the Zod object itself
      const description = (value as z.ZodTypeAny).description ?? 'No description';
      // Check if the type is optional
      const isOptional = value instanceof z.ZodOptional || value._def.typeName === 'ZodOptional';
      return `- ${key}${isOptional ? ' (optional)' : ''}: ${description}`
    })
    .join('\n')

  // Determine default max results based on model provider if needed
  const defaultMaxResults = model?.includes('ollama') ? 5 : 20 // Example default

  // --- START OF PROMPT ---
  const systemPromptForToolSelection = `You are an intelligent assistant that analyzes conversations to select the most appropriate tools and their parameters.
            You excel at understanding context to determine when and how to use available tools, including crafting effective search queries.
            Current date: ${new Date().toISOString().split('T')[0]}

            Do not include any other text in your response.
            Respond ONLY in XML format with the following structure:
            <tool_call>
              <tool>tool_name</tool>
              <parameters>
                <query>search query text</query>
                <max_results>number - ${defaultMaxResults} by default</max_results>
                <search_depth>basic or advanced</search_depth>
                <include_domains>domain1.com,domain2.org</include_domains>
                <exclude_domains>domain3.net,domain4.edu</exclude_domains>
              </parameters>
            </tool_call>

            Available tools: search

            Search parameters:
            ${searchSchemaString}

            If you don't need a tool, respond with <tool_call><tool></tool></tool_call>`
  // --- END OF PROMPT ---

  console.log("executeToolCall: Generating tool selection with model:", model);
  // Generate tool selection using XML format
  const toolSelectionResponse = await generateText({
    model: getModel(model), // Get the actual model instance
    system: systemPromptForToolSelection,
    messages: coreMessages // Pass the conversation history
  })
  console.log("executeToolCall: Raw tool selection response:", toolSelectionResponse.text);


  // Parse the tool selection XML using the (updated) search schema
  const toolCall = parseToolCallXml(toolSelectionResponse.text, searchSchema)
  console.log("executeToolCall: Parsed tool call:", toolCall);


  // If no valid tool is selected, return
  if (!toolCall || !toolCall.tool || toolCall.tool !== 'search') {
     console.log("executeToolCall: No valid 'search' tool selected or needed.");
    return { toolCallDataAnnotation: null, toolCallMessages: [] }
  }

  // --- Execute the selected tool (Search) ---

  const toolCallId = `call_${generateId()}`;
  const toolCallAnnotation = {
    type: 'tool_call',
    data: {
      state: 'call', // Indicate the tool call is starting
      toolCallId: toolCallId,
      toolName: toolCall.tool,
      // Stringify parameters for the data stream annotation
      args: JSON.stringify(toolCall.parameters ?? {})
    }
  }
  // Write the initial "calling tool" state to the data stream
  dataStream.writeData(toolCallAnnotation);
  console.log("executeToolCall: Wrote 'call' state to data stream:", toolCallAnnotation.data);


  // Execute the search tool function with parsed/defaulted parameters
  let searchResults;
  try {
    searchResults = await search(
      toolCall.parameters?.query ?? '', // Default query if somehow missing
      toolCall.parameters?.max_results, // Pass undefined if optional and not provided
      toolCall.parameters?.search_depth, // Pass undefined if optional and not provided
      toolCall.parameters?.include_domains, // Pass undefined if optional and not provided
      toolCall.parameters?.exclude_domains // Pass undefined if optional and not provided
    );
    console.log("executeToolCall: Search tool executed successfully.");
  } catch (error) {
     console.error("executeToolCall: Error executing search tool:", error);
     searchResults = { error: `Failed to execute search: ${error instanceof Error ? error.message : String(error)}` }; // Provide error feedback
  }


  // Update the annotation with the result state
  const updatedToolCallAnnotation = {
    ...toolCallAnnotation,
    data: {
      ...toolCallAnnotation.data,
      result: JSON.stringify(searchResults), // Stringify the actual search results (or error)
      state: 'result' // Indicate the tool call has finished
    }
  }
  // Write the final "result" state to the data stream
  dataStream.writeMessageAnnotation(updatedToolCallAnnotation);
   console.log("executeToolCall: Wrote 'result' state to data stream.");


  // Prepare messages to be added back to the conversation history for the main LLM
  const toolCallDataAnnotation: ExtendedCoreMessage = {
    role: 'data', // Use 'data' role for annotations
    content: {
      type: 'tool_call',
      data: updatedToolCallAnnotation.data // Include the full data with state and result
    } as JSONValue
  }

  // Create messages representing the tool call and its result for the next LLM turn
  const toolCallMessages: CoreMessage[] = [
    // Maybe include the assistant's thought process/decision to call the tool if available?
    // { role: 'assistant', content: toolSelectionResponse.text }, // Could include the raw XML call
    {
      role: 'tool', // Use the standard 'tool' role for results
      content: [
          {
              type: 'tool-result',
              toolCallId: toolCallId,
              toolName: 'search',
              result: searchResults, // Pass the actual result object/error
          }
      ]
    }
    // Removed the generic "Now answer the user question." message,
    // as the main researcher prompt already guides the LLM to use tool results.
  ]

  return { toolCallDataAnnotation, toolCallMessages }
}
```

**Explanation of Prompt(s):**

*   **`systemPromptForToolSelection`:** This prompt is highly specialized. It instructs an LLM (specifically the one designated for tool calls, which might be different from the main chat LLM) to act as an assistant focused *only* on deciding *if* a tool should be used based on the conversation and *what parameters* to use.
    *   It explicitly tells the LLM to respond *only* in a specific XML format (`<tool_call>...`).
    *   It lists the available tools (currently only `search`).
    *   It provides the schema definition (`searchSchemaString`) for the `search` tool's parameters, including descriptions and noting which are optional (based on the updated schema).
    *   It instructs the LLM on how to respond if no tool is needed (`<tool_call><tool></tool></tool_call>`).
    *   This prompt's goal is *not* to answer the user's question but solely to generate the structured data needed to call the correct tool function (`search`) with appropriate arguments.

---

These four files contain the core instructional prompts guiding the different LLM interactions within your Morphic application's architecture.