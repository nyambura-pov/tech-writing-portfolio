# API reference

!!! info "Independent sample"
    This is an independent documentation sample written for a technical
    writing portfolio. It is not official Resend documentation.

This page describes the Resend API endpoints covered in this documentation
set. For a guided introduction, see [Send your first email](index.md).

## Overview

| Property | Value |
|---|---|
| Base URL | `https://api.resend.com` |
| Authentication | `Authorization: Bearer <API key>` |
| Content type | `application/json` |
| Default rate limit | 2 requests per second per team |

All requests must use HTTPS. For the machine-readable contract, see the
[OpenAPI specification](https://github.com/nyambura-pov/tech-writing-portfolio/blob/main/specs/resend-openapi.yaml)
or [download the YAML](../assets/resend-openapi.yaml).

## Endpoints

| Method | Path | Description |
|---|---|---|
| `POST` | [`/emails`](#send-an-email) | Send a single email. |
| `POST` | [`/emails/batch`](#send-batch-emails) | Send up to 100 emails in one request. |
| `GET` | [`/emails/{id}`](#retrieve-an-email) | Retrieve an email and its latest delivery status. |

## Send an email

Sends a single email to one or more recipients.

```
POST /emails
```

### Headers

| Header | Required | Description |
|---|---|---|
| `Authorization` | Yes | `Bearer <API key>` |
| `Content-Type` | Yes | Must be `application/json`. |
| `Idempotency-Key` | No | A unique string that prevents duplicate sends when you retry a request. |

### Request body

| Parameter | Type | Required | Description |
|---|---|---|---|
| `from` | string | Yes | Sender address, for example `Acme <billing@acme.com>`. Must use a verified domain, or `onboarding@resend.dev` for testing. |
| `to` | string or array | Yes | One recipient address, or an array of up to 50 addresses. |
| `subject` | string | Yes | The email subject line. |
| `html` | string | Conditional | The HTML body. Provide `html`, `text`, or both. |
| `text` | string | Conditional | The plain-text body. If you send only `html`, Resend generates a plain-text version. |
| `cc` | string or array | No | Carbon copy recipient addresses. |
| `bcc` | string or array | No | Blind carbon copy recipient addresses. |
| `reply_to` | string or array | No | Address or addresses that replies are sent to. |
| `headers` | object | No | Custom key-value pairs added to the email headers. |

### Example request

=== "cURL"

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

```python
    import os
    import requests

    response = requests.post(
        "https://api.resend.com/emails",
        headers={"Authorization": f"Bearer {os.environ['RESEND_API_KEY']}"},
        json={
            "from": "Acme <onboarding@resend.dev>",
            "to": ["delivered@resend.dev"],
            "subject": "Hello from the Resend API",
            "html": "<p>Congrats on sending your first email with <strong>Resend</strong>!</p>",
        },
    )
    print(response.status_code, response.json())
```

### Response

A successful request returns `200 OK`:

```json
{
  "id": "01a13c69-fa51-7d25-9158-ef0e1ac508a7"
}
```

| Property | Type | Description |
|---|---|---|
| `id` | string | The unique identifier of the email. Use it to [retrieve the email](#retrieve-an-email). |

A `200` response means Resend accepted the email for delivery. It does not
mean the email has been delivered yet.

### Errors

| Status | Cause |
|---|---|
| `401` | The API key is invalid. |
| `403` | The `from` address uses an unverified domain. |
| `422` | A required field, such as `subject`, is missing. |

See [Error codes](errors.md) for example messages.

## Send batch emails

Sends up to 100 emails in a single request.

```
POST /emails/batch
```

### Headers

Same as [Send an email](#send-an-email).

### Request body

An array of 1 to 100 email objects. Each object accepts the same parameters as
[Send an email](#request-body).

!!! warning "Batch limitations"
    * Maximum of 100 emails per request.
    * Attachments and scheduled sending are not supported in batch requests.

### Example request

=== "cURL"

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

### Response

A successful request returns `200 OK` with one `id` per email, in the same
order as the request:

```json
{
  "data": [
    { "id": "01a13c71-c142-788f-ab5c-011036e2c098" },
    { "id": "01a13c71-c142-7775-a562-3f5143e032db" }
  ]
}
```

| Property | Type | Description |
|---|---|---|
| `data` | array | One object per email, in request order. |
| `data[].id` | string | The unique identifier of that email. |

For a task-based walkthrough, see [Send batch emails](batch-emails.md).

## Retrieve an email

Returns the details and latest delivery status of an email you sent.

```
GET /emails/{id}
```

### Headers

| Header | Required | Description |
|---|---|---|
| `Authorization` | Yes | `Bearer <API key>` |

### Path parameters

| Parameter | Type | Required | Description |
|---|---|---|---|
| `id` | string | Yes | The `id` returned when the email was sent. |

### Example request

=== "cURL"

```bash
    curl https://api.resend.com/emails/01a13c69-fa51-7d25-9158-ef0e1ac508a7 \
      -H "Authorization: Bearer $RESEND_API_KEY"
```

=== "Python"

```python
    import os
    import requests

    email_id = "01a13c69-fa51-7d25-9158-ef0e1ac508a7"
    response = requests.get(
        f"https://api.resend.com/emails/{email_id}",
        headers={"Authorization": f"Bearer {os.environ['RESEND_API_KEY']}"},
    )
    print(response.json())
```

### Response

A successful request returns `200 OK`:

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

| Property | Type | Description |
|---|---|---|
| `object` | string | The object type. Always `email`. |
| `id` | string | The unique identifier of the email. |
| `from` | string | The sender address. |
| `to` | array | The recipient addresses. |
| `subject` | string | The subject line. |
| `html` | string or null | The HTML body, if one was sent. |
| `text` | string or null | The plain-text body. If you send only `html`, Resend generates this from it. |
| `cc`, `bcc`, `reply_to` | array or null | The addresses supplied for each field. `null` if none were set. |
| `created_at` | string | When the email was created, for example `2026-10-14 21:55:40.924000+00`. |
| `scheduled_at` | string or null | When the email is scheduled to send. `null` if it isn't scheduled. |
| `message_id` | string | The message ID assigned to the email. |
| `last_event` | string | The most recent delivery event. Values include `sent`, `delivered`, `delivery_delayed`, `bounced`, `complained` and `opened`. |

### Errors

| Status | Cause |
|---|---|
| `401` | The API key is invalid. |
| `404` | No email exists with that ID. |

See [Error codes](errors.md) for example messages.