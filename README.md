# Froala Agent Skills

Portable [Agent Skills](https://agentskills.io/home) that make an AI coding agent an expert on [Froala WYSIWYG editor](https://froala.com/wysiwyg-editor/) development — initialization, events, custom plugins, AI Assist, Word and Markdown workflows, Filestack uploads, and error diagnosis.

The skills use the open `SKILL.md` format, so they work in Claude Code, Cursor, Codex, GitHub Copilot, and any other agent that supports it. Install once and your agent answers Froala questions from structured knowledge instead of guessing.

---

## Installation

### Any supported agent (skills.sh)

```bash
npx skills add froala/froala-claude
```

Installs all seven skills into `.agents/skills/` for any `SKILL.md`-compatible agent, and symlinks them for Claude Code. Add `-g` to install globally, or `-s <skill-name>` to pick individual skills.

### Claude Code plugin marketplace

```
/plugin marketplace add froala/froala-claude
/plugin install froala@froala-claude
```

The skills then load as `froala:froala-initialization-and-sdks`, `froala:froala-methods-and-events`, and so on.

### Manual

Copy any `skills/<name>/` directory into your agent's skills directory — for example `.claude/skills/` for Claude Code, or `.agents/skills/` for Cursor, Codex, and Copilot.

### Local / Development

From a clone of this repository:

```
/plugin marketplace add /path/to/froala-claude
/plugin install froala@froala-claude
```

Validate manifest changes before pushing:

```
claude plugin validate . --strict
```

---

## Skills

The seven skills activate automatically based on your code and questions. You don't invoke them manually — your agent matches the context against each skill's description.

### `froala-initialization-and-sdks`

**Activates when** you import Froala packages, use framework wrappers, or ask about setup.

Covers:
- Installation for Vanilla JS, React, Vue 3, and Angular
- Toolbar configuration and layout options
- Editor lifecycle and proper destroy/cleanup patterns
- License key setup for localhost vs. production

**Trigger keywords:** `froala-editor`, `react-froala-wysiwyg`, `vue-froala-wysiwyg`, `angular-froala-wysiwyg`, `new FroalaEditor(`, `toolbarButtons`, `heightMin`

---

### `froala-methods-and-events`

**Activates when** you work with editor events, get/set content, or intercept keyboard input.

Covers:
- Event listener syntax (`contentChanged`, `focus`, `blur`, `keydown`, etc.)
- Safe HTML get/set via `editor.html.get()` and `editor.html.set()`
- Paste cleanup configuration to strip Word/Google Docs formatting
- Fixes for calling API methods before editor initialization

**Trigger keywords:** `events.on`, `events.bind`, `contentChanged`, `html.get`, `html.set`, `pastePlain`, `paste.afterCleanup`

---

### `froala-custom-plugins`

**Activates when** you register custom plugins, create toolbar buttons, build popups, or extend formatting.

Covers:
- Full plugin boilerplate (`FroalaEditor.PLUGINS`, `RegisterCommand`, `DefineIcon`)
- Custom dropdown buttons with option lists
- Floating popup panels
- State synchronization between custom plugin and editor
- SVG icon registration and shortcut key binding
- Plugin loading order requirements

**Trigger keywords:** `FroalaEditor.PLUGINS`, `RegisterCommand`, `DefineIcon`, `initPopup`, `showPopup`, `POPUP_TEMPLATES`

---

### `froala-filestack-integration`

**Activates when** you wire Filestack into Froala for image or file uploads — either via the official plugin or manual upload interception.

Covers two approaches:

**Approach 1 — Official Froala Filestack Plugin (recommended):**
- Enabling the native `filestack` plugin via `pluginsEnabled: ['filestack']`
- `filestackOptions` configuration (`filestackAPI`, `uploadToFilestackOnly`, `pickerOptions`)
- Plugin events: `filestack.uploadedToFilestack`, `filestack.uploadFailedToFilestack`, `filestack.filestackPickerOpened`, `filestack.filestackPickerClosed`
- Required CDN dependencies (Filestack JS 3.x, drag-and-drop, transforms UI)

**Approach 2 — Manual Interception (advanced):**
- Intercepting `image.beforeUpload` to cancel Froala's default uploader
- Passing files to `filestack.client.upload()` and injecting the CDN URL back with `editor.image.insert()`
- React-specific wiring with correct `this` context
- Applying Filestack CDN transformations (resize, format conversion) before insert
- Common pitfalls (missing `return false`, arrow function `this` context loss, wrong Filestack JS version)

**Trigger keywords:** `filestackOptions`, `filestackAPI`, `uploadToFilestackOnly`, `pickerOptions`, `filestack.uploadedToFilestack`, `filestack.filestackPickerOpened`, `image.beforeUpload`, `file.beforeUpload`, `imageUploadURL`, `fileUploadURL`, `filestackcontent.com`

---

### `froala-error-diagnosis`

**Activates when** you encounter Froala errors, broken behavior, or something that silently does nothing.

Covers:
- Toolbar button missing or not showing (missing plugin JS, `pluginsEnabled` allowlist, wrong button name, v4 group object)
- Editor renders but looks broken or unstyled (missing CSS imports)
- `FroalaEditor is not defined` / `is not a constructor` (script load order, wrong import style)
- Plugin method undefined (`editor.image is undefined`, `editor.link is undefined`) — plugin not imported or called before `initialized`
- Editor does not mount / element not found (selector mismatch, DOM not ready)
- License warning banner (missing or invalid `key` — note the option is `key`, not `licenseKey`)
- Toolbar button renders but does nothing (plugin registered after editor init)
- Image/file upload fails silently (missing `imageUploadURL`, wrong server response shape, CORS)
- `html.get()` returns empty string (called before editor is ready)
- `this` is undefined or wrong in event handlers (arrow function context loss)
- Quick diagnosis checklist for unknown issues

**Trigger keywords:** `toolbar missing`, `button not showing`, `plugin not loading`, `editor broken`, `FroalaEditor is not defined`, `is not a constructor`, `html is undefined`, `image is undefined`, `Cannot read property`, `pluginsEnabled`

---

### `froala-ai-assist`

**Activates when** you add AI writing features — prompts, tone, translate, improve writing, AI Chat or AI image generation.

Covers:
- The required `aiSupplementalTermsAccepted: true`
- Routing requests through your own backend with `aiAssistRequest` (keeps provider API keys off the client)
- Provider choice: OpenAI, Google Gemini, Anthropic, Groq or your own service
- AI Chat model picker, streaming, file context, speech-to-text, inline suggestions

**Trigger keywords:** `aiSupplementalTermsAccepted`, `aiAssistRequest`, `aiChatModels`, `aiAssist`, `aiChatAssistant`, `inlineSuggestions`

---

### `froala-document-features`

**Activates when** you work with Word import/export, Markdown, Track Changes or real-time collaboration.

Covers:
- Word `.docx` import (browser via mammoth.js, or server-side) and export
- Markdown with `markdown.getMarkdown()` / `markdown.setMarkdown()`
- Track Changes — including that `track_changes.min.js` is **not** in the packaged bundles
- Collaborative editing: `collabConfig`, roles, suggestion mode, comments and version history

**Trigger keywords:** `import_from_word`, `export_to_word`, `markdown.getMarkdown`, `trackChanges`, `collabConfig`, `syncUrl`

---

## Example Interactions

**"How do I add Froala to my React app with a dark theme?"**
→ `froala-initialization-and-sdks` activates → Complete React component with `react-froala-wysiwyg`, config prop, and `theme: 'dark'`

**"How do I listen for content changes and get the HTML?"**
→ `froala-methods-and-events` activates → `contentChanged` event with `editor.html.get()` example

**"I want a custom toolbar button that inserts a signature block."**
→ `froala-custom-plugins` activates → Full `FroalaEditor.PLUGINS` + `RegisterCommand` boilerplate

**"How do I make Froala upload images to Filestack instead of my server?"**
→ `froala-filestack-integration` activates → Recommends the official Froala Filestack plugin with `filestackOptions` config, or manual `image.beforeUpload` interception + `client.upload()` + `editor.image.insert()` wiring for advanced cases

**"How do I add AI writing to Froala using our own OpenAI key?"**
→ `froala-ai-assist` activates → `aiAssistRequest` routed through your backend, with `aiSupplementalTermsAccepted: true` and a note on why the browser-direct example is prototype-only

**"How do I let users import a Word document into the editor?"**
→ `froala-document-features` activates → `import_from_word` with mammoth.js for client-side conversion, size and file-type limits, and the import events

**"My toolbar button shows up but clicking it does nothing."**
→ `froala-error-diagnosis` activates → Checks plugin registration order (`RegisterCommand` must run before `new FroalaEditor`), `pluginsEnabled` allowlist, and `this` context in callbacks

---

## Why These Skills Exist

Froala has a rich but sprawling API. Developers repeatedly hit the same problems: initialization order, event binding timing, custom plugin boilerplate, upload interception, and failures that produce no error at all. These skills encode the solutions to those recurring problems in a structured form, so an agent gives correct, Froala-specific answers instead of generic WYSIWYG advice.

---

## Project Structure

```
froala-claude/
├── .claude-plugin/
│   ├── plugin.json         # Plugin metadata
│   └── marketplace.json    # Marketplace entry (local + remote install)
├── skills/
│   ├── froala-initialization-and-sdks/
│   │   └── SKILL.md
│   ├── froala-methods-and-events/
│   │   └── SKILL.md
│   ├── froala-custom-plugins/
│   │   └── SKILL.md
│   ├── froala-filestack-integration/
│   │   └── SKILL.md
│   ├── froala-ai-assist/
│   │   └── SKILL.md
│   ├── froala-document-features/
│   │   └── SKILL.md
│   └── froala-error-diagnosis/
│       └── SKILL.md
├── skills.sh.json          # Grouping metadata for the skills.sh directory
├── PLUGIN_DESCRIPTION.md
└── README.md
```

---

## License

MIT
