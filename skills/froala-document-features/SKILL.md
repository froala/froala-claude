---
name: froala-document-features
description: >
  Use when the user is working with Froala's document-workflow plugins. Activates on:
  Word tokens (import_from_word, export_to_word, importFromWordUrlToUpload, wordExportFileName,
  importFromWordMaxFileSize, mammoth, docx, word.beforeImport, word.afterExport),
  Markdown tokens (markdown.getMarkdown, markdown.setMarkdown, markdown plugin),
  track changes tokens (trackChanges, showChanges, applyAll, removeAll, trackChangesEnabled, track_changes.min.js),
  collaboration tokens (collabConfig, collabMode, collabPanel, collabPresence, versionControl,
  syncUrl, suggestion mode, mentions, wysiwyg-editor-node-sdk),
  or when user asks about importing or exporting Word documents, Markdown editing,
  tracking changes, review workflows, comments, or real-time multi-user editing in Froala.
version: 1.0.0
license: MIT
---

# Froala Document Features

Covers Word import/export, Markdown, Track Changes and real-time collaboration.

Version gates: the Collaborative plugin requires **5.3.0+**. Word import/export, Markdown and Track Changes are available across v4 and v5.

---

## Word Import and Export

Import converts a `.docx` in the browser with mammoth.js, or server-side if you set an upload URL. Export writes a `.docx` from the editor content.

```html
<!-- Import (client-side conversion) -->
<script src="https://cdnjs.cloudflare.com/ajax/libs/mammoth/1.4.21/mammoth.browser.min.js"></script>
<!-- Export -->
<script src="https://cdn.jsdelivr.net/npm/file-saver-es@2.0.5/dist/FileSaver.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/html-docx-js@0.3.1/dist/html-docx.min.js"></script>
```

```js
new FroalaEditor('#editor', {
  toolbarButtons: ['bold', 'italic', '|', 'import_from_word', 'export_to_word'],

  importFromWordMaxFileSize: 3 * 1024 * 1024,   // default 3 MB
  importFromWordFileTypesAllowed: ['docx'],
  importFromWordUrlToUpload: null,              // null = convert in the browser with mammoth.js
  importFromWordEnableImportOnDrop: true,       // drop a .docx onto the editor to import
  wordExportFileName: 'document',               // .docx extension is added automatically

  events: {
    'word.beforeImport'() {},                   // return false to cancel
    'word.afterImport'() {},
    'word.beforeExport'(editorHtml) {},
    'word.afterExport'(editorHtml) {},
  },
});
```

Both plugins are in the `pkgd` bundles. With the core build, load `js/plugins/import_from_word.min.js` and `js/plugins/export_to_word.min.js`.

Docs: [Import from Word](https://froala.com/wysiwyg-editor/docs/plugins/import-from-word-plugin/) · [Export to Word](https://froala.com/wysiwyg-editor/docs/plugins/export-to-word-plugin/)

---

## Markdown

```js
new FroalaEditor('#editor', {
  toolbarButtons: ['bold', 'italic', '|', 'markdown'],

  events: {
    initialized() {
      this.markdown.setMarkdown('# Title\n\nSome **bold** text');
      const md = this.markdown.getMarkdown();   // string, or false if cancelled by markdown.beforeGet
    },
  },
});
```

`markdown.getMarkdown()` returns `false` rather than a string when a `markdown.beforeGet` handler cancels the read — check the type before using the result.

Docs: https://froala.com/wysiwyg-editor/docs/plugins/markdown-plugin/

---

## Track Changes

**Not included in the `pkgd` bundles.** Load it explicitly, or the buttons never render.

```js
import 'froala-editor/js/plugins/track_changes.min.js';

new FroalaEditor('#editor', {
  toolbarButtons: ['bold', 'italic', '|', 'trackChanges', 'showChanges', 'applyAll', 'removeAll'],
  trackChangesEnabled: true,   // start with tracking on
  showChangesEnabled: true,    // highlight changes
});
```

| Button | Action |
|---|---|
| `trackChanges` | Toggle recording |
| `showChanges` | Toggle highlighting |
| `applyAll` | Accept every change |
| `removeAll` | Reject every change |

Docs: https://froala.com/wysiwyg-editor/docs/plugins/track-changes-plugin/

---

## Real-Time Collaboration

Live multi-user editing, presence, threaded comments with @mentions, suggestion mode and version history. **Requires a backend** — it ships in the `wysiwyg-editor-node-sdk` package (WebSocket relay, comments/suggestions REST endpoints, version store).

```js
new FroalaEditor('#editor', {
  toolbarButtons: ['bold', 'italic', '|', 'collabMode', 'collabPanel', 'collabPresence', 'versionControl'],

  collabConfig: {
    docId: 'doc-123',                                 // shared by all collaborators
    user: { id: 'u1', name: 'Ada', role: 'editor' },  // 'editor' | 'suggester' | 'viewer'
    realTime: { syncUrl: 'wss://your-server.example/collab' },  // null = async offline-first mode
    commentsUrl: '/api/comments',
    suggestionsUrl: '/api/suggestions',
  },

  events: {
    'collab.connectionStatus'(status) {},   // 'connecting' | 'connected' | 'disconnected'
    'collab.synced'() {},
  },
});
```

Every collaborator on the same document must use the same `docId`. Set `realTime: null` for async (offline-first) mode instead of live sync.

Requires 5.3.0+. Docs: https://froala.com/wysiwyg-editor/docs/plugins/collaborative-plugin/

---

## Common Mistakes

| Problem | Cause | Fix |
|---|---|---|
| Track Changes buttons missing | `track_changes.min.js` not loaded — it is not in the `pkgd` bundles | Import the plugin explicitly |
| Word import does nothing | mammoth.js not loaded and `importFromWordUrlToUpload` is null | Load mammoth.js, or set a server upload URL |
| Word export does nothing | FileSaver / html-docx-js not loaded | Load both export dependencies |
| Import silently rejects a file | Over `importFromWordMaxFileSize` (default 3 MB) or not in `importFromWordFileTypesAllowed` | Raise the limit or allow the type |
| `getMarkdown()` returns `false` | A `markdown.beforeGet` handler cancelled the read | Check the return type before using it |
| Collaborators don't see each other | Different `docId`, or no reachable `syncUrl` | Share one `docId`; check the WebSocket connection |
| Suggestions can't be made | User `role` is `editor` or `viewer` | Set `role: 'suggester'` |

---

## Reference

- Plugins index: https://froala.com/wysiwyg-editor/docs/plugins/
- Server SDKs: https://froala.com/wysiwyg-editor/docs/sdks/
