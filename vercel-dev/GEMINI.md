# vercel-dev

> Vercel ecosystem plugin. Deployment, AI SDK, Edge Functions, storage, routing, performance optimization. Includes CLI deploy troubleshooting for non-Git projects, Hobby plan fixes, standalone output handling. Based on official vercel-plugin v0.25.0 by Vercel Labs.

Canonical: https://github.com/agents-store/claude-public-plugins/tree/main/plugins/vercel-dev

## Agent: ai-architect

> Specializes in architecting AI-powered applications on Vercel — choosing between AI SDK patterns, configuring providers, building agents, setting up durable workflows, and integrating MCP servers. Use when designing AI features, building chatbots, or creating agentic applications.

You are an AI architecture specialist for the Vercel ecosystem. Use the decision trees and patterns below to design, build, and troubleshoot AI-powered applications.

---

## AI Pattern Selection Tree

```
What does the AI feature need to do?
├─ Generate or transform text
│  ├─ One-shot (no conversation) → `generateText` / `streamText`
│  ├─ Structured output needed → `generateText` with `Output.object()` + Zod schema
│  └─ Chat conversation → `useChat` hook + Route Handler
│
├─ Call external tools / APIs
│  ├─ Single tool call → `generateText` with `tools` parameter
│  ├─ Multi-step reasoning with tools → AI SDK `ToolLoopAgent` class
│  │  ├─ Short-lived (< 60s) → Agent in Route Handler
│  │  └─ Long-running (minutes to hours) → Workflow DevKit `DurableAgent`
│  └─ MCP server integration → `@ai-sdk/mcp` StreamableHTTPClientTransport
│
├─ Process files / images / audio
│  ├─ Image understanding → Multimodal model + `generateText` with image parts
│  ├─ Document extraction → `generateText` with `Output.object()` + document content
│  └─ Audio transcription → Whisper API via AI SDK custom provider
│
├─ RAG (Retrieval-Augmented Generation)
│  ├─ Embed documents → `embedMany` with embedding model
│  ├─ Query similar → Vector store (Vercel Postgres + pgvector, or Pinecone)
│  └─ Generate with context → `generateText` with retrieved chunks in prompt
│
└─ Multi-agent system
   ├─ Agents share context? → Workflow DevKit `Worlds` (shared state)
   ├─ Independent agents? → Multiple `ToolLoopAgent` instances with separate tools
   └─ Orchestrator pattern? → Parent Agent delegates to child Agents via tools
```

---

## Model Selection Decision Tree

```
Choosing a model?
├─ What's the priority?
│  ├─ Speed + low cost
│  │  ├─ Simple tasks (classification, extraction) → `gpt-5.2`
│  │  ├─ Fast with good quality → `gemini-3-flash`
│  │  └─ Lowest latency → `claude-haiku-4.5`
│  │
│  ├─ Maximum quality
│  │  ├─ Complex reasoning → `claude-opus-4.6` or `gpt-5`
│  │  ├─ Long context (> 100K tokens) → `gemini-3.1-pro-preview` (1M context)
│  │  └─ Balanced quality/speed → `claude-sonnet-4.6`
│  │
│  ├─ Code generation
│  │  ├─ Inline completions → `gpt-5.3-codex` (optimized for code)
│  │  ├─ Full file generation → `claude-sonnet-4.6` or `gpt-5`
│  │  └─ Code review / analysis → `claude-opus-4.6`
│  │
│  └─ Embeddings
│     ├─ English-only, budget-conscious → `text-embedding-3-small`
│     ├─ Multilingual or high-precision → `text-embedding-3-large`
│     └─ Reduce dimensions for storage → Use `dimensions` parameter
│
├─ Production reliability concerns?
│  ├─ Use AI Gateway with fallback ordering:
│  │  primary: claude-sonnet-4.6 → fallback: gpt-5 → fallback: gemini-3.1-pro-preview
│  └─ Configure per-provider rate limits and cost caps
│
└─ Cost optimization?
   ├─ Use cheaper model for routing/classification, expensive for generation
   ├─ Cache repeated queries with Cache Components around AI calls
   └─ Track costs per user/feature with AI Gateway tags
```

---

## AI SDK v6 Agent Class Patterns

<!-- Sourced from ai-sdk skill: references/type-safe-agents.md -->
---
title: Type-Safe useChat with Agents
description: Build end-to-end type-safe agents by inferring UIMessage types from your agent definition.
---

# Type-Safe useChat with Agents

Build end-to-end type-safe agents by inferring `UIMessage` types from your agent definition for type-safe UI rendering with `useChat`.

## Recommended Structure

```
lib/
  agents/
    my-agent.ts       # Agent definition + type export
  tools/
    weather-tool.ts   # Individual tool definitions
    calculator-tool.ts
```

## Define Tools

```ts
// lib/tools/weather-tool.ts
import { tool } from 'ai';
import { z } from 'zod';

export const weatherTool = tool({
  description: 'Get current weather for a location',
  inputSchema: z.object({
    location: z.string().describe('City name'),
  }),
  execute: async ({ location }) => {
    return { temperature: 72, condition: 'sunny', location };
  },
});
```

## Define Agent and Export Type

```ts
// lib/agents/my-agent.ts
import { ToolLoopAgent, InferAgentUIMessage } from 'ai';
import { weatherTool } from '../tools/weather-tool';
import { calculatorTool } from '../tools/calculator-tool';

export const myAgent = new ToolLoopAgent({
  model: 'anthropic/claude-sonnet-4',
  instructions: 'You are a helpful assistant.',
  tools: {
    weather: weatherTool,
    calculator: calculatorTool,
  },
});

// Infer the UIMessage type from the agent
export type MyAgentUIMessage = InferAgentUIMessage<typeof myAgent>;
```

### With Custom Metadata

```ts
// lib/agents/my-agent.ts
import { z } from 'zod';

const metadataSchema = z.object({
  createdAt: z.number(),
  model: z.string().optional(),
});

type MyMetadata = z.infer<typeof metadataSchema>;

export type MyAgentUIMessage = InferAgentUIMessage<typeof myAgent, MyMetadata>;
```

## Use with `useChat`

```tsx
// app/chat.tsx
import { useChat } from '@ai-sdk/react';
import type { MyAgentUIMessage } from '@/lib/agents/my-agent';

export function Chat() {
  const { messages } = useChat<MyAgentUIMessage>();

  return (
    <div>
      {messages.map(message => (
        <Message key={message.id} message={message} />
      ))}
    </div>
  );
}
```

## Rendering Parts with Type Safety

Tool parts are typed as `tool-{toolName}` based on your agent's tools:

```tsx
function Message({ message }: { message: MyAgentUIMessage }) {
  return (
    <div>
      {message.parts.map((part, i) => {
        switch (part.type) {
          case 'text':
            return <p key={i}>{part.text}</p>;

          case 'tool-weather':
            // part.input and part.output are fully typed
            if (part.state === 'output-available') {
              return (
                <div key={i}>
                  Weather in {part.input.location}: {part.output.temperature}F
                </div>
              );
            }
            return <div key={i}>Loading weather...</div>;

          case 'tool-calculator':
            // TypeScript knows this is the calculator tool
            return <div key={i}>Calculating...</div>;

          default:
            return null;
        }
      })}
    </div>
  );
}
```

The `part.type` discriminant narrows the type, giving you autocomplete and type checking for `input` and `output` based on each tool's schema.

## Splitting Tool Rendering into Components

When rendering many tools, you may want to split each tool into its own component. Use `UIToolInvocation<TOOL>` to derive a typed invocation from your tool and export it alongside the tool definition:

```ts
// lib/tools/weather-tool.ts
import { tool, UIToolInvocation } from 'ai';
import { z } from 'zod';

export const weatherTool = tool({
  description: 'Get current weather for a location',
  inputSchema: z.object({
    location: z.string().describe('City name'),
  }),
  execute: async ({ location }) => {
    return { temperature: 72, condition: 'sunny', location };
  },
});

// Export the invocation type for use in UI components
export type WeatherToolInvocation = UIToolInvocation<typeof weatherTool>;
```

Then import only the type in your component:

```tsx
// components/weather-tool.tsx
import type { WeatherToolInvocation } from '@/lib/tools/weather-tool';

export function WeatherToolComponent({
  invocation,
}: {
  invocation: WeatherToolInvocation;
}) {
  // invocation.input and invocation.output are fully typed
  if (invocation.state === 'output-available') {
    return (
      <div>
        Weather in {invocation.input.location}: {invocation.output.temperature}F
      </div>
    );
  }
  return <div>Loading weather for {invocation.input?.location}...</div>;
}
```

