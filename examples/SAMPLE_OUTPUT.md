# Sample Output — Expected Tickets / Pages

Illustrative output from `SAMPLE_INPUT.md` after LLM extraction. Exact wording may vary by model; structure should match.

## Extracted JSON (shape)

```json
{
  "actions": [
    {
      "title": "Finish API rate-limit patch",
      "owner": "Alex Rivera",
      "due_date": null,
      "priority": "high",
      "notes": "Production seeing 429s; due mentioned as Friday (relative)"
    },
    {
      "title": "Write rate-limit runbook",
      "owner": "Alex Rivera",
      "due_date": null,
      "priority": null,
      "notes": "Requested by Jordan; due next Wednesday (relative)"
    },
    {
      "title": "Update onboarding Figma",
      "owner": "Sam Chen",
      "due_date": null,
      "priority": "medium",
      "notes": "Due end of week (relative)"
    },
    {
      "title": "Schedule customer interview and send invites",
      "owner": "Jordan Lee",
      "due_date": null,
      "priority": null,
      "notes": "Interview next Monday; invites today (relative)"
    },
    {
      "title": "Pick up billing webhook retry work",
      "owner": null,
      "due_date": null,
      "priority": "high",
      "notes": "Unassigned; before the 20th (ambiguous month/year)"
    },
    {
      "title": "Draft Q2 OKR doc",
      "owner": "Jordan Lee",
      "due_date": "2026-04-01",
      "priority": "medium",
      "notes": "Explicit due April 1; year may follow model/context — confirm in your env"
    }
  ]
}
```

> **Date caveat:** The pack’s system prompt tells the model not to invent dates. Relative deadlines often stay `null`. Absolute “April 1” may become `YYYY-04-01`; year depends on model/context — treat as best-effort and edit in Notion/Linear if needed.

---

## Notion pages (one row per action)

| Name | Owner | Due Date | Priority | Meeting |
|------|-------|----------|----------|---------|
| Finish API rate-limit patch | Alex Rivera | *(empty)* | high | Weekly Ops Sync — March 10 |
| Write rate-limit runbook | Alex Rivera | *(empty)* | *(empty)* | Weekly Ops Sync — March 10 |
| Update onboarding Figma | Sam Chen | *(empty)* | medium | Weekly Ops Sync — March 10 |
| Schedule customer interview and send invites | Jordan Lee | *(empty)* | *(empty)* | Weekly Ops Sync — March 10 |
| Pick up billing webhook retry work | *(empty)* | *(empty)* | high | Weekly Ops Sync — March 10 |
| Draft Q2 OKR doc | Jordan Lee | 2026-04-01 *(or empty/year-adjusted)* | medium | Weekly Ops Sync — March 10 |

---

## Linear issues (if `destination` is `linear` or `both`)

| Title | Priority (Linear) | Due date | Description includes |
|-------|-------------------|----------|----------------------|
| Finish API rate-limit patch | 2 (high) | — | Owner (from transcript): Alex Rivera; Meeting: … |
| Write rate-limit runbook | 0 / unset | — | Owner: Alex Rivera |
| Update onboarding Figma | 3 (medium) | — | Owner: Sam Chen |
| Schedule customer interview and send invites | 0 / unset | — | Owner: Jordan Lee |
| Pick up billing webhook retry work | 2 (high) | — | No owner line (or empty) |
| Draft Q2 OKR doc | 3 (medium) | 2026-04-01 if set | Owner: Jordan Lee |

Assignees are **not** auto-set from names in v1.0.0.

---

## Webhook success response (shape)

```json
{
  "ok": true,
  "created": 6,
  "results": [
    {
      "ok": true,
      "index": 1,
      "notion_page_id": "...",
      "notion_url": "https://www.notion.so/...",
      "linear_issue": null,
      "title": "Finish API rate-limit patch"
    }
  ]
}
```

When no actions are found: `{ "ok": true, "created": 0, "message": "No action items extracted" }`.
