# Faith Njuguna

I am a Technical Writer and Documentation Engineer with a background in Computer Science. I focus on developer-facing documentation: REST API references, architecture guides, and docs-as-code workflows that help engineers build, integrate, and troubleshoot without friction.

This portfolio is built and version-controlled using **MkDocs Material**, **Markdown**, and **Git**, deployed via **GitHub Pages**.


## Featured Work

### [InSight Clinical Decision Support Docs](insight.md)
*Full-stack developer onboarding and API reference for an AI-driven cataract screening service.*

* **The Problem:** Clinical AI tools often operate as "black boxes" with isolated scripts, lacking reproducible local setups and clear interface contracts for integration engineers.
* **What I Built:** 
    * A 5-minute developer quickstart covering environment isolation and local service startup.
    * An architecture pipeline explaining pre-validation gating and Grad-CAM visual heatmaps.
    * Complete endpoint specifications for `POST /predict/` with real request payloads, binary image handling, and explicit HTTP error states (`400`, `422`).
* **My Process:** Analyzed the underlying PyTorch inference engine and FastAPI backend, tested local request/response cycles using `curl` and Postman, and structured the user and developer paths separately so neither audience gets bogged down in irrelevant detail.
* **Skills Shown:** API Reference Design, Technical Architecture Diagrams, Error State Documentation, Markdown.



### [Public API Documentation & Quickstart](about.md) *(In Progress)*
*A complete OpenAPI-based developer onboarding suite for a third-party REST service.*

* **Target Scope:** OpenAPI/Swagger spec parsing, authentication guides, code snippets in multiple languages, and common response workflows.
* **Process Highlight:** Writing for the "zero to first successful call" developer journey, prioritizing copyable code samples and accurate parameter tables.



### [Production Troubleshooting & Runbooks](about.md) *(In Progress)*
*A diagnostic runbook written to help engineers identify, isolate, and resolve service errors.*

* **Target Scope:** Root cause analysis templates, error code catalogs, and actionable remediation steps rather than vague descriptions.



## How I Approach Documentation

* **Engineering Empathy:** Documentation should answer practical developer questions quickly: *What is this? How do I authenticate? What does a valid payload look like? Why did it fail?*
* **Docs-as-Code:** I believe documentation belongs close to the code; managed in Git, written in Markdown, verified through pull requests, and automated through continuous deployment.
* **Cross-Functional Verification:** Good documentation isn't written in isolation. I interview Subject Matter Experts (SMEs), cross-reference requirements with QA test cases, and personally run the commands before publishing.



## Core Tooling

* **Documentation & Markup:** Markdown, OpenAPI (Swagger), MkDocs Material, Git/GitHub, Docs-as-Code.
* **Technical Foundation:** REST APIs, Postman, Python (FastAPI, PyTorch basics), HTTP Status & Error Handling, Terminal fundamentals.



## Connect

* **GitHub:** [github.com/nyambura-pov](https://github.com/nyambura-pov)
* **LinkedIn:** [linkedin.com/in/faith-njugunaaa](www.linkedin.com/in/faith-njugunaaa)
* **Community:** Active member in the **Write the Docs** Slack community.