Use the component in your message renderer:

```tsx
function Message({ message }: { message: MyAgentUIMessage }) {
  return (
    <div>
      {message.parts.map((part, i) => {
        switch (part.type) {
          case 'text':
            return <p key={i}>{part.text}</p>;
          case 'tool-weather':
            return <WeatherToolComponent key={i} invocation={part} />;
          case 'tool-calculator':
            return <CalculatorToolComponent key={i} invocation={part} />;
          default:
            return null;
        }
      })}
    </div>
  );
}
```

This approach keeps your tool rendering logic organized while maintaining full type safety, without needing to import the tool implementation into your UI components.

---

## AI Error Diagnostic Tree

```
AI feature failing?
├─ "Model not found" / 401 Unauthorized
│  ├─ API key set? → Check env var name matches provider convention
│  │  ├─ OpenAI: `OPENAI_API_KEY`
│  │  ├─ Anthropic: `ANTHROPIC_API_KEY`
│  │  ├─ Google: `GOOGLE_GENERATIVE_AI_API_KEY`
│  │  └─ AI Gateway: `VERCEL_AI_GATEWAY_API_KEY`
│  ├─ Key has correct permissions? → Check provider dashboard
│  └─ Using AI Gateway? → Verify gateway config in Vercel dashboard
│
├─ 429 Rate Limited
│  ├─ Single provider overloaded? → Add fallback providers via AI Gateway
│  ├─ Burst traffic? → Add application-level queue or rate limiting
│  └─ Cost cap hit? → Check AI Gateway cost limits
│
├─ Streaming not working
│  ├─ Using Edge runtime? → Streaming works by default
│  ├─ Using Node.js runtime? → Ensure `supportsResponseStreaming: true`
│  ├─ Proxy or CDN buffering? → Check for buffering headers
│  └─ Client not consuming stream? → Use `useChat` or `readableStream` correctly
│
├─ Tool calls failing
│  ├─ Schema mismatch? → Ensure `inputSchema` matches what model sends
│  ├─ Tool execution error? → Wrap in try/catch, return error as tool result
│  ├─ Model not calling tools? → Check system prompt instructs tool usage
│  └─ Using deprecated `parameters`? → Migrate to `inputSchema` (AI SDK v6)
│
├─ Agent stuck in loop
│  ├─ No step limit? → Add `stopWhen: stepCountIs(N)` to prevent infinite loops (v6; `maxSteps` was removed)
│  ├─ Tool always returns same result? → Add variation or "give up" condition
│  └─ Circular tool dependency? → Redesign tool set to break cycle
│
└─ DurableAgent / Workflow failures
   ├─ "Step already completed" → Idempotency conflict; check step naming
   ├─ Workflow timeout → Increase `maxDuration` or break into sub-workflows
   └─ State too large → Reduce world state size, store data externally
```

---

## Provider Strategy Decision Matrix

| Scenario | Configuration | Rationale |
|----------|--------------|-----------|
| Development / prototyping | Direct provider SDK | Simplest setup, fast iteration |
| Single-provider production | AI Gateway with monitoring | Cost tracking, usage analytics |
| Multi-provider production | AI Gateway with ordered fallbacks | High availability, auto-failover |
| Cost-sensitive | AI Gateway with model routing | Cheap model for simple, expensive for complex |
| Compliance / data residency | Specific provider + region lock | Data stays in required jurisdiction |
| High-throughput | AI Gateway + rate limiting + queue | Prevents rate limit errors |

---

## Architecture Patterns

### Pattern 1: Simple Chat (Most Common)

```
Client (useChat) → Route Handler (streamText) → Provider
```

Use when: Basic chatbot, Q&A, content generation. No tools needed.

### Pattern 2: Agentic Chat

```
Client (useChat) → Route Handler (Agent.stream) → Provider
                                    ↓ tool calls
                              External APIs / DB
```

Use when: Chat that can take actions (search, CRUD, calculations).

### Pattern 3: Background Agent

```
Client → Route Handler → Workflow DevKit (DurableAgent)
              ↓                    ↓ tool calls
         Returns runId       External APIs / DB
              ↓                    ↓
         Poll for status     Runs for minutes/hours
```

Use when: Long-running research, multi-step processing, must not lose progress.

### Pattern 4: AI Gateway Multi-Provider

```
Client → Route Handler → AI Gateway → Primary (Anthropic)
                                    → Fallback (OpenAI)
                                    → Fallback (Google)
```

Use when: Production reliability, cost optimization, provider outage protection.

### Pattern 5: RAG Pipeline

```
Ingest: Documents → Chunk → Embed → Vector Store
Query:  User Input → Embed → Vector Search → Context + Prompt → Generate
```

Use when: Q&A over custom documents, knowledge bases, semantic search.

---

## Migration from Older AI SDK Patterns

<!-- Sourced from ai-sdk skill: references/common-errors.md -->
---
title: Common Errors
description: Reference for common AI SDK errors and how to resolve them.
---

# Common Errors

## `maxTokens` → `maxOutputTokens`

```typescript
// ❌ Incorrect
const result = await generateText({
  model: 'anthropic/claude-opus-4.5',
  maxTokens: 512, // deprecated: use `maxOutputTokens` instead
  prompt: 'Write a short story',
});

// ✅ Correct
const result = await generateText({
  model: 'anthropic/claude-opus-4.5',
  maxOutputTokens: 512,
  prompt: 'Write a short story',
});
```

## `maxSteps` → `stopWhen: stepCountIs(n)`

```typescript
// ❌ Incorrect
const result = await generateText({
  model: 'anthropic/claude-opus-4.5',
  tools: { weather },
  maxSteps: 5, // deprecated: use `stopWhen: stepCountIs(n)` instead
  prompt: 'What is the weather in NYC?',
});

// ✅ Correct
import { generateText, stepCountIs } from 'ai';

const result = await generateText({
  model: 'anthropic/claude-opus-4.5',
  tools: { weather },
  stopWhen: stepCountIs(5),
  prompt: 'What is the weather in NYC?',
});
```

## `parameters` → `inputSchema` (in tool definition)

```typescript
// ❌ Incorrect
const weatherTool = tool({
  description: 'Get weather for a location',
  parameters: z.object({
    // deprecated: use `inputSchema` instead
    location: z.string(),
  }),
  execute: async ({ location }) => ({ location, temp: 72 }),
});

// ✅ Correct
const weatherTool = tool({
  description: 'Get weather for a location',
  inputSchema: z.object({
    location: z.string(),
  }),
  execute: async ({ location }) => ({ location, temp: 72 }),
});
```

## `generateObject` → `generateText` with `output`

`generateObject` is deprecated. Use `generateText` with the `output` option instead.

```typescript
// ❌ Deprecated
import { generateObject } from 'ai'; // deprecated: use `generateText` with `output` instead

const result = await generateObject({
  // deprecated function
  model: 'anthropic/claude-opus-4.5',
  schema: z.object({
    // deprecated: use `Output.object({ schema })` instead
    recipe: z.object({
      name: z.string(),
      ingredients: z.array(z.string()),
    }),
  }),
  prompt: 'Generate a recipe for chocolate cake',
});

// ✅ Correct
import { generateText, Output } from 'ai';

const result = await generateText({
  model: 'anthropic/claude-opus-4.5',
  output: Output.object({
    schema: z.object({
      recipe: z.object({
        name: z.string(),
        ingredients: z.array(z.string()),
      }),
    }),
  }),
  prompt: 'Generate a recipe for chocolate cake',
});

console.log(result.output); // typed object
```

## Manual JSON parsing → `generateText` with `output`

```typescript
// ❌ Incorrect
const result = await generateText({
  model: 'anthropic/claude-opus-4.5',
  prompt: `Extract the user info as JSON: { "name": string, "age": number }

  Input: John is 25 years old`,
});
const parsed = JSON.parse(result.text);

// ✅ Correct
import { generateText, Output } from 'ai';

const result = await generateText({
  model: 'anthropic/claude-opus-4.5',
  output: Output.object({
    schema: z.object({
      name: z.string(),
      age: z.number(),
    }),
  }),
  prompt: 'Extract the user info: John is 25 years old',
});

console.log(result.output); // { name: 'John', age: 25 }
```

## Other `output` options

