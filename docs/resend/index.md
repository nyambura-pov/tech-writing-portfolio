# Send your first email with the Resend API

!!! info "Independent sample"
    This is an independent documentation sample written for a technical
    writing portfolio. It is not official Resend documentation.

**Time to complete:** about 5 minutes

In this quickstart, you will:

* Set your API key as an environment variable.
* Send a test email with cURL or Python.
* Capture the email `id` from the response.
* Check the delivery status of your email.

Resend exposes a REST API over HTTPS. Requests and responses use JSON, and
every request is authenticated with a Bearer token.

| Property | Value |
|---|---|
| Base URL | `https://api.resend.com` |
| Authentication | `Authorization: Bearer <API key>` |
| Content type | `application/json` |
| Default rate limit | 2 requests per second per team |

## Before you begin

You need:

1. A [Resend account](https://resend.com).
2. An API key. In the Resend dashboard, go to **API Keys** and create a key
   with **Full access**. Keys start with `re_`. Copy the key right away,
   because Resend shows it only once.
3. A sender address. To test without a custom domain, use
   `onboarding@resend.dev`.
4. For the Python examples, Python 3 and the `requests` library
   (`pip install requests`).

## Set your API key

Store your key in an environment variable so it never ends up in source
control. Run the command in the same terminal window you will use for the
next steps. The variable lasts only for that window, so set it again if you
open a new one.

=== "Linux / macOS"

```bash
    export RESEND_API_KEY="re_your_api_key"
```


=== "Windows (PowerShell)"

```powershell
    $env:RESEND_API_KEY="re_your_api_key"
```

## Step 1: Send an email

The recipient `delivered@resend.dev` is a Resend test address that simulates
a successful delivery, so you don't need a real inbox.

=== "cURL (Linux / macOS)"

```bash
    curl -X POST https://api.resend.com/emails \
      -H "Authorization: Bearer $RESEND_API_KEY" \
      -H "Content-Type: application/json" \
      -d '{
        "from": "Acme <onboarding@resend.dev>",
        "to": ["delivered@resend.dev"],
        "subject": "Hello from the Resend API",
        "html": "<p>Congrats on sending your first email with <strong>Resend</strong>!</p>"
      }'
```

=== "Python"

    Save this as `send_email.py`, then run `python send_email.py`.

```python
    import os
    import requests

    api_key = os.environ.get("RESEND_API_KEY")
    if not api_key:
        raise SystemExit("Set the RESEND_API_KEY environment variable first.")

    response = requests.post(
        "https://api.resend.com/emails",
        headers={
            "Authorization": f"Bearer {api_key}",
            "Content-Type": "application/json",
        },
        json={
            "from": "Acme <onboarding@resend.dev>",
            "to": ["delivered@resend.dev"],
            "subject": "Hello from the Resend API",
            "html": "<p>Congrats on sending your first email with <strong>Resend</strong>!</p>",
        },
    )
    print(response.status_code)
    print(response.json())
```

!!! tip "Using Windows PowerShell?"
    PowerShell's `curl` is an alias for a different command. Use the Python
    example, or run `curl.exe` with backtick (`` ` ``) line continuations.

## Step 2: Confirm the response

A successful request returns `200 OK` and the ID of your email:

```json
{
  "id": "01a13c69-fa51-7d25-9158-ef0e1ac508a7"
}
```

!!! note "Save the email ID"
    You need the `id` to check delivery status in the next step.

A `200` response means Resend accepted the email for delivery. It doesn't
mean the email has arrived yet. The next step checks that.

## Step 3: Check delivery status

Send a `GET` request to `/emails/{id}`. Replace the ID below with yours.

=== "cURL"

```bash
    curl https://api.resend.com/emails/01a13c69-fa51-7d25-9158-ef0e1ac508a7 \
      -H "Authorization: Bearer $RESEND_API_KEY"
```

=== "Python"

    Save this as `get_email.py`, then run
    `python get_email.py YOUR_EMAIL_ID`.

```python
    import os
    import sys
    import requests

    if len(sys.argv) < 2:
        raise SystemExit("Usage: python get_email.py <email_id>")

    response = requests.get(
        f"https://api.resend.com/emails/{sys.argv[1]}",
        headers={"Authorization": f"Bearer {os.environ['RESEND_API_KEY']}"},
    )
    print(response.status_code)
    print(response.json())
```

Example response:

```json
{
  "object": "email",
  "id": "01a13c69-fa51-7d25-9158-ef0e1ac508a7",
  "to": ["delivered@resend.dev"],
  "from": "Acme <onboarding@resend.dev>",
  "created_at": "2026-10-14 21:55:40.924000+00",
  "subject": "Hello from the Resend API",
  "bcc": null,
  "cc": null,
  "reply_to": null,
  "last_event": "delivered",
  "scheduled_at": null,
  "message_id": "<010001a13c69fbd4-f6c90015-97e9-4569-93cf-c5b787ae658f-000000@email.amazonses.com>",
  "html": "<p>Congrats on sending your first email with <strong>Resend</strong>!</p>",
  "text": "Congrats on sending your first email with Resend!"
}
```

`last_event` shows the most recent delivery event. For test addresses like
`delivered@resend.dev`, it can read `delivered` almost immediately. For other
recipients, it may read `sent` first. Run the request again after a few
seconds to see the update.

## Troubleshooting

| Status | Likely cause | Fix |
|---|---|---|
| `401` | Missing or invalid API key | Check that `RESEND_API_KEY` is set in this terminal window and copied in full. |
| `403` | The `from` domain isn't verified | Use `onboarding@resend.dev`, or verify your domain in the dashboard. |
| `404` | No email with that ID | Check that the `id` is copied correctly. |
| `422` | A required field is missing | Read `message`, which names the missing field. |
| `429` | Rate limit exceeded | Limit requests to 2 per second, then retry. |

For the full list, see [Error codes](errors.md).

## Next steps

* [Send batch emails](batch-emails.md)
* [API reference](reference.md)
* [Error codes](errors.md)