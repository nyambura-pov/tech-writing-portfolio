# Send batch emails

!!! info "Independent sample"
    This is an independent documentation sample written for a technical
    writing portfolio. It is not official Resend documentation.

Use the batch endpoint to send up to 100 emails in a single API call. Batching
reduces network overhead and helps you stay within the rate limit of 2
requests per second.

**Time to complete:** about 5 minutes

## Before you begin

Complete [Send your first email](index.md) first. You need an API key stored
in the `RESEND_API_KEY` environment variable in your current terminal window.

## How it differs from a single send

| | `POST /emails` | `POST /emails/batch` |
|---|---|---|
| Request body | One email object | An **array** of email objects |
| Maximum | 1 email | 100 emails |
| Response | `{ "id": "..." }` | `{ "data": [{ "id": "..." }, ...] }` |

!!! warning "Batch limitations"
    * Maximum of 100 emails per request.
    * Attachments and scheduled sending are not supported in batch requests.

## Step 1: Send a batch

=== "cURL (Linux / macOS)"

```bash
    curl -X POST https://api.resend.com/emails/batch \
      -H "Authorization: Bearer $RESEND_API_KEY" \
      -H "Content-Type: application/json" \
      -d '[
        {
          "from": "Acme <onboarding@resend.dev>",
          "to": ["delivered@resend.dev"],
          "subject": "Your order has shipped",
          "html": "<p>Your order is on its way.</p>"
        },
        {
          "from": "Acme <onboarding@resend.dev>",
          "to": ["delivered@resend.dev"],
          "subject": "Welcome to Acme",
          "html": "<p>Thanks for signing up.</p>"
        }
      ]'
```

=== "Python"

    Save this as `send_batch.py`, then run `python send_batch.py`.

```python
    import os
    import requests

    api_key = os.environ.get("RESEND_API_KEY")
    if not api_key:
        raise SystemExit("Set the RESEND_API_KEY environment variable first.")

    emails = [
        {
            "from": "Acme <onboarding@resend.dev>",
            "to": ["delivered@resend.dev"],
            "subject": "Your order has shipped",
            "html": "<p>Your order is on its way.</p>",
        },
        {
            "from": "Acme <onboarding@resend.dev>",
            "to": ["delivered@resend.dev"],
            "subject": "Welcome to Acme",
            "html": "<p>Thanks for signing up.</p>",
        },
    ]

    response = requests.post(
        "https://api.resend.com/emails/batch",
        headers={
            "Authorization": f"Bearer {api_key}",
            "Content-Type": "application/json",
        },
        json=emails,
    )
    print(response.status_code)
    print(response.json())
```

!!! tip "Using Windows PowerShell?"
    Use the Python example, or run `curl.exe` with backtick (`` ` ``) line
    continuations.

## Step 2: Read the response

A successful request returns `200 OK` with one `id` per email, in the same
order as your request:

```json
{
  "data": [
    { "id": "01a13c71-c142-788f-ab5c-011036e2c098" },
    { "id": "01a13c71-c142-7775-a562-3f5143e032db" }
  ]
}
```

## Step 3: Check an email's status

Use any `id` from the response with `GET /emails/{id}`:

```bash
curl https://api.resend.com/emails/01a13c71-c142-7775-a562-3f5143e032db \
  -H "Authorization: Bearer $RESEND_API_KEY"
```

The response shows the second email from the batch:

```json
{
  "object": "email",
  "id": "01a13c71-c142-7775-a562-3f5143e032db",
  "to": ["delivered@resend.dev"],
  "from": "Acme <onboarding@resend.dev>",
  "created_at": "2026-10-14 22:04:10.646000+00",
  "subject": "Welcome to Acme",
  "bcc": null,
  "cc": null,
  "reply_to": null,
  "last_event": "delivered",
  "scheduled_at": null,
  "message_id": "<010001a13c71c29a-956086db-4c51-46dc-a14a-0aa5292aba73-000000@email.amazonses.com>",
  "html": "<p>Thanks for signing up.</p>",
  "text": "Thanks for signing up."
}
```

## Troubleshooting

| Status | Likely cause | Fix |
|---|---|---|
| `401` | Missing or invalid API key | Check that `RESEND_API_KEY` is set in this terminal window and copied in full. |
| `403` | A `from` domain isn't verified | Use `onboarding@resend.dev`, or verify your domain in the dashboard. |
| `422` | An email in the array has a missing or invalid field | Read `message`, which names the problem. Check every object in the array. |
| `429` | Rate limit exceeded | Limit requests to 2 per second, then retry. |

For the full list, see [Error codes](errors.md).

## Next steps

* [API reference](reference.md)
* [Error codes](errors.md)
* [Send your first email](index.md)