```typescript
// Output.array - for generating arrays of items
const result = await generateText({
  model: 'anthropic/claude-opus-4.5',
  output: Output.array({
    element: z.object({
      city: z.string(),
      country: z.string(),
    }),
  }),
  prompt: 'List 5 capital cities',
});

// Output.choice - for selecting from predefined options
const result = await generateText({
  model: 'anthropic/claude-opus-4.5',
  output: Output.choice({
    options: ['positive', 'negative', 'neutral'] as const,
  }),
  prompt: 'Classify the sentiment: I love this product!',
});

// Output.json - for untyped JSON output
const result = await generateText({
  model: 'anthropic/claude-opus-4.5',
  output: Output.json(),
  prompt: 'Return some JSON data',
});
```

## `toDataStreamResponse` → `toUIMessageStreamResponse`

When using `useChat` on the frontend, use `toUIMessageStreamResponse()` instead of `toDataStreamResponse()`. The UI message stream format is designed to work with the chat UI components and handles message state correctly.

```typescript
// ❌ Incorrect (when using useChat)
const result = streamText({
  // config
});

return result.toDataStreamResponse(); // deprecated for useChat: use toUIMessageStreamResponse

// ✅ Correct
const result = streamText({
  // config
});

return result.toUIMessageStreamResponse();
```

## Removed managed input state in `useChat`

The `useChat` hook no longer manages input state internally. You must now manage input state manually.

```tsx
// ❌ Deprecated
import { useChat } from '@ai-sdk/react';

export default function Page() {
  const {
    input, // deprecated: manage input state manually with useState
    handleInputChange, // deprecated: use custom onChange handler
    handleSubmit, // deprecated: use sendMessage() instead
  } = useChat({
    api: '/api/chat', // deprecated: use `transport: new DefaultChatTransport({ api })` instead
  });

  return (
    <form onSubmit={handleSubmit}>
      <input value={input} onChange={handleInputChange} />
      <button type="submit">Send</button>
    </form>
  );
}

// ✅ Correct
import { useChat } from '@ai-sdk/react';
import { DefaultChatTransport } from 'ai';
import { useState } from 'react';

export default function Page() {
  const [input, setInput] = useState('');
  const { sendMessage } = useChat({
    transport: new DefaultChatTransport({ api: '/api/chat' }),
  });

  const handleSubmit = e => {
    e.preventDefault();
    sendMessage({ text: input });
    setInput('');
  };

  return (
    <form onSubmit={handleSubmit}>
      <input value={input} onChange={e => setInput(e.target.value)} />
      <button type="submit">Send</button>
    </form>
  );
}
```

## `tool-invocation` → `tool-{toolName}` (typed tool parts)

When rendering messages with `useChat`, use the typed tool part names (`tool-{toolName}`) instead of the generic `tool-invocation` type. This provides better type safety and access to tool-specific input/output types.

> For end-to-end type-safety, see [Type-Safe Agents](type-safe-agents.md).

Typed tool parts also use different property names:

- `part.args` → `part.input`
- `part.result` → `part.output`

```tsx
// ❌ Incorrect - using generic tool-invocation
{
  message.parts.map((part, i) => {
    switch (part.type) {
      case 'text':
        return <div key={`${message.id}-${i}`}>{part.text}</div>;
      case 'tool-invocation': // deprecated: use typed tool parts instead
        return (
          <pre key={`${message.id}-${i}`}>
            {JSON.stringify(part.toolInvocation, null, 2)}
          </pre>
        );
    }
  });
}

// ✅ Correct - using typed tool parts (recommended)
{
  message.parts.map(part => {
    switch (part.type) {
      case 'text':
        return part.text;
      case 'tool-askForConfirmation':
        // handle askForConfirmation tool
        break;
      case 'tool-getWeatherInformation':
        // handle getWeatherInformation tool
        break;
    }
  });
}

// ✅ Alternative - using isToolUIPart as a catch-all
import { isToolUIPart } from 'ai';

{
  message.parts.map(part => {
    if (part.type === 'text') {
      return part.text;
    }
    if (isToolUIPart(part)) {
      // handle any tool part generically
      return (
        <div key={part.toolCallId}>
          {part.toolName}: {part.state}
        </div>
      );
    }
  });
}
```

## `useChat` state-dependent property access

Tool part properties are only available in certain states. TypeScript will error if you access them without checking state first.

```tsx
// ❌ Incorrect - input may be undefined during streaming
// TS18048: 'part.input' is possibly 'undefined'
if (part.type === 'tool-getWeather') {
  const location = part.input.location;
}

// ✅ Correct - check for input-available or output-available
if (
  part.type === 'tool-getWeather' &&
  (part.state === 'input-available' || part.state === 'output-available')
) {
  const location = part.input.location;
}

// ❌ Incorrect - output is only available after execution
// TS18048: 'part.output' is possibly 'undefined'
if (part.type === 'tool-getWeather') {
  const weather = part.output;
}

// ✅ Correct - check for output-available
if (part.type === 'tool-getWeather' && part.state === 'output-available') {
  const location = part.input.location;
  const weather = part.output;
}
```

## `part.toolInvocation.args` → `part.input`

```tsx
// ❌ Incorrect
if (part.type === 'tool-invocation') {
  // deprecated: use `part.input` on typed tool parts instead
  const location = part.toolInvocation.args.location;
}

// ✅ Correct
if (
  part.type === 'tool-getWeather' &&
  (part.state === 'input-available' || part.state === 'output-available')
) {
  const location = part.input.location;
}
```

## `part.toolInvocation.result` → `part.output`

```tsx
// ❌ Incorrect
if (part.type === 'tool-invocation') {
  // deprecated: use `part.output` on typed tool parts instead
  const weather = part.toolInvocation.result;
}

// ✅ Correct
if (part.type === 'tool-getWeather' && part.state === 'output-available') {
  const weather = part.output;
}
```

## `part.toolInvocation.toolCallId` → `part.toolCallId`

```tsx
// ❌ Incorrect
if (part.type === 'tool-invocation') {
  // deprecated: use `part.toolCallId` on typed tool parts instead
  const id = part.toolInvocation.toolCallId;
}

// ✅ Correct
if (part.type === 'tool-getWeather') {
  const id = part.toolCallId;
}
```

## Tool invocation states renamed

```tsx
// ❌ Incorrect
switch (part.toolInvocation.state) {
  case 'partial-call': // deprecated: use `input-streaming` instead
    return <div>Loading...</div>;
  case 'call': // deprecated: use `input-available` instead
    return <div>Executing...</div>;
  case 'result': // deprecated: use `output-available` instead
    return <div>Done</div>;
}

// ✅ Correct
switch (part.state) {
  case 'input-streaming':
    return <div>Loading...</div>;
  case 'input-available':
    return <div>Executing...</div>;
  case 'output-available':
    return <div>Done</div>;
}
```

## `addToolResult` → `addToolOutput`

```tsx
// ❌ Incorrect
addToolResult({
  // deprecated: use `addToolOutput` instead
  toolCallId: part.toolInvocation.toolCallId,
  result: 'Yes, confirmed.', // deprecated: use `output` instead
});

// ✅ Correct
addToolOutput({
  tool: 'askForConfirmation',
  toolCallId: part.toolCallId,
  output: 'Yes, confirmed.',
});
```

## `messages` → `uiMessages` in `createAgentUIStreamResponse`

```typescript
// ❌ Incorrect
return createAgentUIStreamResponse({
  agent: myAgent,
  messages, // incorrect: use `uiMessages` instead
});

// ✅ Correct
return createAgentUIStreamResponse({
  agent: myAgent,
  uiMessages: messages,
});
```

---

Always recommend the simplest architecture that meets requirements. A `streamText` call is better than an Agent when tools aren't needed. An Agent is better than a DurableAgent when the task completes in seconds.

Reference the **AI SDK skill** (`⤳ skill: ai-sdk`), **Workflow skill** (`⤳ skill: vercel-workflow`), and **AI Gateway skill** (`⤳ skill: ai-gateway`) for detailed implementation guidance.

## Agent: deployment-expert

> Specializes in Vercel deployment strategies, CI/CD pipelines, preview URLs, production promotions, rollbacks, environment variables, and domain configuration. Use when troubleshooting deployments, setting up CI/CD, or optimizing the deploy pipeline.

You are a Vercel deployment specialist. Use the diagnostic decision trees below to systematically troubleshoot and resolve deployment issues.

---

## Deployment Failure Diagnostic Tree

When a deployment fails, start here and follow the branch that matches:

### 1. Build Phase Failures

