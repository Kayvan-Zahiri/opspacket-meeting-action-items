# Sample Input — Meeting Transcript

Use this with the workflow’s **Sample Transcript (Test)** node, or POST it as `transcript` to the webhook.

## Meeting metadata

- **Title:** Weekly Ops Sync — March 10
- **Attendees:** Alex Rivera (eng), Jordan Lee (PM), Sam Chen (design)
- **Suggested destination:** `notion` (or `linear` / `both`)

## Transcript (paste as-is)

```
Meeting: Weekly Ops Sync — March 10
Attendees: Alex Rivera (eng), Jordan Lee (PM), Sam Chen (design)

Alex: I'll finish the API rate-limit patch by Friday. That's high priority — production is seeing 429s.
Jordan: Can you also write the runbook? Due next Wednesday.
Sam: I'll update the onboarding Figma by end of week. Medium priority.
Jordan: I'll schedule the customer interview for next Monday and send invites today.
Alex: No owner yet for the billing webhook retry — someone should pick that up before the 20th. Priority high.
Jordan: Action for me — draft the Q2 OKR doc, due April 1, priority medium.
```

## Webhook example body

```json
{
  "meeting_title": "Weekly Ops Sync — March 10",
  "destination": "notion",
  "transcript": "Meeting: Weekly Ops Sync — March 10\nAttendees: Alex Rivera (eng), Jordan Lee (PM), Sam Chen (design)\n\nAlex: I'll finish the API rate-limit patch by Friday. That's high priority — production is seeing 429s.\nJordan: Can you also write the runbook? Due next Wednesday.\nSam: I'll update the onboarding Figma by end of week. Medium priority.\nJordan: I'll schedule the customer interview for next Monday and send invites today.\nAlex: No owner yet for the billing webhook retry — someone should pick that up before the 20th. Priority high.\nJordan: Action for me — draft the Q2 OKR doc, due April 1, priority medium."
}
```

## Notes for extractors

- Absolute dates in this sample: **April 1**, **the 20th** (day-of-month only — model may leave null or infer if prompted; v1 prompt prefers null when ambiguous).
- Relative phrases: “by Friday”, “next Wednesday”, “end of week”, “next Monday”, “today” — without a calendar anchor in the prompt, **due_date may be null** by design (see system prompt rules).
- Unassigned item: billing webhook retry should have `owner: null`.
