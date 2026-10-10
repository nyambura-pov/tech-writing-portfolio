# Faith Njuguna

I'm a technical writer and documentation engineer with a background in computer science. I specialize in developer-facing documentation: REST API references, architecture guides, and docs-as-code workflows that help engineers build, integrate, and troubleshoot systems.

**Currently seeking:** technical writer and documentation engineer roles. Open to remote work.

I built this portfolio with MkDocs Material and Markdown. I manage it in Git and deploy it with GitHub Pages.

## Featured work

### [InSight clinical decision support documentation](insight/insight.md)

Developer onboarding, a clinical operator guide, and an API reference for an AI-driven cataract screening service.

* **The problem:** Clinical AI tools often run as opaque systems with isolated scripts. They lack reproducible local setups and clear interface contracts for integration engineers.
* **What I built:**
    * A developer quickstart that covers environment isolation and local service startup.
    * An architecture overview that explains the pre-validation gate and Grad-CAM heatmaps.
    * An endpoint reference for `POST /predict/` with multipart request examples in cURL and Python, binary image handling, and documented `422` and `500` error responses.
    * A clinical operator guide that walks nurses through screening, Grad-CAM interpretation, and referral report export.
* **My process:** I analyzed the PyTorch inference engine and the FastAPI backend. I verified request and response cycles with cURL and Postman. I wrote separate paths for clinical and engineering audiences.
* **Skills shown:** API reference design, architecture documentation, error state documentation, and Markdown.

### [Public API documentation: Resend Email API](resend/index.md)

Status: in progress.

A developer onboarding suite and OpenAPI-aligned reference for the Resend transactional email platform.

* **Target scope:** OpenAPI 3.0 specification authoring, Bearer token authentication, multi-language code samples (cURL and Python), and structured error schemas.
* **Focus:** The "zero to first successful call" journey, complete parameter definitions, and transactional email status lifecycle.
* **Skills shown:** REST API documentation, OpenAPI (OAS 3.0), Swagger tooling, developer quickstarts, and schema definition.

### [Production troubleshooting and runbooks](troubleshooting-runbooks.md)

Status: in progress.

A diagnostic runbook that helps engineers identify, isolate, and resolve production errors.

* **Target scope:** Root cause analysis templates, an error code catalog, and remediation steps.

## How I approach documentation

* **Engineering empathy:** Documentation should answer practical questions quickly. What is this service? How do I authenticate? What does a valid request look like? Why did my request fail?
* **Docs-as-code:** I keep documentation close to the source code. I author it in Markdown, manage it in Git, review it with peers, and publish it through continuous integration.
* **Cross-functional verification:** I interview subject matter experts (SMEs), compare requirements against QA test cases, and run commands locally to verify behavior before publishing.

## Core tooling

* **Documentation and markup:** Markdown, OpenAPI (Swagger), MkDocs Material, Git, GitHub, docs-as-code.
* **Technical foundation:** REST APIs, Postman, Python (FastAPI, PyTorch), HTTP status codes, Linux command line.

## Connect

* **GitHub:** [github.com/nyambura-pov](https://github.com/nyambura-pov)
* **LinkedIn:** [linkedin.com/in/faith-njugunaaa](https://www.linkedin.com/in/faith-njugunaaa)
* **Community:** Member of the Write the Docs community.