# Real Estate App: a shared listing network for real estate agents in Cochabamba

**Other agents' listings, with confirmed availability, without leaving WhatsApp.**

Real estate agents in Bolivia close many deals together: when a client asks for something they don't have, they look for it among other agents' listings and split the commission. Today that happens in chaotic WhatsApp and Facebook groups. Interviews with real agents showed that the hard part is not *finding* listings but **verifying** them: is it still available, is the price current, what are the conditions? Real Estate App is a mobile-first prototype that tests a fix: visible freshness ("confirmed 2 days ago"), filters that match what clients really ask for, and **zero change of habits**. Agents publish by pasting the WhatsApp post they already write, and they share back to WhatsApp.

This repository documents the process, from the first idea to a second iteration of the MVP.

## Links

| | |
|---|---|
| **Live MVP** | [mvp-real-estate-app.vercel.app](https://mvp-real-estate-app.vercel.app/). It is designed for phones: open it on a phone or with a 360×800 viewport. The UI is in Spanish (Bolivia). |
| **MVP source code** | [github.com/boris-ravn/MVP-Real-Estate-App](https://github.com/boris-ravn/MVP-Real-Estate-App) |
| **First demo video (5 min)** | [Google Drive](https://drive.google.com/file/d/1LDSivIeA8RWx8hzJWXJuEHz3vo7leXAY/view?usp=sharing) |

> The live MVP shows iteration 2 once the latest changes are deployed. The first demo video shows iteration 1.

## The process at a glance

| # | Step | Result | Read |
|---|---|---|---|
| 1 | **Initial idea and assumptions:** an open "MLS" for independent agents | Trust was rejected as the riskiest assumption; **pain** was chosen instead | [Business plan §1, §4](03-business/business-plan.md) |
| 2 | **Customer discovery:** 3 interviews with real agents (*Mom Test* style) | The pain is **verifying**, not finding; the target is agents **without a strong network** | [Interviews](01-discovery/interviews.md) |
| 3 | **Product definition:** PRD v1 with scope, acceptance criteria and success thresholds | 7 features, 4 test tasks | [PRD v1](02-product/prd-v1-mvp.md) |
| 4 | **Wireframes and MVP v1:** low-fi screens approved, then built by an AI coding agent | Working mobile prototype | [Wireframes v1](02-product/wireframes/v1.html) · [Implementation plan](02-product/implementation-plan-v1.md) |
| 5 | **Walkthrough and first demo** | 11 feedback items; key insight: agents share with **two audiences** | [Business plan §5](03-business/business-plan.md#5-prototype-walkthrough-and-feedback) · [Demo video](https://drive.google.com/file/d/1LDSivIeA8RWx8hzJWXJuEHz3vo7leXAY/view?usp=sharing) |
| 6 | **Iteration 2:** feedback prioritized (P0/P1), PRD v2, wireframes v2, MVP v2 | 7 changes implemented and covered by tests | [PRD evolution](02-product/prd-evolution.md) · [PRD v2](02-product/prd-v2-feedback-iteration.md) · [Wireframes v2](02-product/wireframes/v2.html) |
| 7 | **Moderated test with 3 to 5 real agents** | In progress | [Validation](04-validation/README.md) · [Session guide](04-validation/session-guide.md) · [Results](04-validation/results.md) |

## How the idea changed

| Version | What I believed | What changed it |
|---|---|---|
| v0 | An open MLS for independent agents; competitors are RE/MAX and Century 21; the main risk is trust | Agents already share their clients' properties in open groups, so trust doesn't stop them |
| v1 | The real competitor is WhatsApp; the risk is whether the groups' chaos hurts enough | 3 real interviews |
| v2 | The pain is **verifying**; the segment is agents **without a strong network** | Prototype walkthrough |
| v3 | Paste-to-publish works, but sharing needs **two modes** (for agents / for my client) and the parser must speak the local language | Iteration 2 built this; the real-agent test is next |

## Repository contents

```
business-idea/
├── 01-discovery/
│   ├── interviews.md               Method, interview guide and transcripts (English)
│   └── interviews-original-es.md   Original Spanish transcript
├── 02-product/
│   ├── prd-v1-mvp.md               First PRD
│   ├── implementation-plan-v1.md   How MVP v1 was built, phase by phase
│   ├── prd-v2-feedback-iteration.md  Second PRD: changes after feedback
│   ├── prd-evolution.md            What changed from v1 to v2 and why
│   └── wireframes/                 v1.html and v2.html (open in a browser)
├── 03-business/
│   └── business-plan.md            Business plan and validation report
└── 04-validation/
    ├── README.md                   What the real-agent test answers
    ├── session-guide.md            Script (Spanish + English), tasks and closing questions
    ├── participant-template.md     Notes template for each session
    └── results.md                  Results against the thresholds and the decision (in progress)
```

## About the evidence

- **Real evidence:** the 3 interviews with agents in Cochabamba. All quotes come from them, translated from Spanish. Audio recordings are not published, and only first names are used.
- **Not real-user evidence:** the first prototype feedback came from a **heuristic walkthrough** in which an AI agent played the role of an end user. It is treated as a set of hypotheses, and the moderated test with real agents is the experiment that validates them.
- **Not included:** some earlier interviews were simulated and used only to draft hypotheses. They are not cited as evidence.
- **How it was built:** the PRDs, wireframes and code were produced with an AI coding agent working from my specifications. I approved every step (wireframes before code, PRD before wireframes).

## Status (October 2026)

- MVP v2 is implemented: two-mode sharing, a compact publish review, a local-language parser, "Confirm all", HOA fees, a "2+ bedrooms" filter and collaboration terms moved to the top of the listing.
- **Now:** moderated sessions with 3 to 5 real agents, with success thresholds defined in advance ([validation](04-validation/README.md)). After that comes a 2-week live pilot in a real WhatsApp group to test the riskiest assumption: **will agents keep their listings fresh?**

*Author: Boris Martínez*