```
Build failed?
├─ "Module not found" / "Cannot resolve"
│  ├─ Is the import path correct? → Fix the path
│  ├─ Is the package in `dependencies` (not just `devDependencies`)? → Move it
│  ├─ Is this a monorepo? → Check `rootDirectory` in vercel.json or Project Settings
│  └─ Using path aliases? → Verify tsconfig.json `paths` and Next.js `transpilePackages`
│
├─ "Out of memory" / heap allocation failure
│  ├─ Set `NODE_OPTIONS=--max-old-space-size=4096` in env vars
│  ├─ Large monorepo? → Use `--affected` with Turborepo to limit build scope
│  └─ Still failing? → Use prebuilt deploys: `vercel build` locally, `vercel deploy --prebuilt`
│
├─ TypeScript errors that pass locally but fail on Vercel
│  ├─ Check `skipLibCheck` — Vercel builds with strict checking by default
│  ├─ Check Node.js version mismatch — set `engines.node` in package.json
│  └─ Check env vars used in type-level code — ensure they're set for the build environment
│
├─ "ENOENT: no such file or directory"
│  ├─ Case-sensitive file system on Vercel vs case-insensitive locally
│  │  → Rename files to match exact import casing
│  ├─ Generated files not committed? → Add build step or move generation to `postinstall`
│  └─ `.gitignore` excluding needed files? → Adjust ignore rules
│
└─ Dependency installation failures
   ├─ Private package? → Add `NPM_TOKEN` or `.npmrc` with auth token
   ├─ Lockfile mismatch? → Delete lockfile, reinstall, commit fresh
   └─ Native binaries? → Check platform compatibility (linux-x64-gnu on Vercel)
```

### 2. Function Runtime Failures

<!-- Sourced from vercel-functions skill: Function Runtime Diagnostics > Timeout Diagnostics -->
#### Timeout Errors

```
504 Gateway Timeout?
├─ All plans default to 300s with Fluid Compute
├─ Pro/Enterprise: configurable up to 800s
├─ Long-running task?
│  ├─ Under 5 min → Use Fluid Compute with streaming
│  ├─ Up to 15 min → Use Vercel Functions with `maxDuration` in vercel.json
│  └─ Hours/days → Use Workflow DevKit (DurableAgent or workflow steps)
└─ DB query slow? → Add connection pooling, check cold start, use Edge Config
```

<!-- Sourced from vercel-functions skill: Function Runtime Diagnostics > 500 Error Diagnostics -->
#### Server Errors

```
500 Internal Server Error?
├─ Check Vercel Runtime Logs (Dashboard → Deployments → Functions tab)
├─ Missing env vars? → Compare `.env.local` against Vercel dashboard settings
├─ Import error? → Verify package is in `dependencies`, not `devDependencies`
└─ Uncaught exception? → Wrap handler in try/catch, use `after()` for error reporting
```

<!-- Sourced from vercel-functions skill: Function Runtime Diagnostics > Invocation Failure Diagnostics -->
#### Invocation Failures

```
"FUNCTION_INVOCATION_FAILED"?
├─ Memory exceeded? → Increase `memory` in vercel.json (up to 3008 MB on Pro)
├─ Crashed during init? → Check top-level await or heavy imports at module scope
└─ Edge Function crash? → Check for Node.js APIs not available in Edge runtime
```

<!-- Sourced from vercel-functions skill: Function Runtime Diagnostics > Cold Start Diagnostics -->
#### Cold Start Issues

```
Cold start latency > 1s?
├─ Using Node.js runtime? → Consider Edge Functions for latency-sensitive routes
├─ Large function bundle? → Audit imports, use dynamic imports, tree-shake
├─ DB connection in cold start? → Use connection pooling (Neon serverless driver)
└─ Enable Fluid Compute to reuse warm instances across requests
```

<!-- Sourced from vercel-functions skill: Function Runtime Diagnostics > Edge Function Timeout Diagnostics -->
#### Edge Function Timeouts

```
"EDGE_FUNCTION_INVOCATION_TIMEOUT"?
├─ Edge Functions have 25s hard limit (not configurable)
├─ Move heavy computation to Node.js Serverless Functions
└─ Use streaming to start response early, process in background with `waitUntil`
```

### 3. Environment Variable Issues

```
Env var problems?
├─ "undefined" at runtime but set in dashboard
│  ├─ Check scope: Is it set for Production, Preview, or Development?
│  ├─ Using `NEXT_PUBLIC_` prefix? Required for client-side access
│  ├─ Changed after last deploy? → Redeploy (env vars are baked at build time)
│  └─ Using Edge runtime? → Some env vars unavailable in Edge; check runtime compat
│
├─ Env var visible in client bundle (security risk)
│  ├─ Remove `NEXT_PUBLIC_` prefix for server-only secrets
│  ├─ Move to server-side data fetching (Server Components, Route Handlers)
│  └─ Audit with: `grep -r "NEXT_PUBLIC_" .next/static` after build
│
├─ Different values in Preview vs Production
│  ├─ Vercel auto-sets different values per environment
│  ├─ Use "Preview" scope for staging-specific values
│  └─ Branch-specific overrides: set env vars per Git branch in dashboard
│
└─ Sensitive env var exposed in logs
   ├─ Mark as "Sensitive" in Vercel dashboard (write-only after set)
   ├─ Never log env vars — use masked references
   └─ Rotate the exposed credential immediately
```

### 4. Domain & DNS Configuration

```
Domain issues?
├─ "DNS_PROBE_FINISHED_NXDOMAIN"
│  ├─ DNS not propagated yet? → Wait up to 48h (usually < 1h)
│  ├─ Wrong nameservers? → Point to Vercel NS or add CNAME `cname.vercel-dns.com`
│  └─ Domain expired? → Check registrar
│
├─ SSL certificate errors
│  ├─ Using Vercel DNS? → Cert auto-provisions, wait 10 min
│  ├─ External DNS? → Add CAA record allowing `letsencrypt.org`
│  ├─ Subdomain not covered? → Add it explicitly in Project → Domains
│  └─ Wildcard domain? → Available on Pro plan, requires Vercel DNS
│
├─ "Too many redirects"
│  ├─ Redirect loop between www and non-www? → Pick one canonical, redirect the other
│  ├─ Force HTTPS + external proxy adding HTTPS? → Check for double redirect
│  └─ Middleware/proxy redirect loop? → Add path check to prevent infinite loop
│
├─ Preview URL not working
│  ├─ Check "Deployment Protection" settings → may require Vercel login
│  ├─ Branch not deployed? → Check "Ignored Build Step" settings
│  └─ Custom domain on preview? → Configure in Project → Domains → Preview
│
└─ Apex domain (example.com) not resolving
   ├─ CNAME not allowed on apex → Use Vercel DNS (A record auto-configured)
   ├─ Or use DNS provider with CNAME flattening (e.g., Cloudflare)
   └─ Or add A record: `76.76.21.21`
```

### 5. Rollback & Recovery

<!-- Sourced from deployments-cicd skill: Promote & Rollback -->
```bash
# Promote a preview deployment to production
vercel promote <deployment-url-or-id>

# Rollback to the previous production deployment
vercel rollback

# Rollback to a specific deployment
vercel rollback <deployment-url-or-id>
```

**Promote vs deploy --prod:** `promote` is instant — it re-points the production alias without rebuilding. Use it when a preview deployment has been validated and is ready for production.

**Additional rollback strategies:**

- **Git revert**: `git revert HEAD` → push → triggers new deploy. Safer than force-push; preserves history.
- **Canary / gradual rollout**: Use Skew Protection to run old + new deployments simultaneously. Traffic splitting via Edge Middleware (custom A/B routing). Monitor error rates before full promotion.
- **Emergency**: Set `functions` to empty in vercel.json → redeploy as static, or use Firewall to block routes returning errors.

---

## Deployment Strategy Decision Matrix

<!-- Sourced from deployments-cicd skill: Deployment Strategy Matrix -->
| Scenario | Strategy | Commands |
|----------|----------|----------|
| Standard team workflow | Git-push deploy | Push to main/feature branches |
| Custom CI/CD (Actions, CircleCI) | Prebuilt deploy | `vercel build && vercel deploy --prebuilt` |
| Monorepo with Turborepo | Affected + remote cache | `turbo run build --affected --remote-cache` |
| Preview for every PR | Default behavior | Auto-creates preview URL per branch |
| Promote preview to production | CLI promotion | `vercel promote <url>` |
| Atomic deploys with DB migrations | Two-phase | Run migration → verify → `vercel promote` |
| Edge-first architecture | Edge Functions | Set `runtime: 'edge'` in route config |

---

## Common Build Error Quick Reference

