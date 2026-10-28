# Documenting an email API, from first call to first failure

How I structured a quickstart, reference, and troubleshooting set for a transactional email API, and verified it against the live service.

## Summary

| Item | Details |
|---|---|
| **Project** | Resend API documentation sample |
| **Role** | Technical writer and documentation engineer |
| **Deliverables** | Quickstart, API reference, batch guide, error catalog, troubleshooting guide, OpenAPI specification |
| **Tools and standards** | OpenAPI 3.1, MkDocs Material, Python (`requests`), cURL, Git |
| **Goals** | Under 5 minutes to a first successful call, no credentials in code, readers can resolve common failures themselves |

*This is an independent sample, not official Resend documentation.*

## 1. The problem

Email APIs have predictable failure points for new developers:

* **Slow onboarding.** Long setup and unclear prerequisites delay the first successful request.
* **Schema guesswork.** If the docs don't say whether `to` takes a string or an array, readers hit `422` errors.
* **Silent failures.** Missing DNS records, rate limits, and webhook signature mismatches fail in ways that are hard to trace.

The goal was a documentation set and OpenAPI specification that lets a developer authenticate, send an email, check its delivery status, and fix common failures without help.

## 2. Information architecture

Developers use API docs in three situations: getting started, looking something up, and fixing a problem. I gave each its own track:

```text
Resend documentation
├── Get started (index.md)
│   └── Prerequisites → API key → first send → check status
├── Look up (reference.md, batch-emails.md)
│   └── Endpoints → parameter tables → payloads → batch limits
├── Fix problems (errors.md, troubleshooting.md)
│   └── Error catalog → triage table → DNS and webhook fixes
└── Machine-readable contract (specs/resend-openapi.yaml)
    └── OpenAPI specification
```

## 3. Key decisions

**A. Faster first call with test addresses.**
Developers often give up when they must verify a domain before testing. The quickstart uses Resend's test sender `onboarding@resend.dev` and test recipient `delivered@resend.dev`, so no DNS changes are needed.

**B. Credentials stay out of code.**
Every snippet reads `RESEND_API_KEY` from an environment variable. The quickstart shows the Linux/macOS and PowerShell commands, and warns that PowerShell's `curl` is an alias for a different command.

**C. Accepted does not mean delivered.**
A `200 OK` from `POST /emails` means the email was queued, not delivered. The docs explain the two steps:

```text
POST /emails        →  200 OK with an id  (accepted)
GET /emails/{id}    →  last_event: sent → delivered  (outcome)
```

**D. Diagnostics with runnable commands.**
The troubleshooting guide pairs each explanation with a command or code sample, such as `dig` and `Resolve-DnsName` for DNS records, and raw-body handlers for webhook signatures.

## 4. Verification

I ran the documented requests against the live API and corrected my drafts to match.

**Tested:** `POST /emails`, `POST /emails/batch`, `GET /emails/{id}`, and the `401`, `403`, `404`, and `422` errors. Testing ran on Windows PowerShell using Python.

**What testing changed:**

* Empty `cc`, `bcc`, and `reply_to` fields return `null`, not `[]`.
* `created_at` is not ISO 8601 (`2026-10-14 21:55:40.924000+00`).
* The plain-text body is generated automatically when only `html` is sent.
* A missing field, an invalid key, and an unverified domain all return `name: "validation_error"`, so the error catalog tells readers to check `statusCode` and `message` too.

**Not yet tested:** rate-limit (`429`) behavior, webhooks, DNS verification, the `Idempotency-Key` header, and the cURL commands on Linux or macOS. Those sections follow vendor documentation.

## 5. Outcomes

The following are design outcomes:

* A reader can configure a key, send an email, and check its status in one sitting without a custom domain.
* The reference tables state types and allowed values, so readers don't have to guess at request formats.
* The OpenAPI specification can be used to generate SDKs, run contract tests, and render interactive docs.

