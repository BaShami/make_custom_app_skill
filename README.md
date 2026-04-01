# make_custom_app_skill
# make-module-engineer

A Claude AI skill that turns curl commands and API docs into production-ready Make.com Custom App modules — all seven files.

---

## What It Does

Give it a curl command or API endpoint description and it generates every file the Make Custom App SDK needs:

- `base.imljson` — shared URL, headers, error handling, log sanitization
- `connection/parameters.imljson` — connection input fields
- `connection/api.imljson` — connection test + credential storage
- `module/parameters.imljson` — user-facing input fields
- `module/api.imljson` — request, response mapping, pagination
- `module/interface.imljson` — output contract (what users see in the mapper)
- `module/samples.imljson` — example output for UI preview

It also tells you exactly which steps still require the Make SDK web UI (app creation, module shell, etc.).

---

## Why I Built It

Building Make custom apps involves a lot of repetitive, error-prone JSON. Getting the interface to match the API output, handling null safety correctly, and remembering IML quirks (1-based arrays, `ifempty`, `omit`) takes experience and costs time.

I wanted a way to go from "here's the API" to "here's all the code" without hand-crafting seven files every time — and without the common bugs that make modules run silently with empty output.

---

## Who It Helps

- **Non-developers** building custom app integrations
- **No-code/low-code teams** who need to connect APIs that don't have native Make modules
- **Agencies** building client integrations at scale
- Anyone who finds themselves writing the same `ifempty(trim(...), 'MISSING')` pattern for the tenth time

---

## How It Works

The skill is a system prompt loaded into CoPilot or Claude Code. It encodes:

- **Module type rules** — when to use Action vs Search vs Universal, what iterate/pagination require
- **IML reference** — key functions, null safety patterns, 1-based array indexing
- **File-by-file templates** — correct structure for every SDK file
- **Pagination patterns** — cursor, offset, page-number, link-header
- **RPC patterns** — dynamic dropdowns backed by API calls
- **Error handling patterns** — base-level, module-level, connection-level, HTTP 200 errors
- **Debugging checklist** — ordered diagnosis for empty output panels
- **Common mistakes table** — 10 traps with symptoms and fixes

When you describe an endpoint, AI uses all of this to generate complete, null-safe, review-ready JSON or the CoPilot inserts the code already into the files.

---

## How to Use / Import It

### Option A — Claude.ai Skills (Recommended)

1. Load the skill in your preferred IDE Claude or VS studio
2. Paste the CURL request and any additional information in your prompt 
3. Reference the skill if needed
4. AI should now trigger this skill automatically when you mention Make custom apps, IML, or paste a curl command

### Usage Example

```
Build me a Make module for this endpoint:

curl -X GET "https://api.example.com/v1/contacts?search=john&limit=50" \
  -H "Authorization: Bearer YOUR_API_KEY"

Response:
{
  "contacts": [
    { "id": "c_123", "name": "John Smith", "email": "john@example.com", "created_at": "2025-01-01T00:00:00Z" }
  ],
  "nextPage": "cursor_abc123"
}
```

Claude will output all seven files with correct types, null safety, cursor pagination, and a manual steps checklist.

---

## Screenshots

> - <img width="830" height="463" alt="image" src="https://github.com/user-attachments/assets/688019f2-a10d-4a7d-a192-fb7cd85e4384" />

> - <img width="908" height="489" alt="image" src="https://github.com/user-attachments/assets/8604dfbf-f71f-413f-b174-681d471c72cc" />

> - <img width="1163" height="445" alt="image" src="https://github.com/user-attachments/assets/c2c5c4a4-9663-491f-b16b-edd8e825b5c6" />


---

## Limitations

- **Manual SDK steps still required** — the skill cannot create the app shell, module shell, or connection in the Make SDK. It should tell you exactly what to do, but knowing AI it may not dont bet on it, you must do those steps yourself in the UI or via the Make REST API.
- **No OAuth support yet** — the templates cover API key and Bearer token auth. OAuth2 flows require additional connection configuration not covered here.
- **Response shape must be provided** — if you don't include a sample API response, Claude will make reasonable guesses at field names. Always include a real response example for accurate output.
- **Pagination detection** — cursor/page/offset pagination is inferred from the response shape. If the API uses a non-standard pattern, you may need to adjust the generated `pagination` block.
- **No live API testing** — the skill generates code; it doesn't validate it against a real API. Always test in the Make SDK after importing.

---

## What I Learned

- **IML has sharp edges** — 1-based array indexing, `omit` as a value, and the difference between `parameters.*` and `connection.*` are easy to get wrong and hard to debug. Encoding these as explicit rules (not just examples) makes a big difference.
- **Interface mismatches are the #1 silent failure** — a module can run successfully and return nothing visible if `interface.imljson` doesn't exactly match `api.imljson` output. The skill enforces this explicitly.
- **Skills work best with decision trees, not just templates** — knowing *when* to use Search vs Action, *when* to add pagination, and *when* to use `ifempty` vs direct mapping is more valuable than having correct boilerplate.
- **Prose instructions don't survive context** — the most reliable way to encode engineering knowledge for an LLM is structured reference tables and explicit rules, not paragraphs of explanation.

---

## License

This project is licensed under the **MIT License** — see [`LICENSE`](./LICENSE) for details.

> **Why MIT?** It's the most permissive and widely recognized open source license. Anyone can use, modify, and distribute this skill freely — including commercially — as long as they keep the copyright notice. If you want to prevent commercial use, consider `CC BY-NC 4.0` instead.