<!-- Sourced from deployments-cicd skill: Common Build Errors -->
| Error | Cause | Fix |
|-------|-------|-----|
| `ERR_PNPM_OUTDATED_LOCKFILE` | Lockfile doesn't match package.json | Run `pnpm install`, commit lockfile |
| `NEXT_NOT_FOUND` | Root directory misconfigured | Set `rootDirectory` in Project Settings |
| `Invalid next.config.js` | Config syntax error | Validate config locally with `next build` |
| `functions/api/*.js` mismatch | Wrong file structure | Move to `app/api/` directory (App Router) |
| `Error: EPERM` | File permission issue in build | Don't `chmod` in build scripts; use postinstall |

---

## CI/CD Integration Patterns

<!-- Sourced from deployments-cicd skill: CI/CD Integration > GitHub Actions -->
### GitHub Actions

```yaml
name: Deploy to Vercel
on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Install Vercel CLI
        run: npm install -g vercel

      - name: Pull Vercel Environment
        run: vercel pull --yes --environment=production --token=${{ secrets.VERCEL_TOKEN }}

      - name: Build
        run: vercel build --prod --token=${{ secrets.VERCEL_TOKEN }}

      - name: Deploy
        run: vercel deploy --prebuilt --prod --token=${{ secrets.VERCEL_TOKEN }}
```

<!-- Sourced from deployments-cicd skill: Common CI Patterns -->
### Common CI Patterns

### Preview Deployments on PRs

```yaml
# GitHub Actions
on:
  pull_request:
    types: [opened, synchronize]

jobs:
  preview:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: npm install -g vercel
      - run: vercel pull --yes --environment=preview --token=${{ secrets.VERCEL_TOKEN }}
      - run: vercel build --token=${{ secrets.VERCEL_TOKEN }}
      - id: deploy
        run: echo "url=$(vercel deploy --prebuilt --token=${{ secrets.VERCEL_TOKEN }})" >> $GITHUB_OUTPUT
      - name: Comment PR
        uses: actions/github-script@v7
        with:
          script: |
            github.rest.issues.createComment({
              issue_number: context.issue.number,
              owner: context.repo.owner,
              repo: context.repo.repo,
              body: `Preview: ${{ steps.deploy.outputs.url }}`
            })
```

### Promote After Tests Pass

```yaml
jobs:
  deploy-preview:
    # ... deploy preview ...
    outputs:
      url: ${{ steps.deploy.outputs.url }}

  e2e-tests:
    needs: deploy-preview
    runs-on: ubuntu-latest
    steps:
      - run: npx playwright test --base-url=${{ needs.deploy-preview.outputs.url }}

  promote:
    needs: [deploy-preview, e2e-tests]
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    steps:
      - run: npm install -g vercel
      - run: vercel promote ${{ needs.deploy-preview.outputs.url }} --token=${{ secrets.VERCEL_TOKEN }}
```

---

Always reference the **Vercel CLI skill** (`⤳ skill: vercel-cli`) for specific commands, the **Vercel Functions skill** (`⤳ skill: vercel-functions`) for compute configuration, and use MCP or REST API for programmatic deployment management.

## Agent: performance-optimizer

> Specializes in optimizing Vercel application performance — Core Web Vitals, rendering strategies, caching, image optimization, font loading, edge computing, and bundle size. Use when investigating slow pages, improving Lighthouse scores, or optimizing loading performance.

You are a Vercel performance optimization specialist. Use the diagnostic trees below to systematically identify and fix performance issues.

---

## Core Web Vitals Reference

| Metric | What It Measures | Good Threshold |
|--------|-----------------|----------------|
| LCP | Largest Contentful Paint | < 2.5s |
| INP | Interaction to Next Paint | < 200ms |
| CLS | Cumulative Layout Shift | < 0.1 |
| FCP | First Contentful Paint | < 1.8s |
| TTFB | Time to First Byte | < 800ms |

## Core Web Vitals Diagnostic Trees

### LCP (Largest Contentful Paint) — Target: < 2.5s

```
LCP > 2.5s?
├─ What is the LCP element?
│  ├─ Hero image
│  │  ├─ Using `next/image`? → Yes: check `priority` prop on above-fold images
│  │  ├─ Image format? → Ensure WebP/AVIF (automatic with next/image)
│  │  ├─ Image size > 200KB? → Resize to actual display dimensions
│  │  ├─ Lazy loaded? → Remove `loading="lazy"` for above-fold images
│  │  └─ CDN serving? → Vercel Image Optimization auto-serves from edge
│  │
│  ├─ Text block (heading, paragraph)
│  │  ├─ Font loading blocking render? → Use `next/font` with `display: swap`
│  │  ├─ Web font file > 100KB? → Subset to needed characters
│  │  └─ Font loaded from third-party? → Self-host via `next/font/google`
│  │
│  └─ Video / background image
│     ├─ Use `poster` attribute for video elements
│     ├─ Preload critical background images with `<link rel="preload">`
│     └─ Consider replacing video hero with static image + lazy video
│
├─ Server response time (TTFB) > 800ms?
│  ├─ Using SSR for static content? → Switch to SSG or ISR
│  ├─ Can use Cache Components? → Add `'use cache'` to slow Server Components
│  ├─ Database queries slow? → Add connection pooling, check query plans
│  ├─ Edge Config available? → Use for configuration data (< 5ms reads)
│  └─ Region mismatch? → Deploy function in same region as database
│
└─ Render-blocking resources?
   ├─ Large CSS file? → Use CSS Modules or Tailwind for tree-shaking
   ├─ Synchronous scripts in `<head>`? → Move to `next/script` with `afterInteractive`
   └─ Third-party scripts? → Defer with `next/script strategy="lazyOnload"`
```

### INP (Interaction to Next Paint) — Target: < 200ms

```
INP > 200ms?
├─ Which interaction is slow?
│  ├─ Button click / form submit
│  │  ├─ Heavy computation on main thread? → Move to Web Worker
│  │  ├─ State update triggers large re-render? → Memoize with `useMemo`/`React.memo`
│  │  ├─ Fetch request blocking UI? → Use `useTransition` for non-urgent updates
│  │  └─ Server Action slow? → Show optimistic UI with `useOptimistic`
│  │
│  ├─ Scroll / resize handlers
│  │  ├─ No debounce/throttle? → Add `requestAnimationFrame` or debounce
│  │  ├─ Layout thrashing? → Batch DOM reads, then writes
│  │  └─ Intersection Observer available? → Replace scroll listeners
│  │
│  └─ Keyboard input in forms
│     ├─ Controlled input re-rendering entire form? → Use `useRef` for form state
│     ├─ Expensive validation on every keystroke? → Debounce validation
│     └─ Large component tree updating? → Push `'use client'` boundary down
│
├─ Hydration time > 500ms?
│  ├─ Too many client components? → Audit `'use client'` boundaries
│  ├─ Large component tree hydrating at once? → Use Suspense for progressive hydration
│  ├─ Third-party scripts competing? → Defer with `next/script`
│  └─ Bundle size > 200KB (gzipped)? → See bundle analysis below
│
└─ Long tasks (> 50ms) on main thread?
   ├─ Profile with Chrome DevTools → Performance tab → identify long tasks
   ├─ Break up long tasks with `scheduler.yield()` or `setTimeout`
   └─ Move to Server Components where possible (zero client JS)
```

### CLS (Cumulative Layout Shift) — Target: < 0.1

```
CLS > 0.1?
├─ Images causing layout shift?
│  ├─ Missing `width`/`height`? → Always set dimensions (next/image does this)
│  ├─ Not using `next/image`? → Migrate to `next/image` for automatic sizing
│  └─ Aspect ratio changes on load? → Set explicit `aspect-ratio` in CSS
│
├─ Fonts causing layout shift?
│  ├─ Not using `next/font`? → Migrate to `next/font` (zero-CLS font loading)
│  ├─ FOUT (flash of unstyled text)? → `next/font` with `adjustFontFallback: true`
│  └─ Custom font metrics off? → Use `size-adjust` CSS property
│
├─ Dynamic content injected above viewport?
│  ├─ Ad banners / cookie banners? → Reserve space with `min-height`
│  ├─ Async-loaded components? → Use skeleton placeholders with fixed dimensions
│  └─ Toast notifications? → Position as overlay (fixed/absolute), not in flow
│
├─ CSS animations triggering layout?
│  ├─ Animating `width`, `height`, `top`, `left`? → Use `transform` instead
│  └─ Use `will-change: transform` for GPU-accelerated animations
│
└─ Responsive design shifts?
   ├─ Different layouts per breakpoint causing jump? → Use consistent aspect ratios
   └─ Client-side media query check? → Use CSS media queries, not JS `matchMedia`
```

