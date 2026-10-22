# PRD evolution: from v1 to v4

How the product definition changed after the first prototype round, and why. Full documents: [PRD v1](prd-v1-mvp.md) · [implementation plan v1](implementation-plan-v1.md) · [PRD v2](prd-v2-feedback-iteration.md).

## Timeline

| Step | Artifact | What it decided |
|---|---|---|
| 1. Interviews | [3 real interviews](../01-discovery/interviews.md) | The pain is **verifying** listings (availability, price, conditions), not finding them. The target is agents **without a strong network**. |
| 2. PRD v1 | [prd-v1-mvp.md](prd-v1-mvp.md) | A mobile prototype built on 7 features: search with filters, standard listing sheet with visible freshness, contact through WhatsApp, **publish by pasting a WhatsApp post**, keep listings up to date, share back to WhatsApp, and in-app feedback. Success thresholds were defined before testing. |
| 3. Wireframes v1 → MVP v1 | [wireframes v1](wireframes/v1.html), [plan](implementation-plan-v1.md) | 6 low-fi screens approved before coding; then implemented by an AI coding agent working from the PRD. |
| 4. Walkthrough + first demo | [business plan §5](../03-business/business-plan.md#5-prototype-walkthrough-and-feedback), [demo video](https://drive.google.com/file/d/1LDSivIeA8RWx8hzJWXJuEHz3vo7leXAY/view?usp=sharing) | A heuristic walkthrough of the 4 test tasks produced 11 feedback items. |
| 5. Prioritization | This document | Feedback ranked by how much it threatens the riskiest assumptions: **P0** (needed before testing with real agents), **P1** (cheap search and readability fixes) and **P2** (deferred). |
| 6. PRD v2 | [prd-v2-feedback-iteration.md](prd-v2-feedback-iteration.md) | 7 changes (C1–C7) with acceptance criteria, new metrics and an updated test plan. |
| 7. Wireframes v2 → MVP v2 | [wireframes v2](wireframes/v2.html), [live MVP](https://mvp-real-estate-app.vercel.app/) | Changed screens approved before coding; all 7 changes implemented and covered by unit tests (85 tests passing). |
| 8. Real-agent test | [results](../04-validation/results.md) | 3 agents completed the tasks; all signals on the "continue" side. Publishing asks for fields that don't apply, and closing a listing is too easy and can't be undone later. |
| 9. PRD v3 | [prd-v3-usability-results.md](prd-v3-usability-results.md) | 3 changes (D1–D3): fields grouped by what applies to each listing type, with a manual "not applicable"; confirm before closing and publish again; a clearer prompt to review detected data. |
| 10. Wireframes v3 → MVP v3 | [wireframes v3](wireframes/v3.html) | Changed screens approved before coding; all 3 changes implemented, covered by tests (111 passing) and verified by a QA pass. |
| 11. PRD v4 | [prd-v4-pilot.md](prd-v4-pilot.md) | The prototype can't run a live pilot (data lives on each phone). 6 changes (E1–E6): shared data on Supabase, invitation-only access, real photos, server-side metrics, ready for real use, and concierge operation. Pilot: 10–20 agents, 1 week of onboarding and 3 weeks of use, with thresholds set in advance. |

## From feedback to change

**About the evidence:** the feedback came from a walkthrough in which an AI agent played the role of an end user. It was not a test with real agents. Each change is therefore a **hypothesis**, and the "Support from real interviews" column shows where a real agent said something similar.

| Feedback (in end-user terms) | Priority | Change in v2 | Support from real interviews | How it works in MVP v2 |
|---|---|---|---|---|
| "If I share a listing with my **client**, I don't want them to see the commission or that it belongs to another agent." | P0 | **C1** · Two-mode sharing | Valeria posts for end clients (Marketplace) and for agents (WhatsApp groups): two different audiences | The share sheet has "Para mi cliente" (for my client) and "Para asesores" (for agents). Client mode leaves out the commission, owner authorization, listing agent and link, and ends with the sender's own name and WhatsApp. It is the default on other agents' listings. |
| "Pasting my message was a surprise, but the review is too long: show me only what's missing." | P0 | **C2** · Compact publish review | — | The review step opens with a one-line summary of what was detected (expandable with "Editar"), then shows only the missing fields, with required ones first. |
| "It didn't understand '4 mil Bs' or the zone when I wrote 'Av. Pando'." | P0 | **C3** · Local-language parser | — | The parser understands "4 mil", "3,5 mil" and "$us 185 mil"; zones written with hyphens or joined together; and more ways of writing HOA fees. An avenue or landmark suggests a zone, which the agent then reviews. The avenue dictionary only has 3 validated entries so far, so "Av. Pando" is still pending. |
| "It says 2 listings need confirmation, but which ones? Let me confirm them all at once." | P0 | **C4** · Confirm all | Valeria: the biggest frustration is a property that *"was already rented or sold"* | Listings that need confirmation come first. The banner names them, and "Confirm all (N)" confirms them in one tap, with undo. |
| "HOA fees (*expensas*) are missing, and they matter for rentals." | P1 | **C5** · HOA fees | Valeria lists *expensas* among the essential filters | HOA fees are captured when publishing (this also fixed a bug that dropped them) and shown on cards, on the listing sheet and in shared texts. |
| "If I search for 2 bedrooms, show me 3 too." | P1 | **C6** · Minimum bedrooms | Nicolás: *"I want at least four bedrooms"* | The filter reads "1+ / 2+ / 3+ / 4+". |
| "Commission and owner authorization should be at the top, not at the bottom." | P1 | **C7** · Collaboration block moved up | — | The Collaboration block now comes right after Availability. |
| "I'd like to publish what my client is *looking for*." | P2 | Deferred | Nicolás: agents post their clients' requirements and others answer | Not built yet. If real agents use the product mostly for this, it becomes a pivot signal (business plan §9). |
| "I see very few listings per screen; photos take too much space." | P2 | Deferred | — | Not built yet. |
| "The feedback button covers information." / Product name placeholder | P2 | Resolved outside PRD v2 | — | The in-app feedback button ("Opinar") was removed: participant feedback is now collected in the closing questions of each [test session](../04-validation/session-guide.md). The product name was set to "Real Estate App". |
| "Filtering was fast and easy." / "Pasting and having it fill itself was a surprise." | — | Kept as is | — | No change. |

## What did not change

- The customer (agents in Cochabamba without a strong network), the core mechanism (a shared listing network) and the principle of working **with** WhatsApp instead of against it.
- The prototype constraints: no backend, no login and data stored on the device. The goal is still to learn from real agents, not to scale.

## New metrics in v2

| Event | What it tells us |
|---|---|
| `share_click.mode` | Whether agents share mostly with clients or with other agents |
| `publish_review_expanded` | Whether agents trust the summary or need to open and correct it |
| `publish_completed.missingAtStart` | How much the parser left for the agent to fill in |
| `confirm_available.bulk` | Whether "Confirm all" makes keeping listings fresh easier, which is the riskiest assumption |
