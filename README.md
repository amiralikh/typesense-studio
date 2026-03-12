# TypeSense Studio

A single HTML file that gives you a clean UI for browsing and managing your [Typesense](https://typesense.org) data. No install, no npm, no server, no Docker container you have to babysit. Just open the file in a browser.

---

## Why this exists

Typesense is great. But if you hand a project manager, a content editor, or literally any non-technical person the API docs and say "just query the collections", you will have a bad time.

TypeSense Studio is the middle ground. It gives people a real UI to look at data, search through documents, check schemas, and do basic imports and exports, without them needing to know what a cURL command is. It costs you nothing to deploy because there is nothing to deploy. You drop one HTML file somewhere, tell them to open it, and you are done.

It is not trying to replace Typesense's dashboard or compete with enterprise tooling. It is just a handy, lightweight option for teams where the data lives in Typesense but not everyone on the team is a developer.

---

## What it does

**Connect and browse**
- Connect to any Typesense instance (local or remote) with host, port, protocol, and API key
- Save multiple connection profiles to your browser (localStorage), so you can switch between local dev, staging, and production without retyping credentials every time
- Live connection status in the topbar

**Overview**
- Stats at a glance: total collections, document counts, aliases, average search latency, index size
- Collections table with quick links to browse or delete

**Browse tab**
- Full-text search with configurable sort field and direction
- Paginated results table (20 per page)
- Per-row actions: view full document as JSON, edit inline, delete
- Export current results as JSON

**Schema tab**
- Field list with type badges (string, int32, float, bool, object, etc.)
- Flags shown per field: indexed, facet, optional, sortable, infix

**Import / Export tab**
- Paste a JSON array or NDJSON directly into the UI to import
- Action selector: upsert, create, or update
- Export all documents from a collection as NDJSON

**Aliases and API Keys**
- Create and delete aliases from a simple form
- Load and inspect API keys (requires admin key)

**Theme switcher**
- Light, dark, and system (follows OS preference)
- Preference saved to localStorage, no flash on reload

---

## How to use it

1. Download or copy `typesense-admin.html` to anywhere on your machine (or a shared drive, or a static file host, whatever works for your team)
2. Open it in a browser
3. Enter your Typesense server details and API key
4. Click Connect

That is it.

> Your Typesense server needs to run with `--enable-cors` if you are connecting from a browser. Without it the browser will block the requests. This is a Typesense/browser security thing, not a bug in this tool.

```
./typesense-server --enable-cors --api-key=your-key --data-dir=/tmp/data
```

---

## Saving connections

Click the bookmark icon next to the connect button to save the current connection with a name. Saved connections appear as clickable cards on the login screen so you can get back to them in one click. Everything stays in your browser's localStorage, nothing is sent anywhere.

---

## Tech stack

| Thing | What it is |
|---|---|
| Zero dependencies | Pure HTML, CSS, and vanilla JS. No framework, no bundler, no build step |
| Icons | [Heroicons 2.0](https://heroicons.com) (outline), inlined as SVG paths |
| Font | [Inter](https://rsms.me/inter/) via Google Fonts (one external request) |
| State | Flat JS object + single `render()` cycle, no virtual DOM |
| Storage | `localStorage` for saved connections and theme preference |
| Typesense API | Direct fetch calls to the REST API, no SDK |

The whole thing is one file and under 1,400 lines. You can read it, fork it, and modify it without needing to set up a project.

---

## Typesense version compatibility

The tool uses only the core REST API endpoints that have been stable for a long time:

| Endpoint | Used for |
|---|---|
| `GET /health` | Connection check |
| `GET /collections` | Listing collections and schemas |
| `GET /collections/:name/documents/search` | Browse and search |
| `POST /collections/:name/documents` | Add / upsert documents |
| `DELETE /collections/:name/documents/:id` | Delete a document |
| `POST /collections/:name/documents/import` | Bulk import |
| `GET /collections/:name/documents/export` | Export all |
| `DELETE /collections/:name` | Drop a collection |
| `GET /aliases`, `PUT /aliases/:name`, `DELETE /aliases/:name` | Alias management |
| `GET /keys` | API key listing |
| `GET /stats.json` | Server stats (overview page) |

All of these have been available since **v0.25**. There is nothing in here that requires a modern version.

Tested against:
- v0.25.x
- v26, v27, v28, v29
- v30.0 and v30.1 (latest as of writing)

If you are on anything older than v0.25, some endpoints might respond differently but the basics (connect, browse, search) should still work. If you find a version that breaks something, open an issue.

---

## Requirements

- A running Typesense instance (v0.25 or newer)
- A modern browser (Chrome, Firefox, Safari, Edge all work)
- An API key with the permissions you need (admin key gives full access)
- CORS enabled on the Typesense server when connecting from a browser

---

## Limitations

This is not a production admin panel. Some things it does not do:

- No authentication of its own (it just passes your API key to Typesense)
- No multi-user access control
- No query analytics
- No collection backups
- No synonym or stop word management (yet)

If you need those things, Typesense's own [Cloud dashboard](https://cloud.typesense.org) or a full-featured admin tool is a better fit.

---

## Built with AI

This project was built with the help of Claude (by Anthropic). It is a good example of what you can get done quickly when you are not precious about where the code comes from, and you focus on the actual problem you are solving.

---

## Disclaimer

This tool is provided as-is, with no warranties of any kind. It is a convenience UI, not a production-grade admin system. Do not use it as your only way to manage critical data. Always have backups. The authors are not responsible for accidental deletes, data loss, or anything else that goes wrong. You are connecting directly to your Typesense instance from the browser, which means your API key is visible in the page. Use a scoped read-only key when handing this to non-technical users, not your master admin key.

## License

Do whatever you want with it. MIT-ish. If it saves you time, great.