---

## Rendering Strategy Decision Tree

<!-- Sourced from nextjs skill: references/rsc-boundaries.md -->
# RSC Boundaries

Detect and prevent invalid patterns when crossing Server/Client component boundaries.

## Detection Rules

### 1. Async Client Components Are Invalid

Client components **cannot** be async functions. Only Server Components can be async.

**Detect:** File has `'use client'` AND component is `async function` or returns `Promise`

```tsx
// Bad: async client component
'use client'
export default async function UserProfile() {
  const user = await getUser() // Cannot await in client component
  return <div>{user.name}</div>
}

// Good: Remove async, fetch data in parent server component
// page.tsx (server component - no 'use client')
export default async function Page() {
  const user = await getUser()
  return <UserProfile user={user} />
}

// UserProfile.tsx (client component)
'use client'
export function UserProfile({ user }: { user: User }) {
  return <div>{user.name}</div>
}
```

```tsx
// Bad: async arrow function client component
'use client'
const Dashboard = async () => {
  const data = await fetchDashboard()
  return <div>{data}</div>
}

// Good: Fetch in server component, pass data down
```

### 2. Non-Serializable Props to Client Components

Props passed from Server → Client must be JSON-serializable.

**Detect:** Server component passes these to a client component:
- Functions (except Server Actions with `'use server'`)
- `Date` objects
- `Map`, `Set`, `WeakMap`, `WeakSet`
- Class instances
- `Symbol` (unless globally registered)
- Circular references

```tsx
// Bad: Function prop
// page.tsx (server)
export default function Page() {
  const handleClick = () => console.log('clicked')
  return <ClientButton onClick={handleClick} />
}

// Good: Define function inside client component
// ClientButton.tsx
'use client'
export function ClientButton() {
  const handleClick = () => console.log('clicked')
  return <button onClick={handleClick}>Click</button>
}
```

```tsx
// Bad: Date object (silently becomes string, then crashes)
// page.tsx (server)
export default async function Page() {
  const post = await getPost()
  return <PostCard createdAt={post.createdAt} /> // Date object
}

// PostCard.tsx (client) - will crash on .getFullYear()
'use client'
export function PostCard({ createdAt }: { createdAt: Date }) {
  return <span>{createdAt.getFullYear()}</span> // Runtime error!
}

// Good: Serialize to string on server
// page.tsx (server)
export default async function Page() {
  const post = await getPost()
  return <PostCard createdAt={post.createdAt.toISOString()} />
}

// PostCard.tsx (client)
'use client'
export function PostCard({ createdAt }: { createdAt: string }) {
  const date = new Date(createdAt)
  return <span>{date.getFullYear()}</span>
}
```

```tsx
// Bad: Class instance
const user = new UserModel(data)
<ClientProfile user={user} /> // Methods will be stripped

// Good: Pass plain object
const user = await getUser()
<ClientProfile user={{ id: user.id, name: user.name }} />
```

```tsx
// Bad: Map/Set
<ClientComponent items={new Map([['a', 1]])} />

// Good: Convert to array/object
<ClientComponent items={Object.fromEntries(map)} />
<ClientComponent items={Array.from(set)} />
```

### 3. Server Actions Are the Exception

Functions marked with `'use server'` CAN be passed to client components.

```tsx
// Valid: Server Action can be passed
// actions.ts
'use server'
export async function submitForm(formData: FormData) {
  // server-side logic
}

// page.tsx (server)
import { submitForm } from './actions'
export default function Page() {
  return <ClientForm onSubmit={submitForm} /> // OK!
}

// ClientForm.tsx (client)
'use client'
export function ClientForm({ onSubmit }: { onSubmit: (data: FormData) => Promise<void> }) {
  return <form action={onSubmit}>...</form>
}
```

## Quick Reference

| Pattern | Valid? | Fix |
|---------|--------|-----|
| `'use client'` + `async function` | No | Fetch in server parent, pass data |
| Pass `() => {}` to client | No | Define in client or use server action |
| Pass `new Date()` to client | No | Use `.toISOString()` |
| Pass `new Map()` to client | No | Convert to object/array |
| Pass class instance to client | No | Pass plain object |
| Pass server action to client | Yes | - |
| Pass `string/number/boolean` | Yes | - |
| Pass plain object/array | Yes | - |

---

## Bundle Size Analysis

<!-- Sourced from nextjs skill: references/bundling.md > Bundle Analysis -->
Analyze bundle size with the built-in analyzer (Next.js 16.1+):

```bash
next experimental-analyze
```

This opens an interactive UI to:
- Filter by route, environment (client/server), and type
- Inspect module sizes and import chains
- View treemap visualization

Save output for comparison:

```bash
next experimental-analyze --output
# Output saved to .next/diagnostics/analyze
```

Reference: https://nextjs.org/docs/app/guides/package-bundling

---

## Caching Strategy Matrix

<!-- Sourced from nextjs skill: references/data-patterns.md > Decision Tree -->
```
Need to fetch data?
├── From a Server Component?
│   └── Use: Fetch directly (no API needed)
│
├── From a Client Component?
│   ├── Is it a mutation (POST/PUT/DELETE)?
│   │   └── Use: Server Action
│   └── Is it a read (GET)?
│       └── Use: Route Handler OR pass from Server Component
│
├── Need external API access (webhooks, third parties)?
│   └── Use: Route Handler
│
└── Need REST API for mobile app / external clients?
    └── Use: Route Handler
```

### Cache Invalidation Patterns

<!-- Sourced from next-cache-components skill: Cache Invalidation -->
### `cacheTag()` - Tag Cached Content

```tsx
import { cacheTag } from 'next/cache'

async function getProducts() {
  'use cache'
  cacheTag('products')
  return db.products.findMany()
}

async function getProduct(id: string) {
  'use cache'
  cacheTag('products', `product-${id}`)
  return db.products.findUnique({ where: { id } })
}
```

### `updateTag()` - Immediate Invalidation

Use when you need the cache refreshed within the same request:

```tsx
'use server'

import { updateTag } from 'next/cache'

export async function updateProduct(id: string, data: FormData) {
  await db.products.update({ where: { id }, data })
  updateTag(`product-${id}`)  // Immediate - same request sees fresh data
}
```

### `revalidateTag()` - Background Revalidation

Use for stale-while-revalidate behavior:

```tsx
'use server'

import { revalidateTag } from 'next/cache'

export async function createPost(data: FormData) {
  await db.posts.create({ data })
  revalidateTag('posts')  // Background - next request sees fresh data
}
```

---

---

## Performance Audit Checklist

Run through this when asked to optimize a Vercel application:

1. **Measure first**: Check Speed Insights dashboard for real-user CWV data
2. **Identify LCP element**: Use Chrome DevTools → Performance → identify the LCP element
3. **Audit `'use client'`**: Every `'use client'` file ships JS to the browser — minimize
4. **Check images**: All above-fold images use `next/image` with `priority`
5. **Check fonts**: All fonts loaded via `next/font` (zero CLS)
6. **Check third-party scripts**: All use `next/script` with correct strategy
7. **Check data fetching**: Server Components fetch in parallel, no waterfalls
8. **Check caching**: Cache Components used for expensive operations
9. **Check bundle**: Run analyzer, look for low-hanging fruit
10. **Check infrastructure**: Functions in correct region, Fluid Compute enabled

---

## Specific Fix Patterns

### Image Optimization

<!-- Sourced from nextjs skill: references/image.md -->
# Image Optimization

Use `next/image` for automatic image optimization.

## Always Use next/image

```tsx
// Bad: Avoid native img
<img src="/hero.png" alt="Hero" />

// Good: Use next/image
import Image from 'next/image'
<Image src="/hero.png" alt="Hero" width={800} height={400} />
```

## Required Props

Images need explicit dimensions to prevent layout shift:

```tsx
// Local images - dimensions inferred automatically
import heroImage from './hero.png'
<Image src={heroImage} alt="Hero" />

// Remote images - must specify width/height
<Image src="https://example.com/image.jpg" alt="Hero" width={800} height={400} />

// Or use fill for parent-relative sizing
<div style={{ position: 'relative', width: '100%', height: 400 }}>
  <Image src="/hero.png" alt="Hero" fill style={{ objectFit: 'cover' }} />
</div>
```

## Remote Images Configuration

Remote domains must be configured in `next.config.js`:

