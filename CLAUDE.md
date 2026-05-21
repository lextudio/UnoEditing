# uno-tools workspace instructions

## DevFlow — runtime UI inspection agent

DevFlow is an HTTP-based UI inspection and automation agent embedded in running applications. It exposes the live visual tree, screenshot capture, and input simulation over a local REST API.

### How the sample app registers it

`UnoRichText/src/LeXtudio.RichText.Sample` uses `builder.UseUnoDevFlowAgent()` in `App.cs` (or similar startup). Once the app is running the agent listens on **port 5500** by default.

### REST endpoints (base: `http://localhost:5500`)

| Endpoint | Method | Purpose |
|---|---|---|
| `/api/v1/agent/status` | GET | Agent metadata (name, framework, port) |
| `/api/v1/ui/tree` | GET | Full UI element tree as JSON |
| `/api/v1/ui/element?id=<id>` | GET | Single element by stable ID |
| `/api/v1/ui/screenshot` | GET | PNG screenshot of the app |
| `/api/v1/ui/tap` | POST | Click an element — body: `{"id":"<id>"}` |
| `/api/v1/ui/actions/scroll` | POST | Scroll — body: `{"id":"<id>","deltaX":0,"deltaY":600}` |

### How to diagnose with DevFlow

When the user says "use DevFlow to diagnose", do the following while the sample app is running:

1. **Find the port** — DevFlow does not always use 5500. Run `netstat -aon` and look for the app's PID listening on a TCP port, or try candidate ports by hitting `/api/v1/agent/status` until one responds. The process name in Task Manager or `tasklist` can help match the PID.
2. **Fetch the UI tree** — `GET http://localhost:<port>/api/v1/ui/tree` — look for the target element and note its `id`.
3. **Fetch a screenshot** — `GET http://localhost:<port>/api/v1/ui/screenshot` — save and display to confirm what is actually rendered.
4. **Inspect a specific element** — `GET http://localhost:<port>/api/v1/ui/element?id=<id>` — check properties like `IsVisible`, `ActualWidth`, `ActualHeight`, `Content`.
5. Cross-reference what the tree shows against what the code sets up to identify the root cause.

### Source location

`C:\Users\lextudio\source\repos\wpf-tools\wpf-labs\src\DevFlow`
