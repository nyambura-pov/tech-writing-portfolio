# Case study: documenting the InSight clinical decision-support system

How I organized documentation for two different audiences of one computer-vision cataract triage tool.

## Summary

| | |
|---|---|
| **Project** | InSight cataract screening application |
| **Role** | Sole technical writer and documentation engineer |
| **Deliverables** | System overview, clinical operator guide, developer and API guide, OpenAPI specification |
| **Tools and standards** | MkDocs Material, Markdown, OpenAPI, Google Developer Documentation Style Guide, Git, GitHub Actions |
| **Audiences** | Frontline healthcare workers (operators) and software engineers (maintainers) |

## 1. The problem

InSight is a machine learning tool that screens eye (fundus) photographs for signs of cataracts in low-resource clinics. The PyTorch model and FastAPI backend worked, but there was no documentation:

* **Operators** had no instructions for capturing and uploading images, or for reading confidence scores and Grad-CAM heatmaps.
* **Engineers** had no API specification, dependency map, or local setup guide.

The challenge was to document one product for two audiences with opposite needs, without confusing either.

## 2. The audiences

| | Clinical operator | Backend engineer |
|---|---|---|
| **Who** | Nurse, triage assistant, community health worker | Full-stack developer, DevOps engineer, API consumer |
| **Goal** | Upload images, interpret risk tiers, export PDF referral reports | Deploy the containers, integrate the REST API, debug inference issues |
| **Technical comfort** | Low | High (terminal, Python, REST, JSON) |
| **What they need** | Visual, step-by-step tasks with screenshots | OpenAPI contracts, cURL examples, status code tables, architecture |

## 3. Information architecture

I split the content into three documents instead of one long page:

```text
InSight documentation
├── System overview (insight.md)
│   └── Capabilities, clinical safety limits, architecture
├── Clinical operator guide (user-guide.md)
│   └── Image checks, step-by-step workflow, interpreting results
└── Developer and API guide (developer-guide.md)
    └── Setup, endpoints, error contracts, schemas
```

Two decisions shaped the structure:

* **Visible medical disclaimers.** Warning callouts state that InSight is a decision-support aid, not a diagnostic authority.
* **Separation of concerns.** Model details such as ResNet-18 weights and learning rates stay in the developer guide, so operators never meet them.

## 4. Editorial and technical approach

**Style.** I followed the Google Developer Documentation Style Guide:

* Imperative, active verbs for steps ("Upload the fundus photograph," not "The photograph should be uploaded").
* Sentence-case headings.
* Captions and callouts on every screenshot, for screen-reader accessibility.

**Docs-as-code.**

* All content is Markdown in Git.
* A standalone OpenAPI specification (`specs/insight-openapi.yaml`) describes request parameters and validation models.
* MkDocs Material builds the site, and GitHub Actions publishes it to GitHub Pages.

## 5. Outcomes

The following are design outcomes:

* **Lower cognitive load for operators.** The workflow is presented as four visual steps with no code or command-line syntax.
* **Faster developer onboarding.** Copyable cURL commands let an engineer start the container and run a test prediction against `/predict`.
* **Maintainable docs.** Changes go through pull requests, the same as application code.
