# Writing a troubleshooting guide for readers who are mid-problem

How I designed a troubleshooting guide that helps a developer find and fix a failure quickly.

## Summary

| Item | Details |
|---|---|
| **Project** | Troubleshooting guide for the Resend API |
| **Artifact** | `docs/resend/troubleshooting.md` |
| **Role** | Technical writer and documentation engineer |
| **Contents** | Triage table, error runbooks, DNS checks, webhook handlers, support checklist, glossary |
| **Tools** | Markdown, MkDocs Material, `dig`, PowerShell `Resolve-DnsName`, Node.js (Express), Python (FastAPI) |
| **Goals** | Find the right section within seconds, fix without contacting support, calm and neutral tone |

## 1. The problem

When something fails in production, the reader is stressed and wants a fix, not a lecture. Troubleshooting docs often fail them in three ways:

* **Organized by cause, not symptom.** The reader sees `403 Domain is not verified` but must already know the root cause to find the right page.
* **Filler and condescension.** Words like "simply," "just," and "obviously" add frustration without adding information.
* **Advice without commands.** "Verify your keys" doesn't help without a command to run or code to copy.

## 2. Structure

Each problem follows the same path:

```text
1. Triage table      Match the error code or symptom to a section
2. Cause             State why it happens
3. Fix               Platform-specific commands and code
4. Escalation        What to collect before contacting support
```

## 3. Key decisions

**A. Triage table first.**
The table sits at the top, before any narrative. It maps each symptom (`401`, `403`, `404`, `422`, `429`, silent webhooks) to a likely cause, a first check, and a link to its section.

**B. Neutral, blame-free tone.**
Instructions describe what the system does, not what the reader did wrong.

* Before: "You are calculating the signature wrong because you parsed the body."
* After: "Signature verification fails when the body your code checks differs from the body Resend sent."

Terms are defined once, in a sentence, where they first appear. There are no analogies or "in plain terms" boxes, because the readers are technical and mid-problem.

**C. Commands for both platforms.**
DNS and environment checks show Linux/macOS and PowerShell side by side (`dig` and `Resolve-DnsName`), so a Windows engineer isn't told to run a Unix tool.

**D. The raw-body webhook problem.**
Web frameworks parse JSON by default, which can change spacing or key order and break the HMAC signature. The guide shows how to read the unmodified body in Express (`express.raw()`) and FastAPI (`await request.body()`) and use a verification library.

## 4. Escalation

The guide ends with "When to contact support," which lists what to collect first:

* The email `id`.
* The time of the request, with time zone.
* The `statusCode`, `name`, and `message`.
* The request, with the API key removed.

It also warns never to share API keys (`re_...`) or webhook signing secrets (`whsec_...`).

## 5. Verification

**Tested against the live API:** the `401`, `403`, `404`, and `422` error messages shown in the guide are real output.

**Based on vendor documentation, not yet tested:** DNS verification, webhooks, and rate limits. The code samples follow Resend's and Svix's documented behavior but haven't been run.

## 6. Outcomes

The following are design outcomes:

* A reader can match a symptom to a fix without reading the whole page.
* Every fix includes something to run or copy.
* The support checklist helps readers file a report that support can act on.
