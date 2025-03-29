### isReasoningModels

---

**1. `public/config/models.json`**

```json
{
  "models": [
    {
      "id": "o3-mini",
      "name": "o3 mini",
      "provider": "OpenAI",
      "providerId": "openai",
      "enabled": true,
      "toolCallType": "native"
    },
    {
      "id": "gpt-4o",
      "name": "GPT-4o",
      "provider": "OpenAI",
      "providerId": "openai",
      "enabled": true,
      "toolCallType": "native"
    },
    {
      "id": "gpt-4o-mini",
      "name": "GPT-4o mini",
      "provider": "OpenAI",
      "providerId": "openai",
      "enabled": true,
      "toolCallType": "native"
    },
    {
      "id": "claude-3-7-sonnet-20250219",
      "name": "Claude 3.7 Sonnet",
      "provider": "Anthropic",
      "providerId": "anthropic",
      "enabled": true,
      "toolCallType": "native"
    },
    {
      "id": "claude-3-5-sonnet-latest",
      "name": "Claude 3.5 Sonnet",
      "provider": "Anthropic",
      "providerId": "anthropic",
      "enabled": true,
      "toolCallType": "native"
    },
    {
      "id": "claude-3-5-haiku-20241022",
      "name": "Claude 3.5 Haiku",
      "provider": "Anthropic",
      "providerId": "anthropic",
      "enabled": true,
      "toolCallType": "native"
    },
    {
      "id": "<AZURE_DEPLOYMENT_NAME>",
      "name": "<AZURE_DEPLOYMENT_NAME>",
      "provider": "Azure",
      "providerId": "azure",
      "enabled": true,
      "toolCallType": "native"
    },
    {
      "id": "gemini-2.0-flash",
      "name": "Gemini 2.0 Flash",
      "provider": "Google Generative AI",
      "providerId": "google",
      "enabled": true,
      "toolCallType": "manual"
    },
    {
      "id": "gemini-2.0-flash-thinking-exp-01-21",
      "name": "Gemini 2.0 Flash Thinking (Exp)",
      "provider": "Google Generative AI",
      "providerId": "google",
      "enabled": true,
      "toolCallType": "manual",
      "toolCallModel": "gemini-2.0-flash"
    },
    // --- ADDED NEW GEMINI MODEL ---
    {
      "id": "gemini-2.5-pro-exp-03-25",
      "name": "Gemini 2.5 Pro (Exp)",
      "provider": "Google Generative AI",
      "providerId": "google",
      "enabled": true,
      "toolCallType": "manual",
      "toolCallModel": "gemini-2.0-flash" // Fallback for tool calls if needed
    },
    // --- END ADDED NEW GEMINI MODEL ---
    {
      "id": "deepseek/deepseek-r1",
      "name": "DeepSeek R1",
      "provider": "OpenRouter",
      "providerId": "openrouter",
      "enabled": true,
      "toolCallType": "manual"
    },
    {
      "id": "deepseek-reasoner",
      "name": "DeepSeek R1",
      "provider": "DeepSeek",
      "providerId": "deepseek",
      "enabled": true,
      "toolCallType": "manual",
      "toolCallModel": "deepseek-chat"
    },
    {
      "id": "deepseek-chat",
      "name": "DeepSeek V3",
      "provider": "DeepSeek",
      "providerId": "deepseek",
      "enabled": true,
      "toolCallType": "manual"
    },
    {
      "id": "deepseek-r1-distill-llama-70b",
      "name": "DeepSeek R1 Distill Llama 70B",
      "provider": "Groq",
      "providerId": "groq",
      "enabled": true,
      "toolCallType": "manual",
      "toolCallModel": "llama-3.1-8b-instant"
    },
    {
      "id": "deepseek-r1",
      "name": "DeepSeek R1",
      "provider": "Ollama",
      "providerId": "ollama",
      "enabled": true,
      "toolCallType": "manual",
      "toolCallModel": "phi4"
    },
    {
      "id": "<OLLAMA_MODEL_ID>",
      "name": "<OLLAMA_MODEL_NAME>",
      "provider": "Ollama",
      "providerId": "ollama",
      "enabled": true,
      "toolCallType": "manual",
      "toolCallModel": "<OLLAMA_MODEL_ID>"
    },
    {
      "id": "grok-2-1212",
      "name": "Grok 2",
      "provider": "xAI",
      "providerId": "xai",
      "enabled": true,
      "toolCallType": "native"
    },
    {
      "id": "grok-2-vision-1212",
      "name": "Grok 2 Vision",
      "provider": "xAI",
      "providerId": "xai",
      "enabled": true,
      "toolCallType": "native"
    },
    {
      "id": "<OPENAI_COMPATIBLE_MODEL>",
      "name": "<OPENAI_COMPATIBLE_MODEL>",
      "provider": "OpenAI Compatible",
      "providerId": "openai-compatible",
      "enabled": true,
      "toolCallType": "native"
    }
  ]
}
```

