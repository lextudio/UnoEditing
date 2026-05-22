# uno-tools workspace instructions

## DevFlow - runtime UI inspection agent

DevFlow is an HTTP-based UI inspection and automation agent embedded in running applications. It exposes the live visual tree, screenshot capture, and input simulation over a local REST API.

### How the sample app registers it

`UnoRichText/src/LeXtudio.RichText.Sample` uses `builder.UseUnoDevFlowAgent()` in `App.cs`.

Current repo notes:

- The RichTextBox tab is selected by default when the sample loads.
- `UnoRichText/src/LeXtudio.RichText.Sample/LeXtudio.RichText.Sample.csproj` is currently set to `UseNuGetPackage=false`, so the sample uses the local `wpf-labs/src/DevFlow/LeXtudio.DevFlow.Agent.Uno` project instead of the published package.
- Do not assume DevFlow will use port `5500`. In this workspace it was observed on port `9223` on May 21, 2026.

### REST endpoints

Base URL: `http://localhost:<port>`

| Endpoint | Method | Purpose |
|---|---|---|
| `/api/v1/agent/status` | GET | Agent metadata, including the active port |
| `/api/v1/ui/tree` | GET | Full UI element tree as JSON |
| `/api/v1/ui/element?id=<id>` | GET | Single element by stable ID |
| `/api/v1/ui/screenshot` | GET | PNG screenshot of the app |
| `/api/v1/ui/tap` | POST | Activate an element. Body: `{"id":"<id>"}` |
| `/api/v1/ui/actions/scroll` | POST | Scroll an element. Body: `{"id":"<id>","deltaX":0,"deltaY":600}` |

### How to diagnose with DevFlow

When the user says "use DevFlow to diagnose", do the following while the sample app is running:

1. Find the port. DevFlow does not always use 5500. Run `netstat -aon` and match the app PID to a listening TCP port, or probe candidate ports with `/api/v1/agent/status`.
2. Fetch the UI tree with `GET http://localhost:<port>/api/v1/ui/tree` and locate the target element by `id`, `automationId`, `type`, or text.
3. Fetch a screenshot with `GET http://localhost:<port>/api/v1/ui/screenshot` to confirm what actually rendered.
4. Inspect a specific element with `GET http://localhost:<port>/api/v1/ui/element?id=<id>` to confirm visibility, size, text, and framework properties.
5. Cross-reference the live tree with the sample code and control implementation to identify the root cause.

### PowerShell note for screenshots

For binary screenshot responses, prefer `curl.exe` in PowerShell. `Invoke-WebRequest` may try to parse the PNG response as a document unless you force basic/binary handling.

Example:

`curl.exe -s http://localhost:<port>/api/v1/ui/screenshot -o %TEMP%\devflow-shot.png`

### Source location

`C:\Users\User\source\repos\UnoEditing\wpf-labs\src\DevFlow`
