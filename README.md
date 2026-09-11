# PDM-external_feedback_form

Client-facing feedback form wired to an iO n8n production webhook.

- Webhook (production): https://workflows.iobonzai.com/webhook/pdm-feedback-intake
- Method: POST (JSON)
- Authentication: None (MVP); minimal validation via `shared_secret` field.

## Shared secret for MVP validation

This form posts a `shared_secret` hidden field that you can validate in n8n (Set/IF node):

```
shared_secret = 6c8e5f3d2a9b47c1e0f4a7d3c9b28e1601fdd1a5a0c3e7b69fb24c5d8132e7a4
```

In the workflow, compare `{{$json.shared_secret}}` against this value and reject otherwise.

Note: Because this is a public form, this secret is visible to clients (view-source). It only deters casual misuse and should be replaced by a more robust approach later (e.g., short-lived tokens or server-issued HMAC).

## GitHub Pages

This repository serves the form from the `/docs` folder.

Steps to enable Pages:
1. Go to Settings → Pages
2. Source: "Deploy from a branch"
3. Branch: `main`, Folder: `/docs`
4. Save

The site will be available at:
- https://bolans1.github.io/PDM-external_feedback_form/

Robots are blocked (`docs/robots.txt`) and the page has `<meta name="robots" content="noindex, nofollow">`.

## n8n CORS / Allowed Origins

For MVP, Allowed Origins can remain `*`. After Pages is live, restrict to:
- `https://bolans1.github.io`

## JSON payload

The form sends a JSON body matching displayed fields, plus a `_meta` object:

```json
{
  "gever_rol": "Klant",
  "shared_secret": "<see above>",
  "coachee_naam": "...",
  "gever_naam": "...",
  "datum_feedback": "YYYY-MM-DD",
  "Expectations": "...",
  "Competence": "...",
  "Performance": "...",
  "Potential": "...",
  "overall_score": "...",
  "free_format": "...",
  "_meta": {
    "form_variant": "external",
    "page_url": "...",
    "user_agent": "..."
  }
}
```

## Next steps
- After E2E verification, tighten Allowed Origins and migrate to a more robust auth/validation scheme. Then proceed to SharePoint write and LLM extraction in n8n.
