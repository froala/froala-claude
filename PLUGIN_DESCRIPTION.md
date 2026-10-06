# Froala Agent Skills

Portable skills that make an AI coding agent an expert on Froala WYSIWYG editor development — initialization, events, custom plugins, Filestack cloud storage integration, and error diagnosis.

---

## What It Does

These skills give an agent deep, structured knowledge of the Froala editor API. Instead of searching documentation, developers get accurate, framework-specific answers directly in their coding workflow.

Written in the open `SKILL.md` format, so they work in Claude Code, Cursor, Codex, GitHub Copilot, and any other agent that supports it.

**No MCP server required.** Everything works through skills — the right Froala API context is surfaced based on what you're working on.

---

## Skills (5)

| Skill | When it activates | What it provides |
|-------|-------------------|------------------|
| `froala-initialization-and-sdks` | Importing Froala CSS/JS, using framework wrappers, or configuring editor instances | Installation for Vanilla JS, React, Vue 3, Angular; toolbar/theme config; lifecycle and destroy patterns; license key handling |
| `froala-methods-and-events` | Writing event listeners, getting/setting HTML content, intercepting keyboard input, or accessing the `editor` instance | Event listener syntax; safe HTML get/set; paste cleanup config; pre-init API call fixes |
| `froala-custom-plugins` | Registering custom plugins, creating toolbar buttons, building floating popups, or extending formatting | Custom plugin boilerplate; command and dropdown registration; state sync patterns; SVG icon registration; plugin load order |
| `froala-filestack-integration` | Configuring image/file upload, bypassing the default uploader, or wiring Filestack into the editor | Official `filestack` plugin setup via `pluginsEnabled` and `filestackOptions` (recommended); manual `image.beforeUpload` interception with `client.upload()` and `image.insert()` for advanced cases |
| `froala-error-diagnosis` | Hitting Froala errors, broken toolbars, or features that silently do nothing | Symptom-indexed diagnosis for missing toolbar buttons, unstyled editors, `FroalaEditor is not defined`, undefined plugin methods, silent upload failures, empty `html.get()`, and `this` context loss |

---

## Use Cases

### 1. Set up Froala in a React app

> **User:** "How do I add Froala to my React component with a custom toolbar?"

`froala-initialization-and-sdks` activates and provides a complete React component using `react-froala-wysiwyg` with the correct `config` prop, toolbar button list, and cleanup in `useEffect`.

---

### 2. Capture content changes

> **User:** "How do I listen for content changes in the Froala editor?"

`froala-methods-and-events` activates and shows the correct `events.on('contentChanged', ...)` syntax along with safe HTML extraction via `editor.html.get()`.

---

### 3. Build a custom toolbar button

> **User:** "I want to add a custom button to the Froala toolbar that inserts a special HTML block."

`froala-custom-plugins` activates and provides the full plugin registration boilerplate: `FroalaEditor.PLUGINS['myPlugin']`, `FroalaEditor.DefineIcon`, `FroalaEditor.RegisterCommand`, and toolbar config.

---

### 4. Upload images to Filestack instead of a local server

> **User:** "How do I make Froala upload images to Filestack instead of my own server?"

`froala-filestack-integration` activates and recommends the official Froala Filestack plugin — enabling it through `pluginsEnabled: ['filestack']` and configuring `filestackOptions` — with the manual `image.beforeUpload` interception path for cases the plugin doesn't cover.

---

### 5. Diagnose a button that does nothing

> **User:** "My toolbar button shows up but clicking it does nothing."

`froala-error-diagnosis` activates and works through the likely causes: `RegisterCommand` running after `new FroalaEditor`, the `pluginsEnabled` allowlist, and `this` context loss in callbacks.

---

## Installation

**Any supported agent:**

```bash
npx skills add froala/froala-claude
```

**Claude Code plugin marketplace:**

```
/plugin marketplace add froala/froala-claude
/plugin install froala@froala-claude
```

See the README for manual installation and local development setup.

---

## Why These Skills Exist

Froala has a rich but sprawling API. Developers frequently hit issues with initialization order, event binding timing, custom plugin boilerplate, upload interception, and failures that surface no error at all. These skills encode the solutions to those recurring problems in a structured form — so an agent gives correct, specific answers instead of generic WYSIWYG advice.