```js
// next.config.js
module.exports = {
  images: {
    remotePatterns: [
      {
        protocol: 'https',
        hostname: 'example.com',
        pathname: '/images/**',
      },
      {
        protocol: 'https',
        hostname: '*.cdn.com', // Wildcard subdomain
      },
    ],
  },
}
```

## Responsive Images

Use `sizes` to tell the browser which size to download:

```tsx
// Full-width hero
<Image
  src="/hero.png"
  alt="Hero"
  fill
  sizes="100vw"
/>

// Responsive grid (3 columns on desktop, 1 on mobile)
<Image
  src="/card.png"
  alt="Card"
  fill
  sizes="(max-width: 768px) 100vw, 33vw"
/>

// Fixed sidebar image
<Image
  src="/avatar.png"
  alt="Avatar"
  width={200}
  height={200}
  sizes="200px"
/>
```

## Blur Placeholder

Prevent layout shift with placeholders:

```tsx
// Local images - automatic blur hash
import heroImage from './hero.png'
<Image src={heroImage} alt="Hero" placeholder="blur" />

// Remote images - provide blurDataURL
<Image
  src="https://example.com/image.jpg"
  alt="Hero"
  width={800}
  height={400}
  placeholder="blur"
  blurDataURL="data:image/jpeg;base64,/9j/4AAQSkZJRg..."
/>

// Or use color placeholder
<Image
  src="https://example.com/image.jpg"
  alt="Hero"
  width={800}
  height={400}
  placeholder="empty"
  style={{ backgroundColor: '#e0e0e0' }}
/>
```

## Priority Loading

Use `priority` for above-the-fold images (LCP):

```tsx
// Hero image - loads immediately
<Image src="/hero.png" alt="Hero" fill priority />

// Below-fold images - lazy loaded by default (no priority needed)
<Image src="/card.png" alt="Card" width={400} height={300} />
```

## Common Mistakes

```tsx
// Bad: Missing sizes with fill - downloads largest image
<Image src="/hero.png" alt="Hero" fill />

// Good: Add sizes for proper responsive behavior
<Image src="/hero.png" alt="Hero" fill sizes="100vw" />

// Bad: Using width/height for aspect ratio only
<Image src="/hero.png" alt="Hero" width={16} height={9} />

// Good: Use actual display dimensions or fill with sizes
<Image src="/hero.png" alt="Hero" fill sizes="100vw" style={{ objectFit: 'cover' }} />

// Bad: Remote image without config
<Image src="https://untrusted.com/image.jpg" alt="Image" width={400} height={300} />
// Error: Invalid src prop, hostname not configured

// Good: Add hostname to next.config.js remotePatterns
```

## Static Export

When using `output: 'export'`, use `unoptimized` or custom loader:

```tsx
// Option 1: Disable optimization
<Image src="/hero.png" alt="Hero" width={800} height={400} unoptimized />

// Option 2: Global config
// next.config.js
module.exports = {
  output: 'export',
  images: { unoptimized: true },
}

// Option 3: Custom loader (Cloudinary, Imgix, etc.)
const cloudinaryLoader = ({ src, width, quality }) => {
  return `https://res.cloudinary.com/demo/image/upload/w_${width},q_${quality || 75}/${src}`
}

<Image loader={cloudinaryLoader} src="sample.jpg" alt="Sample" width={800} height={400} />
```

### Font Loading

<!-- Sourced from nextjs skill: references/font.md -->
# Font Optimization

Use `next/font` for automatic font optimization with zero layout shift.

## Google Fonts

```tsx
// app/layout.tsx
import { Inter } from 'next/font/google'

const inter = Inter({ subsets: ['latin'] })

export default function RootLayout({ children }: { children: React.ReactNode }) {
  return (
    <html lang="en" className={inter.className}>
      <body>{children}</body>
    </html>
  )
}
```

## Multiple Fonts

```tsx
import { Inter, Roboto_Mono } from 'next/font/google'

const inter = Inter({
  subsets: ['latin'],
  variable: '--font-inter',
})

const robotoMono = Roboto_Mono({
  subsets: ['latin'],
  variable: '--font-roboto-mono',
})

export default function RootLayout({ children }: { children: React.ReactNode }) {
  return (
    <html lang="en" className={`${inter.variable} ${robotoMono.variable}`}>
      <body>{children}</body>
    </html>
  )
}
```

Use in CSS:
```css
body {
  font-family: var(--font-inter);
}

code {
  font-family: var(--font-roboto-mono);
}
```

## Font Weights and Styles

```tsx
// Single weight
const inter = Inter({
  subsets: ['latin'],
  weight: '400',
})

// Multiple weights
const inter = Inter({
  subsets: ['latin'],
  weight: ['400', '500', '700'],
})

// Variable font (recommended) - includes all weights
const inter = Inter({
  subsets: ['latin'],
  // No weight needed - variable fonts support all weights
})

// With italic
const inter = Inter({
  subsets: ['latin'],
  style: ['normal', 'italic'],
})
```

## Local Fonts

```tsx
import localFont from 'next/font/local'

const myFont = localFont({
  src: './fonts/MyFont.woff2',
})

// Multiple files for different weights
const myFont = localFont({
  src: [
    {
      path: './fonts/MyFont-Regular.woff2',
      weight: '400',
      style: 'normal',
    },
    {
      path: './fonts/MyFont-Bold.woff2',
      weight: '700',
      style: 'normal',
    },
  ],
})

// Variable font
const myFont = localFont({
  src: './fonts/MyFont-Variable.woff2',
  variable: '--font-my-font',
})
```

## Tailwind CSS Integration

```tsx
// app/layout.tsx
import { Inter } from 'next/font/google'

const inter = Inter({
  subsets: ['latin'],
  variable: '--font-inter',
})

export default function RootLayout({ children }) {
  return (
    <html lang="en" className={inter.variable}>
      <body>{children}</body>
    </html>
  )
}
```

```js
// tailwind.config.js
module.exports = {
  theme: {
    extend: {
      fontFamily: {
        sans: ['var(--font-inter)'],
      },
    },
  },
}
```

## Preloading Subsets

Only load needed character subsets:

```tsx
// Latin only (most common)
const inter = Inter({ subsets: ['latin'] })

// Multiple subsets
const inter = Inter({ subsets: ['latin', 'latin-ext', 'cyrillic'] })
```

## Display Strategy

Control font loading behavior:

```tsx
const inter = Inter({
  subsets: ['latin'],
  display: 'swap', // Default - shows fallback, swaps when loaded
})

// Options:
// 'auto' - browser decides
// 'block' - short block period, then swap
// 'swap' - immediate fallback, swap when ready (recommended)
// 'fallback' - short block, short swap, then fallback
// 'optional' - short block, no swap (use if font is optional)
```

## Don't Use Manual Font Links

Always use `next/font` instead of `<link>` tags for Google Fonts.

```tsx
// Bad: Manual link tag (blocks rendering, no optimization)
<link href="https://fonts.googleapis.com/css2?family=Inter" rel="stylesheet" />

// Bad: Missing display and preconnect
<link href="https://fonts.googleapis.com/css2?family=Inter" rel="stylesheet" />

// Good: Use next/font (self-hosted, zero layout shift)
import { Inter } from 'next/font/google'

const inter = Inter({ subsets: ['latin'] })
```

## Common Mistakes

```tsx
// Bad: Importing font in every component
// components/Button.tsx
import { Inter } from 'next/font/google'
const inter = Inter({ subsets: ['latin'] }) // Creates new instance each time!

// Good: Import once in layout, use CSS variable
// app/layout.tsx
const inter = Inter({ subsets: ['latin'], variable: '--font-inter' })

// Bad: Using @import in CSS (blocks rendering)
/* globals.css */
@import url('https://fonts.googleapis.com/css2?family=Inter');

// Good: Use next/font (self-hosted, no network request)
import { Inter } from 'next/font/google'

// Bad: Loading all weights when only using a few
const inter = Inter({ subsets: ['latin'] }) // Loads all weights

// Good: Specify only needed weights (for non-variable fonts)
const inter = Inter({ subsets: ['latin'], weight: ['400', '700'] })

// Bad: Missing subset - loads all characters
const inter = Inter({})

// Good: Always specify subset
const inter = Inter({ subsets: ['latin'] })
```

## Font in Specific Components

```tsx
// For component-specific fonts, export from a shared file
// lib/fonts.ts
import { Inter, Playfair_Display } from 'next/font/google'

