# Troubleshooting guide

!!! info "Independent sample"
    This is an independent documentation sample written for a technical
    writing portfolio. It is not official Resend documentation.

This guide helps you diagnose and fix common problems when sending email with
the Resend API: failed requests, domain verification errors, webhook
problems, and rate limits.

Find your symptom in the table below, then follow the link to the matching
section. Each section lists the symptom, the likely cause, and the fix.

## Quick triage

| Symptom | Likely cause | First check | Go to |
|---|---|---|---|
| `401` when sending or retrieving | The API key is invalid or not set | Confirm `RESEND_API_KEY` is set in this terminal window | [Invalid API key](#401-api-key-is-invalid) |
| `403` when sending | The `from` domain isn't verified | Check the domain status in the dashboard | [Domain not verified](#403-domain-is-not-verified) |
| `404` when retrieving an email | The email ID is wrong | Compare the ID with the one the send request returned | [Email not found](#404-email-not-found) |
| `422` when sending | A required field is missing | Read `message`, which names the field | [Missing field](#422-missing-required-field) |
| `429` on bursts of requests | The request rate exceeds 2 per second | Count your requests per second | [Rate limits](#rate-limits-429) |
| Your webhook receives nothing | Your endpoint is unreachable or returns errors | Check the delivery log in the Resend dashboard | [No webhook events](#no-webhook-events-arrive) |
| Your app rejects a webhook as invalid | The signature check failed | Confirm you verify the raw request body | [Signature verification](#signature-verification-fails) |

## Read the error first

Every failed API request returns a JSON error with three fields:

```json
{
  "statusCode": 403,
  "name": "validation_error",
  "message": "The gmail.com domain is not verified. Please, add and verify your domain on https://resend.com/domains"
}
```

Different problems can share the same `name`. In testing, a missing field, an
invalid API key, and an unverified domain all returned `validation_error`.
Use `statusCode` to narrow down the problem, and read `message` for the exact
cause.

## API request errors

The errors in this section were reproduced against the live API.

### 401: API key is invalid

**Symptom**

```json
{
  "statusCode": 401,
  "name": "validation_error",
  "message": "API key is invalid"
}
```

**Cause:** The key is wrong, was deleted, was copied incompletely, or isn't
set in the terminal window you're running the command from.

**Fix**

**Step 1.** Check that the variable is set in the current window:

=== "Linux / macOS"

```bash
    [ -n "$RESEND_API_KEY" ] && echo "Key is set" || echo "Key is NOT set"
```

=== "Windows (PowerShell)"

```powershell
    if ($env:RESEND_API_KEY) { "Key is set" } else { "Key is NOT set" }
```

**Step 2.** If the key isn't set, set it again. An environment variable set in
one terminal window isn't available in another.

**Step 3.** If the key is set, create a new key in the dashboard under
**API Keys** and replace the old one. Don't commit API keys to Git.

### 403: Domain is not verified

**Symptom**

```json
{
  "statusCode": 403,
  "name": "validation_error",
  "message": "The gmail.com domain is not verified. Please, add and verify your domain on https://resend.com/domains"
}
```

**Cause:** The `from` address uses a domain that hasn't been verified for
your account. You can't send from domains you don't control, such as
`gmail.com`.

**Fix**

* **To test:** use `onboarding@resend.dev` as the `from` address.
* **To send from your own domain:** verify it. See
  [Domain authentication](#domain-authentication-and-dns).

### 404: Email not found

**Symptom**

```json
{
  "statusCode": 404,
  "name": "not_found",
  "message": "Email not found"
}
```

**Cause:** No email exists with that ID.

**Fix:** Compare the ID in your request with the `id` returned when you sent
the email. Copy and paste the ID rather than retyping it.

### 422: Missing required field

**Symptom**

```json
{
  "statusCode": 422,
  "name": "validation_error",
  "message": "Missing `subject` field."
}
```

**Cause:** The request body is missing a required field.

**Fix:** Add the field named in `message`. Every send requires `from`, `to`,
and `subject`, plus an `html` or `text` body.

## Domain authentication and DNS

Receiving mail servers check that the sender is authorized to use the domain
in the `from` address. Resend relies on two DNS-based checks:

* **SPF** lists the servers that are allowed to send email for your domain.
* **DKIM** adds a cryptographic signature to each message, which receivers
  verify against a public key published in your DNS.

Both are published as DNS records at your DNS provider. If the records are
missing or incorrect, the domain fails verification, and sending from it
returns a [`403` error](#403-domain-is-not-verified).

### Domain verification fails

**Symptom:** Sending returns a `403` error, or the domain shows as pending or
failed in the dashboard.

**Cause:** The DNS records provided by Resend are missing, mistyped, or have
not propagated yet. Propagation is the time it takes for a DNS change to
become visible across the internet.

**Diagnose**

**Step 1.** Open the Resend dashboard, go to **Domains**, and open your domain.
Copy the exact record names and values shown there.

**Step 2.** Check whether the record is live. Replace the name below with the
DKIM record name from your dashboard:

=== "Linux / macOS"

```bash
    dig TXT resend._domainkey.yourdomain.com +short
```

=== "Windows (PowerShell)"

```powershell
    Resolve-DnsName -Name "resend._domainkey.yourdomain.com" -Type TXT
```

**Step 3.** If the command returns nothing, the record isn't published yet or
contains a typo.

**Fix**

1. At your DNS provider (for example Cloudflare, Route 53, or Namecheap),
   create every record exactly as shown in the Resend dashboard. Some
   providers append your domain name automatically, so check for doubled
   names such as `resend._domainkey.yourdomain.com.yourdomain.com`.
2. Wait for propagation. Changes can take from a few minutes to 48 hours.
   Lowering the record's TTL (the time DNS servers cache it) to 300 seconds
   before you make changes shortens the wait for later updates.
3. Return to the dashboard and run verification again.

## Webhooks

A webhook is an HTTP request that Resend sends to your server when an event
occurs, such as an email being delivered or bouncing. Use webhooks instead of
polling `GET /emails/{id}` when you need to react to delivery events.

### No webhook events arrive

**Symptom:** You registered a webhook, but your server receives nothing.

**Likely causes**

* Your endpoint isn't reachable from the public internet, for example because
  it runs only on `localhost`.
* Your endpoint doesn't have a valid HTTPS certificate.
* Your endpoint responds slowly or returns an error status.

**Fix**

1. In the Resend dashboard, open **Webhooks** and check the delivery log for
   your endpoint. It shows the status code your server returned.
2. Confirm the endpoint URL is public and uses HTTPS. When developing
   locally, use a tunneling tool to expose your local server temporarily.
3. Return a `2xx` status quickly. Do slow work, such as database writes,
   after you respond.
4. See Resend's webhook documentation for how failed deliveries are retried.

### Signature verification fails

Resend signs each webhook so that you can confirm it came from Resend and was
not modified. The signature is an HMAC, a code computed from the request body
and a signing secret that only you and Resend know. Verification fails when
the body your code checks differs from the body Resend sent.

**Symptom:** Your server rejects webhooks as having an invalid signature. Your
application might respond with `401` or log a message such as "signature
verification failed". That response comes from your code, not from Resend.

**Likely causes**

* Your framework parsed the JSON body before you verified it. Re-serializing
  parsed JSON can change spacing or key order, which changes the signature.
* The signing secret in your environment doesn't match the secret for this
  webhook endpoint. Signing secrets start with `whsec_`.
* The verification step doesn't receive the signature headers.

**Fix**

1. **Verify the raw body.** Read the request body as unmodified bytes or
   text, and pass that to the verifier.
2. **Use a verification library** instead of writing the HMAC check yourself.
   Resend delivers webhooks through Svix, which provides libraries for this.
3. **Pass the three headers:** `svix-id`, `svix-timestamp`, and
   `svix-signature`.
4. **Check the secret.** Compare `RESEND_WEBHOOK_SECRET` with the signing
   secret shown for the endpoint in the dashboard.

Example handlers that verify the raw body:

=== "Node.js (Express)"

```javascript
    import express from "express";
    import { Webhook } from "svix";

    const app = express();

    // express.raw() keeps the body unparsed for this route only.
    app.post(
      "/api/webhooks",
      express.raw({ type: "application/json" }),
      (req, res) => {
        const wh = new Webhook(process.env.RESEND_WEBHOOK_SECRET);
        try {
          const event = wh.verify(req.body.toString(), req.headers);
          // Handle the verified event here.
          res.sendStatus(200);
        } catch (err) {
          res.sendStatus(400); // Signature check failed.
        }
      }
    );
```

=== "Python (FastAPI)"

```python
    import os

    from fastapi import FastAPI, HTTPException, Request
    from svix.webhooks import Webhook, WebhookVerificationError

    app = FastAPI()


    @app.post("/api/webhooks")
    async def handle_webhook(request: Request):
        raw_body = await request.body()  # unparsed bytes
        wh = Webhook(os.environ["RESEND_WEBHOOK_SECRET"])
        try:
            event = wh.verify(raw_body, dict(request.headers))
        except WebhookVerificationError:
            raise HTTPException(status_code=400, detail="Invalid signature")
        # Handle the verified event here.
        return {"ok": True}
```

## Rate limits (429)

The default rate limit is 2 requests per second per team. Requests over the
limit are rejected with `429 Too Many Requests`. The request itself is valid,
and it can succeed if you send it again at a lower rate.

**Symptom:** Bursts of requests return `429`, while the same requests succeed
when sent more slowly.

**Cause:** Your application sent more than 2 requests in one second.

**Fix**

* **Batch your sends.** One call to `POST /emails/batch` can carry up to 100
  emails and counts as a single request. See
  [Send batch emails](batch-emails.md).
* **Queue and pace requests.** Place outgoing emails in a background queue,
  such as Celery or BullMQ, and have workers send at a controlled rate.
* **Retry with backoff.** When you receive a `429`, wait and retry, waiting
  longer after each failed attempt. See [Retry safely](errors.md#retry-safely).

## When to contact support

If the problem remains after you've followed the steps above, collect the
following before you contact support:

* The email `id` returned by the API.
* The time of the request, including the time zone.
* The `statusCode`, `name`, and `message` from the error.
* The request you sent, with the API key removed.

Don't share your API key or webhook signing secret with anyone, including
support.

## Glossary

| Term | Definition |
|---|---|
| **API key** | A secret string that identifies your account in API requests. |
| **Bearer token** | A credential sent in the `Authorization` header. Resend API keys are sent this way. |
| **DKIM** | A cryptographic signature added to each email and verified against a public key in DNS. |
| **DNS** | The system that maps domain names to the records published for them. |
| **HMAC** | A code computed from a message and a shared secret, used to verify the sender and detect changes. |
| **Idempotency key** | A unique value sent with a request so that retries don't create duplicates. |
| **Propagation** | The time a DNS change takes to become visible across the internet. |
| **Rate limit** | The maximum number of requests allowed in a given period. |
| **SPF** | A DNS record that lists the servers allowed to send email for a domain. |
| **Webhook** | An HTTP request sent by a service to your server when an event occurs. |

## Next steps

* [Error codes](errors.md)
* [Send batch emails](batch-emails.md)
* [API reference](reference.md)