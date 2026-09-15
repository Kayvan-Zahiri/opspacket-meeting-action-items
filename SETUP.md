# Setup Guide — Meeting → Action Items (~10 minutes)

OpsPacket workflow pack for n8n. Turns a pasted meeting transcript into Notion pages and/or Linear issues.

## What you need before starting

| Item | Required? | Notes |
|------|-----------|--------|
| n8n Cloud **or** self-hosted n8n (v1.0+) | Yes | Ability to import JSON workflows and create credentials |
| OpenAI-compatible LLM API key | Yes | OpenAI, Azure OpenAI, Groq, Together, Fireworks, etc. |
| Notion workspace + Internal Integration | For Notion path | Free Notion plan works for API |
| Linear workspace + API key | For Linear path | Optional if you only use Notion |
| ~10 minutes | Yes | Plus time to create a Notion database once |

**Cost note:** You pay your own LLM + Notion/Linear usage. This pack does not include API credits or hosting.

---

## Step 1 — Import the workflow (1 min)

1. Open n8n → **Workflows** → **⋯** / **Add workflow** → **Import from File** (or Import from URL / paste JSON, depending on your n8n UI).
2. Select `workflow/meeting-to-actions.n8n.json`.
3. Confirm the canvas shows nodes: Webhook, Normalize, LLM, Parse, Switch, Notion, Linear, etc.
4. Leave the workflow **inactive** until credentials are set.

**Assumption:** Import format matches n8n workflow export. If a node typeVersion warns on import, open the node and re-save — structure is intentional; minor version mismatches are common across n8n releases.

---

## Step 2 — LLM credential (2 min)

The node **LLM — Extract Action Items (OpenAI-compatible)** uses **HTTP Header Auth**.

1. n8n → **Credentials** → **Add** → **Header Auth** (or “HTTP Header Auth”).
2. Configure:
   - **Name:** `Authorization`
   - **Value:** `Bearer sk-YOUR_KEY` (include the word `Bearer` and a space for OpenAI-style APIs)
3. Attach this credential to the LLM HTTP Request node (replace the placeholder “OpsPacket LLM API Key”).
4. Optional env vars (n8n instance settings / `.env` for self-host):

```bash
LLM_BASE_URL=https://api.openai.com/v1
LLM_MODEL=gpt-4o-mini
```

- Azure OpenAI: set `LLM_BASE_URL` to your deployment chat-completions base URL (must end such that `.../chat/completions` is valid as built in the node).
- Groq example: `LLM_BASE_URL=https://api.groq.com/openai/v1` and a Groq model id.

**Common failure:** `401` → wrong key or missing `Bearer`.  
**Common failure:** `response_format` / `json_object` unsupported → remove that field from the JSON body in the LLM node, or switch model.

---

## Step 3 — Notion path (fully implemented) (~4 min)

### 3a. Create the database

In Notion, create a **full-page database** with these properties (names must match unless you edit the Notion node JSON):

| Property | Type | Notes |
|----------|------|--------|
| **Name** | Title | Action item title |
| **Owner** | Rich text | Person name from transcript (not Notion Person type in v1) |
| **Due Date** | Date | ISO date when extracted |
| **Priority** | Select | Options: `high`, `medium`, `low` |
| **Meeting** | Rich text | Meeting title echo |

### 3b. Integration + share

