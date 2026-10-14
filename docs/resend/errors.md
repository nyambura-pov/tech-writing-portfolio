# Error codes

!!! info "Independent sample"
    This is an independent documentation sample written for a technical
    writing portfolio. It is not official Resend documentation.

When a request fails, the Resend API returns a non-2xx HTTP status code and a
JSON body that describes the problem. Use this page to identify the cause and
decide whether to fix the request or retry it.

## Error response format

```json
{
  "statusCode": 422,
  "name": "validation_error",
  "message": "Missing `subject` field."
}
```

| Field | Type | Description |
|---|---|---|
| `statusCode` | integer | The HTTP status code of the response. |
| `name` | string | The error category. Different problems can share the same `name`, so also check `statusCode` and `message`. |
| `message` | string | A human-readable explanation of what went wrong. |

!!! note "Check more than the error name"
    A missing field, an invalid API key and an unverified domain all return
    `name: "validation_error"`. Use `statusCode` to tell them apart, and read
    `message` for the specific cause.

## Error catalog

These errors were reproduced against the live API.

| Status | `name` | Example `message` | Cause | How to fix it |
|---|---|---|---|---|
| `401` | `validation_error` | `API key is invalid` | The API key is invalid. | Check that `RESEND_API_KEY` is set and copied in full. |
| `403` | `validation_error` | `The gmail.com domain is not verified. Please, add and verify your domain on https://resend.com/domains` | The `from` address uses an unverified domain. | Verify your domain, or use `onboarding@resend.dev`. |
| `404` | `not_found` | `Email not found` | No email exists with that ID. | Check the email `id`. |
| `422` | `validation_error` | ``Missing `subject` field.`` | A required field is missing. | Add the field named in `message`. |

Other errors can occur. For example, the API returns `429` when you exceed the
rate limit of 2 requests per second, and `5xx` when something fails on
Resend's side. Neither was reproduced for this page.

## Which errors to retry

| Status | Retry? | Why |
|---|---|---|
| `4xx` except `429` | No | The request itself is wrong. Retrying sends the same bad request. |
| `429` | Yes, after a delay | The request was valid but you exceeded a limit. |
| `5xx` | Yes, after a delay | The failure is temporary and on the server side. |

## Retry safely

Retrying a send can deliver the same email twice if the first attempt actually
succeeded. Send the same `Idempotency-Key` header on every attempt so Resend
treats the retries as one request. Increase the wait between attempts
(exponential backoff).

```python
import os
import time
import uuid

import requests

URL = "https://api.resend.com/emails"


def send_with_retry(payload, max_attempts=5):
    api_key = os.environ["RESEND_API_KEY"]
    idempotency_key = str(uuid.uuid4())  # same key for every attempt

    for attempt in range(max_attempts):
        response = requests.post(
            URL,
            headers={
                "Authorization": f"Bearer {api_key}",
                "Idempotency-Key": idempotency_key,
            },
            json=payload,
            timeout=10,
        )

        if response.status_code == 429 or response.status_code >= 500:
            time.sleep(2 ** attempt)  # wait 1, 2, 4, 8 seconds
            continue

        response.raise_for_status()  # raises for non-retryable 4xx errors
        return response.json()

    raise RuntimeError(f"Email not sent after {max_attempts} attempts.")
```

## Test error handling

Trigger an error on purpose to confirm your handling works:

| To trigger | Do this | Result |
|---|---|---|
| Missing field | Omit the `subject` field. | `422` |
| Invalid key | Set `RESEND_API_KEY` to a key that doesn't exist. | `401` |
| Unverified sender | Set `from` to an address at a domain you haven't verified. | `403` |
| Unknown email | Request `GET /emails/00000000-0000-0000-0000-000000000000`. | `404` |

## Next steps

* [Send your first email](index.md)
* [Send batch emails](batch-emails.md)
* [API reference](reference.md)