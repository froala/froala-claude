---
name: froala-ai-assist
description: >
  Use when the user is adding AI writing features to Froala. Activates on:
  config tokens (aiSupplementalTermsAccepted, aiAssistRequest, aiAssistEndpoint, aiAssistHeaders,
  aiChatModels, aiChatDefaultModel, aiChatStreamResponse, inlineSuggestions, aiSpeechToText),
  button tokens (aiAssist, aiShortCuts, aiChatAssistant),
  plugin tokens (ai_assist.min.js, AI Assist, AI Chat),
  provider tokens (OpenAI, Gemini, Anthropic, Groq with Froala),
  or when user asks how to add AI writing, AI chat, tone change, translate, improve writing,
  AI image generation, or ghost-text autocomplete to the Froala editor.
version: 1.0.0
license: MIT
---

# Froala AI Assist

**Version gates:** AI Assist requires **5.1.0+**. AI Chat (`aiChatAssistant`) requires **5.4.0+**. AI image generation requires **5.5.0+**. Options below are documented against 5.5.0.

AI Assist adds free-form prompts, change tone, translate, improve writing, an AI Chat panel and AI image generation.

**Froala does not ship a model.** You connect your own provider — OpenAI, Google Gemini, Anthropic, Groq, or your own backend.

Plugin files: `js/plugins/ai_assist.min.js` and `css/plugins/ai_assist.min.css`. Both are already in the `pkgd` bundles.

---

## Required: Accept the Terms

```js
aiSupplementalTermsAccepted: true,
```

Without it, every AI button opens a popup asking you to accept the AI Supplemental Terms and nothing else happens. This is the single most common reason AI Assist "doesn't work".

---

## Recommended: Call Your Own Backend

Keep provider API keys on your server. `aiAssistRequest` receives the request data, an `AbortSignal`, and an optional `onChunk` callback for streaming. Return the answer as an HTML string, or `{ answer, session_id? }`.

```js
new FroalaEditor('#editor', {
  aiSupplementalTermsAccepted: true,
  toolbarButtons: ['bold', 'italic', '|', 'aiAssist', 'aiShortCuts', 'aiChatAssistant'],

  aiAssistRequest: async function (data, signal, onChunk) {
    const res = await fetch('/api/ai', {       // your server calls OpenAI / Gemini / Anthropic
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ prompt: data.prompt, context: data.context, model: data.model }),
      signal,
    });
    const json = await res.json();
    return json.answer;                        // HTML string
  },

  // Optional: model picker in the AI Chat panel
  aiChatModels: [
    { modelName: 'gpt-5', displayName: 'GPT-5' },
    { modelName: 'gemini-2.5-pro', displayName: 'Gemini 2.5 Pro' },
    { modelName: 'claude-sonnet-4-5', displayName: 'Claude Sonnet' },
  ],
  aiChatDefaultModel: 'gpt-5',
});
```

`data` may include `prompt`, `context`, `question`, `session_id`, `model`, `webSearchEnabled`, `reasoningEnabled`, `files`, `referenceUrls` and `regenerate`, depending on which feature the user invoked. The selected `aiChatModels` entry's `modelName` arrives as `data.model`.

### Endpoint alternative

Instead of `aiAssistRequest`, set `aiAssistEndpoint` to a URL, with `aiAssistHeaders` for auth and `aiAssistResponseParserPath` to locate the answer in the response.

---

## Prototype Only: Calling the Provider from the Browser

Froala's official example calls OpenAI directly from the browser. **This exposes your API key to every visitor.** Use it for local prototyping only, never in production — route through your own backend instead.

https://froala.com/wysiwyg-editor/examples/ai-assist-direct-openai-api-integration/

---

## Toolbar Buttons

| Button | Purpose |
|---|---|
| `aiAssist` | Free-form prompt panel |
| `aiShortCuts` | Tone, translate, improve writing shortcuts |
| `aiChatAssistant` | AI Chat panel |

All three come from the `ai_assist` plugin.

---

## Other Options

| Option | Purpose |
|---|---|
| `aiAssistToneOptions` / `aiAssistTranslateOptions` | Choices in the tone and translate menus |
| `aiAssistPromptTemplate` / `aiImproveWritingPrompt` | Prompt templates |
| `aiChatWebSearchEnabled` / `aiChatReasoningEnabled` | Initial AI Chat toggles |
| `aiChatStreamResponse` | Stream AI Chat responses |
| `aiChatSupportedFileTypes` / `aiChatMaxFileSizeMB` | Files AI Chat accepts as context |
| `aiSpeechToText` | Voice dictation for AI prompts |
| `inlineSuggestions` | Ghost-text autocomplete while typing |

---

## Common Mistakes

| Problem | Cause | Fix |
|---|---|---|
| AI buttons only show a terms popup | `aiSupplementalTermsAccepted` not set | Set it to `true` |
| AI buttons missing entirely | Core build without `ai_assist.min.js` | Use a `pkgd` bundle, or load the plugin JS and CSS |
| API key visible in the browser | Calling the provider directly from the client | Move the call behind your own endpoint via `aiAssistRequest` |
| Response inserted as plain text | Returned Markdown instead of HTML | Return an HTML string from `aiAssistRequest` |
| Request never cancels | Ignored the `signal` argument | Pass `signal` through to `fetch` |

---

## Reference

- AI Assist plugin: https://froala.com/wysiwyg-editor/docs/plugins/ai-assist-plugin/
- OpenAI example: https://froala.com/wysiwyg-editor/examples/ai-assist-direct-openai-api-integration/