1. [Notion → My integrations](https://www.notion.so/my-integrations) → New integration → copy the **Internal Integration Secret**.
2. Open the database → **⋯** → **Connections** / **Add connections** → select your integration.
3. Copy the **database ID** from the URL:  
   `https://www.notion.so/workspace/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX?v=...`  
   (32-character hex; may include hyphens — either form usually works).

### 3c. n8n credential + env

1. Credentials → **Notion API** → paste Internal Integration Token. Attach to **Notion — Create Database Page**.
2. Set environment variable (recommended):

```bash
NOTION_DATABASE_ID=your_database_id_here
```

Or edit the default in **Normalize Input + Config**.

### 3d. Destination

- Webhook body: `"destination": "notion"` (default if omitted).
- Manual test sample already sets `destination` to `notion`.

---

## Step 4 — Linear path (optional, ~3 min)

The **Linear — Create Issue** node is wired for GraphQL `issueCreate`. Owner **names are not auto-assigned** to Linear users in v1.0.0 — they are appended to the issue description.

1. Linear → **Settings** → **API** → create a personal API key.
2. n8n → Header Auth credential:
   - **Name:** `Authorization`
   - **Value:** `lin_api_...` (**no** `Bearer` prefix — Linear expects the raw key)
3. Attach to **Linear — Create Issue**.
4. Team ID: Linear team → settings / API docs / GraphQL `teams` query. Set:

```bash
LINEAR_TEAM_ID=your-team-uuid
```

5. Use `"destination": "linear"` or `"both"`.

**Priority mapping:** high → 2, medium → 3, low → 4, missing → 0 (no priority).

---

## Step 5 — Run a test (1–2 min)

### Option A — Manual

1. Ensure **Manual Trigger — Test Run** connects through **Sample Transcript (Test)** → **Normalize…** (already wired).
2. Click **Test workflow** / **Execute**.
3. Check Notion (or Linear) for new rows/issues.
4. Inspect **Parse LLM JSON** output for extracted fields.

### Option B — Webhook

1. Activate the workflow (or use “Listen for test event”).
2. Copy the webhook URL (path: `opspacket-meeting-actions`).
3. POST JSON:

```bash
curl -s -X POST 'https://YOUR_N8N_HOST/webhook/opspacket-meeting-actions' \
  -H 'Content-Type: application/json' \
  -d '{
    "transcript": "Alex: Ship the rate-limit fix by Friday, high priority.\nJordan: I will draft the OKR doc due April 1.",
    "meeting_title": "Ops Sync",
    "destination": "notion"
  }'
```

Expected response shape: `{ "ok": true, "created": N, "results": [ ... ] }` (when using webhook response nodes).

---

## Environment variables summary

| Variable | Purpose | Default in workflow |
|----------|---------|---------------------|
| `NOTION_DATABASE_ID` | Target Notion database | Placeholder string |
| `LINEAR_TEAM_ID` | Linear team UUID | Placeholder string |
| `LLM_BASE_URL` | OpenAI-compatible base URL | `https://api.openai.com/v1` |
| `LLM_MODEL` | Model id | `gpt-4o-mini` |

n8n Cloud: set via instance **Variables** / environment if your plan supports them; otherwise hardcode in **Normalize Input + Config**.

---

## Common failures

| Symptom | Likely cause | Fix |
|---------|--------------|-----|
| Import warning on typeVersion | Different n8n version | Open/save affected nodes; upgrade n8n if a node type is missing |
| LLM 401 | Bad key / missing Bearer | Fix Header Auth value |
| LLM returns prose, Parse throws | Model ignored JSON mode | Strengthen system prompt or switch model; strip markdown fences (parser already tries) |
| Notion 404 / object_not_found | DB not shared with integration | Add connection on the database page |
| Notion 400 validation | Property names/types mismatch | Rename DB properties to match table above or edit JSON body |
| Notion select invalid | Priority option missing | Add `high` / `medium` / `low` to Priority select |
| Linear 401 | Used `Bearer` prefix | Use raw API key as Authorization value |
| Linear team error | Wrong `LINEAR_TEAM_ID` | Confirm UUID via Linear API |
| Empty actions | Vague transcript | Use clearer commitments; see `SAMPLE_INPUT.md` |
| Webhook 404 | Workflow inactive / wrong path | Activate workflow; path is `opspacket-meeting-actions` |
| Both destinations create twice into Aggregate | Expected fan-out | Filter Aggregate or run destinations separately if you need a single merged response |

---

## Security checklist

- Do not commit API keys into the workflow JSON.
- Restrict webhook access (n8n auth, reverse proxy, or secret query param you add yourself).
- Treat transcripts as sensitive — they may contain PII; your LLM provider will process them.

---

## After setup

- Point your meeting notes tool / Zap / bookmarklet at the webhook.
- Adjust the system prompt in the LLM node for your team’s jargon.
- Extend Linear with a `users` query if you want name → assigneeId mapping (not included in v1.0.0).

License and SKU: see `PRODUCT.md`.