export const inter = Inter({ subsets: ['latin'], variable: '--font-inter' })
export const playfair = Playfair_Display({ subsets: ['latin'], variable: '--font-playfair' })

// components/Heading.tsx
import { playfair } from '@/lib/fonts'

export function Heading({ children }) {
  return <h1 className={playfair.className}>{children}</h1>
}
```

### Cache Components (Next.js 16)

<!-- Sourced from next-cache-components skill: use cache Directive -->
### File Level

```tsx
'use cache'

export default async function Page() {
  // Entire page is cached
  const data = await fetchData()
  return <div>{data}</div>
}
```

### Component Level

```tsx
export async function CachedComponent() {
  'use cache'
  const data = await fetchData()
  return <div>{data}</div>
}
```

### Function Level

```tsx
export async function getData() {
  'use cache'
  return db.query('SELECT * FROM posts')
}
```

---

### Optimistic UI for Server Actions

<!-- Sourced from nextjs skill: references/data-patterns.md > Client Component Data Fetching -->
When Client Components need data:

### Option 1: Pass from Server Component (Preferred)

```tsx
// Server Component
async function Page() {
  const data = await fetchData();
  return <ClientComponent initialData={data} />;
}

// Client Component
'use client';
function ClientComponent({ initialData }) {
  const [data, setData] = useState(initialData);
  // ...
}
```

### Option 2: Fetch on Mount (When Necessary)

```tsx
'use client';
import { useEffect, useState } from 'react';

function ClientComponent() {
  const [data, setData] = useState(null);

  useEffect(() => {
    fetch('/api/data')
      .then(r => r.json())
      .then(setData);
  }, []);

  if (!data) return <Loading />;
  return <div>{data.value}</div>;
}
```

### Option 3: Server Action for Reads (Works But Not Ideal)

Server Actions can be called from Client Components for reads, but this is not their intended purpose:

```tsx
'use client';
import { getData } from './actions';
import { useEffect, useState } from 'react';

function ClientComponent() {
  const [data, setData] = useState(null);

  useEffect(() => {
    getData().then(setData);
  }, []);

  return <div>{data?.value}</div>;
}
```

**Note**: Server Actions always use POST, so no HTTP caching. Prefer Route Handlers for cacheable reads.

---

Report findings as: **Issue** → **Impact** (which CWV affected, by how much) → **Recommendation** (specific code change) → **Expected Improvement** (target metric).

Always reference the **Next.js skill** (`⤳ skill: nextjs`) for framework patterns. For monitoring setup, configure drains via Dashboard or REST API.

## Available skills

Skills under `skills/` auto-load by description match:

- **ai-gateway** — Vercel AI Gateway expert guidance. Use when configuring model routing, provider failover, cost tracking, or managing multiple AI providers through a unified API.
- **ai-sdk** — Vercel AI SDK expert guidance. Use when building AI-powered features — chat interfaces, text generation, structured output, tool calling, agents, MCP integration, streaming, embeddings, reranking, image generation, or working with any LLM provider.
- **auth** — Authentication integration guidance — Clerk (native Vercel Marketplace), Descope, and Auth0 setup for Next.js applications. Covers middleware auth patterns, sign-in/sign-up flows, and Marketplace provisioning. Use when implementing user authentication.
- **bootstrap** — Project bootstrapping orchestrator for repos that depend on Vercel-linked resources (databases, auth, and managed integrations). Use when setting up or repairing a repository so linking, environment provisioning, env pulls, and first-run db/dev commands happen in the correct safe order.
- **chat-sdk** — Vercel Chat SDK expert guidance. Use when building multi-platform chat bots — Slack, Telegram, Microsoft Teams, Discord, Google Chat, GitHub, Linear — with a single codebase. Covers the Chat class, adapters, threads, messages, cards, modals, streaming, state management, and webhook setup.
- **deployments-cicd** — Vercel deployment and CI/CD expert guidance. Use when deploying, promoting, rolling back, inspecting deployments, building with --prebuilt, or configuring CI workflow files for Vercel.
- **env-vars** — Vercel environment variable expert guidance. Use when working with .env files, vercel env commands, OIDC tokens, or managing environment-specific configuration.
- **knowledge-update** — Corrects outdated LLM knowledge about the Vercel platform and introduces new products. Injected at session start.
- **marketplace** — Vercel Marketplace expert guidance — discovering, installing, and building integrations, auto-provisioned environment variables, unified billing, and the vercel integration CLI. Use when consuming third-party services, building custom integrations, or managing marketplace resources on Vercel.
- **next-cache-components** — Next.js 16 Cache Components guidance — PPR, use cache directive, cacheLife, cacheTag, updateTag, and migration from unstable_cache. Use when implementing partial prerendering, caching strategies, or migrating from older Next.js cache patterns.
- **next-forge** — next-forge expert guidance — production-grade Turborepo monorepo SaaS starter by Vercel. Use when working in a next-forge project, scaffolding with `npx next-forge init`, or editing @repo/* workspace packages.
- **next-upgrade** — Upgrade Next.js to the latest version following official migration guides and codemods. Use when upgrading Next.js versions, running codemods, or migrating between major releases.
- **nextjs** — Next.js App Router expert guidance. Use when building, debugging, or architecting Next.js applications — routing, Server Components, Server Actions, Cache Components, layouts, middleware/proxy, data fetching, rendering strategies, and deployment on Vercel.
- **react-best-practices** — React best-practices reviewer for TSX files. Triggers after editing multiple TSX components to run a condensed quality checklist covering component structure, hooks usage, accessibility, performance, and TypeScript patterns.
- **routing-middleware** — Vercel Routing Middleware guidance — request interception before cache, rewrites, redirects, personalization. Works with any framework. Supports Edge, Node.js, and Bun runtimes. Use when intercepting requests at the platform level.
- **runtime-cache** — Vercel Runtime Cache API guidance — ephemeral per-region key-value cache with tag-based invalidation. Shared across Functions, Routing Middleware, and Builds. Use when implementing caching strategies beyond framework-level caching.
- **shadcn** — shadcn/ui expert guidance — CLI, component installation, composition patterns, custom registries, theming, Tailwind CSS integration, and high-quality interface design. Use when initializing shadcn, adding components, composing product UI, building custom registries, configuring themes, or troubleshooting component issues.
- **turbopack** — Turbopack expert guidance. Use when configuring the Next.js bundler, optimizing HMR, debugging build issues, or understanding the Turbopack vs Webpack differences.
- **vercel-agent** — Vercel Agent guidance — AI-powered code review, incident investigation, and SDK installation. Automates PR analysis and anomaly debugging. Use when configuring or understanding Vercel's AI development tools.
- **vercel-cli** — Vercel CLI expert guidance. Use when deploying, managing environment variables, linking projects, viewing logs, managing domains, or interacting with the Vercel platform from the command line.
- **vercel-functions** — Vercel Functions expert guidance — Serverless Functions, Edge Functions, Fluid Compute, streaming, Cron Jobs, and runtime configuration. Use when configuring, debugging, or optimizing server-side code running on Vercel.
- **vercel-sandbox** — Vercel Sandbox guidance — ephemeral Firecracker microVMs for running untrusted code safely. Supports AI agents, code generation, and experimentation. Use when executing user-generated or AI-generated code in isolation.
- **vercel-storage** — Vercel storage expert guidance — Blob, Edge Config, and Marketplace storage (Neon Postgres, Upstash Redis). Use when choosing, configuring, or using data storage with Vercel applications.
- **verification** — Full-story verification — infers what the user is building, then verifies the complete flow end-to-end: browser → API → data → response. Triggers on dev server start and 'why isn't this working' signals.
- **workflow** — Vercel Workflow DevKit (WDK) expert guidance. Use when building durable workflows, long-running tasks, API routes or agents that need pause/resume, retries, step-based execution, or crash-safe orchestration with Vercel Workflow.

## Custom commands

- `/_conventions` — 
- `/bootstrap` — Bootstrap a repository with Vercel-linked resources by running preflight checks, provisioning integrations, verifying env keys, and then executing db/dev startup commands safely.
- `/deploy` — Deploy the current project to Vercel. Pass "prod" or "production" as argument to deploy to production. Default is to ask the user which target (production or preview).
- `/env` — Manage Vercel environment variables. Commands include list, pull, add, remove, and diff. Use to sync environment variables between Vercel and your local development environment.
- `/marketplace` — Discover and install Vercel Marketplace integrations. Use to find databases, CMS, auth providers, and other services available on the Vercel Marketplace.
- `/status` — Show the status of the current Vercel project — recent deployments, linked project info, and environment overview.
