# Faith Njuguna

Technical writer and documentation engineer

I'm a technical writer with a computer science degree. I write for developers and for the people who use the software they build. This portfolio has three pieces: documentation for a clinical AI tool I built, a set of API docs for an email service, and a troubleshooting guide. I tested the examples in these docs against the running systems.

I'm looking for a full-time, remote technical writer or documentation engineer role.

[Download my resume](assets/Faith_Njuguna_Resume.pdf){ .md-button .md-button--primary }
[Contact me](contact.md){ .md-button }

## Selected work

### InSight: documentation for a clinical AI tool

InSight screens retinal photographs for signs of cataracts. I built it as my final-year computer science project, then wrote the documentation for the two groups who would use it: clinic staff and developers.

* A clinical operator guide that covers screening, reading the Grad-CAM heatmaps that show what influenced a result, and exporting referral reports.
* A developer guide with local setup and a reference for `POST /predict/`, with cURL and Python examples and the `422` and `500` errors.
* A system overview with the architecture and a clinical-use disclaimer.

Because I wrote the code, I could check every statement against it, and I ran the API requests with cURL and Postman. I kept model details in the developer guide and out of the operator guide.

[Overview](insight/insight.md) · [Operator guide](insight/user-guide.md) · [Developer guide](insight/developer-guide.md) · [Case study](insight/case-study.md)

### Resend API: quickstart, reference, and OpenAPI spec

An independent documentation sample for Resend, a transactional email API. I'm not affiliated with Resend. The set covers sending an email, sending a batch, and checking delivery status.

* A quickstart with cURL and Python examples, from creating an API key to checking delivery status.
* An API reference and a batch-sending guide.
* An error catalog with retry guidance.
* An OpenAPI 3.1 specification.

I ran every request against the live API and corrected my drafts where the real responses differed. Empty fields came back as `null` instead of empty arrays, and three different errors shared the name `validation_error`, so the error docs tell readers to check the status code and message as well.

[Quickstart](resend/index.md) · [API reference](resend/reference.md) · [Error codes](resend/errors.md) · [Case study](resend/api-case-study.md)

### Troubleshooting guide for the Resend API

A guide for when an API call fails. It opens with a table that matches a symptom (`401`, `403`, `404`, `422`, `429`, or a webhook that never arrives) to the section that explains the fix.

* Real error messages for `401`, `403`, `404`, and `422`, reproduced against the live API.
* DNS checks for domain verification, with commands for Linux/macOS and PowerShell.
* Webhook signature verification examples in Node.js and Python.
* A checklist of what to collect before contacting support.

Sections I haven't tested yet are noted in the case study.

[Troubleshooting guide](resend/troubleshooting.md) · [Case study](resend/troubleshooting-case-study.md)

## How I work

* **I test what I write.** I run the commands and API calls before I publish them, and I fix the docs when the system behaves differently from my draft.
* **I organize by what the reader is doing.** Getting started, looking something up, and fixing a problem need different pages.
* **I keep docs in the same workflow as code.** Markdown in Git, built with MkDocs Material, published to GitHub Pages, and written to the Google Developer Documentation Style Guide.

## Tools

Markdown, MkDocs Material, Git and GitHub, OpenAPI, REST APIs, cURL, Postman, Python (FastAPI), and the Linux and PowerShell command lines.

## Contact

[faith.njuguna.cse@gmail.com](mailto:faith.njuguna.cse@gmail.com) · [LinkedIn](https://www.linkedin.com/in/faith-njugunaaa) · [GitHub](https://github.com/nyambura-pov)