---

**2. `lib/config/default-models.json`**

```json
{
  "models": [
    {
      "id": "o3-mini",
      "name": "o3 mini",
      "provider": "OpenAI",
      "providerId": "openai",
      "enabled": true,
      "toolCallType": "native"
    },
    {
      "id": "gpt-4o",
      "name": "GPT-4o",
      "provider": "OpenAI",
      "providerId": "openai",
      "enabled": true,
      "toolCallType": "native"
    },
    {
      "id": "gpt-4o-mini",
      "name": "GPT-4o mini",
      "provider": "OpenAI",
      "providerId": "openai",
      "enabled": true,
      "toolCallType": "native"
    },
    {
      "id": "claude-3-7-sonnet-20250219",
      "name": "Claude 3.7 Sonnet",
      "provider": "Anthropic",
      "providerId": "anthropic",
      "enabled": true,
      "toolCallType": "native"
    },
    {
      "id": "claude-3-5-sonnet-latest",
      "name": "Claude 3.5 Sonnet",
      "provider": "Anthropic",
      "providerId": "anthropic",
      "enabled": true,
      "toolCallType": "native"
    },
    {
      "id": "claude-3-5-haiku-20241022",
      "name": "Claude 3.5 Haiku",
      "provider": "Anthropic",
      "providerId": "anthropic",
      "enabled": true,
      "toolCallType": "native"
    },
    {
      "id": "<AZURE_DEPLOYMENT_NAME>",
      "name": "<AZURE_DEPLOYMENT_NAME>",
      "provider": "Azure",
      "providerId": "azure",
      "enabled": true,
      "toolCallType": "native"
    },
    {
      "id": "gemini-2.0-flash",
      "name": "Gemini 2.0 Flash",
      "provider": "Google Generative AI",
      "providerId": "google",
      "enabled": true,
      "toolCallType": "manual"
    },
    {
      "id": "gemini-2.0-flash-thinking-exp-01-21",
      "name": "Gemini 2.0 Flash Thinking (Exp)",
      "provider": "Google Generative AI",
      "providerId": "google",
      "enabled": true,
      "toolCallType": "manual",
      "toolCallModel": "gemini-2.0-flash"
    },
    // --- ADDED NEW GEMINI MODEL ---
    {
      "id": "gemini-2.5-pro-exp-03-25",
      "name": "Gemini 2.5 Pro (Exp)",
      "provider": "Google Generative AI",
      "providerId": "google",
      "enabled": true,
      "toolCallType": "manual",
      "toolCallModel": "gemini-2.0-flash" // Fallback for tool calls if needed
    },
    // --- END ADDED NEW GEMINI MODEL ---
    {
      "id": "deepseek/deepseek-r1",
      "name": "DeepSeek R1",
      "provider": "OpenRouter",
      "providerId": "openrouter",
      "enabled": true,
      "toolCallType": "manual"
    },
    {
      "id": "deepseek-reasoner",
      "name": "DeepSeek R1",
      "provider": "DeepSeek",
      "providerId": "deepseek",
      "enabled": true,
      "toolCallType": "manual",
      "toolCallModel": "deepseek-chat"
    },
    {
      "id": "deepseek-chat",
      "name": "DeepSeek V3",
      "provider": "DeepSeek",
      "providerId": "deepseek",
      "enabled": true,
      "toolCallType": "manual"
    },
    {
      "id": "deepseek-r1-distill-llama-70b",
      "name": "DeepSeek R1 Distill Llama 70B",
      "provider": "Groq",
      "providerId": "groq",
      "enabled": true,
      "toolCallType": "manual",
      "toolCallModel": "llama-3.1-8b-instant"
    },
    {
      "id": "deepseek-r1",
      "name": "DeepSeek R1",
      "provider": "Ollama",
      "providerId": "ollama",
      "enabled": true,
      "toolCallType": "manual",
      "toolCallModel": "phi4"
    },
    {
      "id": "<OLLAMA_MODEL_ID>",
      "name": "<OLLAMA_MODEL_NAME>",
      "provider": "Ollama",
      "providerId": "ollama",
      "enabled": true,
      "toolCallType": "manual",
      "toolCallModel": "<OLLAMA_MODEL_ID>"
    },
    {
      "id": "grok-2-1212",
      "name": "Grok 2",
      "provider": "xAI",
      "providerId": "xai",
      "enabled": true,
      "toolCallType": "native"
    },
    {
      "id": "grok-2-vision-1212",
      "name": "Grok 2 Vision",
      "provider": "xAI",
      "providerId": "xai",
      "enabled": true,
      "toolCallType": "native"
    },
    {
      "id": "<OPENAI_COMPATIBLE_MODEL>",
      "name": "<OPENAI_COMPATIBLE_MODEL>",
      "provider": "OpenAI Compatible",
      "providerId": "openai-compatible",
      "enabled": true,
      "toolCallType": "native"
    }
  ]
}
```

