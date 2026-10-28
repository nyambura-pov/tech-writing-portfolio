# Documenting InSight for two audiences

InSight is a cataract screening tool I built as my final-year computer science project. This case study explains how I documented it for clinic staff and for developers.

## Summary

| Item | Details |
|---|---|
| **Project** | InSight cataract screening application |
| **My role** | Built the system and wrote all of the documentation |
| **Deliverables** | System overview, clinical operator guide, developer and API guide, OpenAPI specification |
| **Tools and standards** | MkDocs Material, Markdown, OpenAPI, Google Developer Documentation Style Guide, Git, GitHub Actions |
| **Audiences** | Frontline healthcare workers (operators) and software engineers (maintainers) |

## 1. The problem

InSight screens eye (fundus) photographs for signs of cataracts. It uses a PyTorch model and a FastAPI backend and is meant for low-resource clinics. The system worked, but nothing in it would let anyone else use it:

* **Clinic staff** needed instructions for uploading images and for reading confidence scores and Grad-CAM heatmaps.
* **Developers** needed an API specification, a dependency overview, and a local setup guide.

The challenge was to write for two audiences with opposite needs without confusing either.

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

Three decisions shaped the structure:

* **Visible medical disclaimers.** Warning callouts state that InSight is a decision-support aid, not a diagnostic authority.
* **Separation of concerns.** Model details such as ResNet-18 weights and learning rates stay in the developer guide, so operators never meet them.
* **Writing about my own code.** Knowing the system well meant I could check each statement against the code. It also meant I had to guard against skipping steps that felt obvious to me, which is part of why the operator guide has no code or model terminology.

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

These are design outcomes, not measured results:

* **Lower cognitive load for operators.** The workflow is presented as four visual steps with no code or command-line syntax.
* **Faster developer onboarding.** Copyable cURL commands let an engineer start the services and run a test prediction against `/predict`.
* **Maintainable docs.** Changes go through pull requests, the same as application code.