---

**3. `lib/utils/registry.ts`**

```typescript
import { anthropic } from '@ai-sdk/anthropic'
import { createAzure } from '@ai-sdk/azure'
import { deepseek } from '@ai-sdk/deepseek'
import { google } from '@ai-sdk/google'
import { groq } from '@ai-sdk/groq'
import { createOpenAI, openai } from '@ai-sdk/openai'
import { xai } from '@ai-sdk/xai'
import {
  experimental_createProviderRegistry as createProviderRegistry,
  extractReasoningMiddleware,
  wrapLanguageModel
} from 'ai'
import { createOllama } from 'ollama-ai-provider'

export const registry = createProviderRegistry({
  openai,
  anthropic,
  google,
  groq,
  ollama: createOllama({
    baseURL: `${process.env.OLLAMA_BASE_URL}/api`
  }),
  azure: createAzure({
    apiKey: process.env.AZURE_API_KEY,
    resourceName: process.env.AZURE_RESOURCE_NAME
  }),
  deepseek,
  'openai-compatible': createOpenAI({
    apiKey: process.env.OPENAI_COMPATIBLE_API_KEY,
    baseURL: process.env.OPENAI_COMPATIBLE_API_BASE_URL
  }),
  openrouter: createOpenAI({
    apiKey: process.env.OPENROUTER_API_KEY,
    baseURL: 'https://openrouter.ai/api/v1',
  }),
  xai
})

export function getModel(model: string) {
  const [provider, ...modelNameParts] = model.split(':') ?? []
  const modelName = modelNameParts.join(':')

  // Handle Ollama separately
  if (provider === 'ollama') {
    const ollama = createOllama({
      baseURL: `${process.env.OLLAMA_BASE_URL}/api`
    })
    // Apply reasoning middleware if model ID includes 'deepseek-r1'
    if (modelName.includes('deepseek-r1')) {
      return wrapLanguageModel({
        model: ollama(modelName),
        middleware: extractReasoningMiddleware({
          tagName: 'think'
        })
      })
    }
    // Default Ollama handling
    return ollama(modelName, {
      simulateStreaming: true
    })
  }

  // Get the base model from the registry
  const baseModel = registry.languageModel(model);

  // Apply reasoning middleware for specific models/providers
  // Note: We check if the model *is* a reasoning model using the dedicated function
  if (isReasoningModel(model)) {
     return wrapLanguageModel({
       model: baseModel,
       middleware: extractReasoningMiddleware({
         tagName: 'think' // Assuming 'think' is the desired tag for all reasoning models
       })
     })
  }

  // Return the base model if no specific middleware is needed
  return baseModel
}

export function isProviderEnabled(providerId: string): boolean {
  switch (providerId) {
    case 'openai':
      return !!process.env.OPENAI_API_KEY
    case 'anthropic':
      return !!process.env.ANTHROPIC_API_KEY
    case 'google':
      return !!process.env.GOOGLE_GENERATIVE_AI_API_KEY
    case 'groq':
      return !!process.env.GROQ_API_KEY
    case 'ollama':
      return !!process.env.OLLAMA_BASE_URL
    case 'azure':
      return !!process.env.AZURE_API_KEY && !!process.env.AZURE_RESOURCE_NAME
    case 'deepseek':
      return !!process.env.DEEPSEEK_API_KEY
    case 'openrouter':
       return !!process.env.OPENROUTER_API_KEY;
    case 'xai':
      return !!process.env.XAI_API_KEY
    case 'openai-compatible':
      return (
        !!process.env.OPENAI_COMPATIBLE_API_KEY &&
        !!process.env.OPENAI_COMPATIBLE_API_BASE_URL
      )
    default:
      return false
  }
}

export function getToolCallModel(model?: string) {
  const [provider, ...modelNameParts] = model?.split(':') ?? []
  const modelName = modelNameParts.join(':')
  switch (provider) {
    case 'deepseek':
      return getModel('deepseek:deepseek-chat')
    case 'groq':
      return getModel('groq:llama-3.1-8b-instant')
    case 'ollama':
      const ollamaModel =
        process.env.NEXT_PUBLIC_OLLAMA_TOOL_CALL_MODEL || modelName
      return getModel(`ollama:${ollamaModel}`)
    case 'google':
      // Use flash for all google tool calls as fallback
      return getModel('google:gemini-2.0-flash')
    case 'openrouter':
       // OpenRouter doesn't have a specific small model defined here,
       // so it falls through to the default OpenAI model.
       break; // Fall through to default
    default:
      // Default fallback (often OpenAI or another reliable provider)
      return getModel('openai:gpt-4o-mini')
  }
  // Ensure a default return if the switch doesn't match explicitly
  return getModel('openai:gpt-4o-mini');
}


export function isToolCallSupported(model?: string) {
  const [provider, ...modelNameParts] = model?.split(':') ?? []
  const modelName = modelNameParts.join(':')

  // Explicitly list providers/models known *not* to support (or reliably support) tool calling
  if (provider === 'ollama' || provider === 'google') {
    return false
  }

  // OpenRouter depends on the underlying model via OpenAI compatibility.
  // Assume native tool calling works unless it's known not to (like DeepSeek R1).
  if (provider === 'openrouter') {
      return !modelName?.includes('deepseek/deepseek-r1');
  }

  // Direct DeepSeek provider checks
  if (provider === 'deepseek') {
      // DeepSeek R1/Reasoner are known not to support tools well
      return !modelName?.includes('deepseek-r1') && !modelName?.includes('deepseek-reasoner');
  }

  // Default assumption is native tool calling is supported for others
  // (like OpenAI, Anthropic, Azure, xAI, OpenAI-Compatible unless specified otherwise)
  return true;
}

// --- UPDATED isReasoningModel function ---
export function isReasoningModel(model: string): boolean {
  if (typeof model !== 'string') {
    return false
  }
  // Check includes the OpenRouter path for DeepSeek R1
  return (
    model.includes('deepseek-r1') || // Catches Ollama, Groq versions
    model.includes('deepseek/deepseek-r1') || // Catches OpenRouter version
    model.includes('deepseek-reasoner') || // Catches direct DeepSeek provider version
    model.includes('o3-mini') ||
    model.includes('gemini-2.5-pro-exp-03-25') || // Added Gemini 2.5 Pro
    model.includes('claude-3-7-sonnet') // Added Claude 3.7 Sonnet
  )
}
// --- END UPDATED isReasoningModel function ---
```

These changes add the `gemini-2.5-pro-exp-03-25` model to the available options and ensure that both it and `claude-3.7-sonnet` are recognized by the `isReasoningModel` function, which might influence how they are handled (e.g., applying the reasoning middleware).