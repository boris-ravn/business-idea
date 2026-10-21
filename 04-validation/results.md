# Results: moderated test with real agents

**Status:** first round completed · Participants: 3 of 3–5 · Last update: 2026-10-21

This test validates MVP v2 with real agents. The first round of feedback came from a heuristic walkthrough (see [PRD evolution](../02-product/prd-evolution.md)). The thresholds below were **defined before running the test** ([PRD v1 §12](../02-product/prd-v1-mvp.md), [PRD v2 §7](../02-product/prd-v2-feedback-iteration.md)) and must not be changed after seeing the results.

**Limits of this evidence:**
- **Small sample:** 3 participants, the minimum of the planned 3–5.
- **Event log:** only P1's session was exported. Signals that depend on it (parser accuracy, use of "Para mi cliente", "Editar" and "Confirmar todas") have **1 data point**.
- **Notes:** for P2 and P3 they are a summary per screen, without times.
- **Possible bias:** P1 and P3 share first names with two discovery interviewees and are probably the same people, which could bias results upward.

## Participants

| # | Experience | Mainly works on | Network | Format | Notes |
|---|---|---|---|---|---|
| P1 | María · works through an office and outside agents' groups | Rentals, some sales | Medium | Not recorded | [participant-1.md](participant-1.md) (with event log) |
| P2 | Alejandro · not recorded | Not recorded | Not recorded | Not recorded | [participant-2.md](participant-2.md) |
| P3 | Valeria · about 1 year, previously independent | Rentals and sales | Weak to medium | Not recorded | [participant-3.md](participant-3.md) |

## Task completion

✅ done alone · 🟡 done with help · ❌ not done

| Task | P1 | P2 | P3 | Done alone |
|---|---|---|---|---|
| 1. Find and send to a client | ✅ | ✅ | ✅ | 3 of 3 |
| 2. Publish by pasting | ✅ | ✅ | ✅ | 3 of 3 |
| 3. Bring listings up to date | ✅ | ✅ | ✅ | 3 of 3 |
| 4. Mark as rented | 🟡 | ✅ | ✅ | 2 of 3 |
| 5. Share with agents | ✅ | ✅ | ✅ | 3 of 3 |

P1's task 4 is marked 🟡: the event log shows she first closed a different listing (an *anticrético*) 4 seconds after confirming, most likely a mis-tap, before closing the right one.

## Signals against the thresholds

| Signal | Source | Continue | Change course / revisit | Result | Verdict |
|---|---|---|---|---|---|
| Task 2 completed (publish by pasting) | PRD v1 | ≥ 60% of participants | < 40%, or they say they wouldn't use it | 3 of 3 (100%) | ✅ |
| Parser detects ≥ 5 fields with their real message | PRD v1 | ≥ 60% of messages | < 30% → evaluate an LLM-based parser | 1 of 1 measured (P1: 12 of 15) | ✅ (1 data point) |
| "Better than how I do it today" | PRD v1 | ≥ 3 of 5 | ≤ 1 of 5 | 3 of 3 | ✅ |
| "I'd use it for my next client" (yes/maybe) | PRD v1 | ≥ 3 of 5 | ≤ 1 of 5 | 3 of 3 "yes" | ✅ |
| Task 1: choose or accept "Para mi cliente" without help | PRD v2 · C1 | ≥ 60% | < 40% | 1 of 1 measured (P1) | ✅ (1 data point) |
| Task 2: finish the review without opening "Editar" | PRD v2 · C2 | ≥ 50% | They almost always open it → the parser is wrong too often | 1 of 1 measured (P1) | ✅ (1 data point) |
| Task 3: use "Confirmar todas" | PRD v2 · C4 | ≥ 60% | They ignore it or don't find the pending listings | 1 of 1 measured (P1) | ✅ (1 data point) |

Verdict: ✅ continue · ⚠️ between thresholds (watch) · ❌ change course / revisit

## Parser accuracy with real messages

| # | Fields detected | Wrong | Missed | Zone (detected / suggested / missing) | Unrecognized local expressions |
|---|---|---|---|---|---|
| P1 | 12 of 15 (house for sale) | None reported | Left in "Te falta": pets, storage unit, utilities (two of them don't apply to a house sale) | Detected | None reported |
| P2 | Not recorded | — | — | — | — |
| P3 | Not recorded | — | — | — | — |

## Willingness to pay

| # | "If it cost something, how much would make sense?" |
|---|---|
| P1 | Up to 100 Bs/month, if it showed visibility data such as how many people see her listings |
| P2 | About 60 Bs/month, if it works well |
| P3 | About 50 Bs/month: it saves time but isn't essential |

The pricing hypothesis is a Pro plan at about 70 Bs/month (see the [business plan §8](../03-business/business-plan.md)). Answers range from **50 to 100 Bs (median 60)**, so 70 Bs is inside the range but at the upper end. Two of three would pay for **visibility metrics** (who sees or interacts with my listings), which the current Pro hypothesis does not include.

## Key findings

1. **The core flows work and are perceived as better than today (3 of 3).** All tasks were completed, and all three said they would use it for their next client. Publishing by pasting was described as "quite simple" (P2), and searching as "fast and easy" (P2) and "all clear" (P3).
2. **Adoption depends on supply and freshness, not on the interface (3 of 3).** Every participant made their use conditional on other agents being there, enough matching listings and listings being up to date (P1, P2, P3). This confirms the main open risks: cold start and freshness upkeep.
3. **Reminders are expected for keeping listings fresh (2 of 3).** P1 and P2 would confirm weekly if the app reminded them; P3 would do it if she used the app daily and it gave results.
4. **Publishing asks for fields that don't apply (2 of 3).** P1 and P3. The event log backs it up: in P1's house sale, two of the three fields left in "Te falta" were pets and storage unit, which she said don't apply. P3 suggests separating what always applies from what depends on the listing type.
5. **Closing a listing is too easy and can't be undone later (2 of 3, plus behavioral evidence).** P1 asked for a confirmation, and her event log shows a likely mis-tap. P3 points out that a closed listing can't be reopened, which matters because rentals become available again.
6. **New need: the rental lifecycle (1 of 3).** P3 wants to track when a rental or *anticrético* started and ends, to re-offer the property or contact the tenant about renewing.
7. **Trust in the parser (1 of 3).** P2 fears wrong automatic parsing and asks the app to prompt a review of the data. This is in tension with the compact review (C2).
8. **Not in the plan: the price filter went unused (1 of 1 observed).** P1 completed task 1 without the price filter, although the task included a budget.

## Best quotes

- *"Me gusta que ahorra tiempo porque puedo usar y generar mensajes de WhatsApp que comparto en los grupos y porque puedo buscar propiedades más fácilmente."* (I like that it saves time: I can generate WhatsApp messages I share in groups and search properties more easily.) · P1
- *"Es rápida y fácil de usar, me gusta. Una mejora."* (It's fast and easy to use; I like it. An improvement.) · P2
- *"Lo usaría si hubiera una buena cantidad de propiedades que cumplan los distintos requisitos de mis clientes, y si los asesores tuvieran sus propiedades actualizadas."* (I'd use it if there were enough listings matching my clients' requirements and agents kept their listings up to date.) · P2
- *"Me gustaría […] saber cuándo se alquilaron, hasta cuándo, para que pueda gestionar y saber si ya debo empezar a ofrecerla nuevamente."* (I'd like to know when they were rented and until when, so I know whether to start offering them again.) · P3

## Decision

*Based only on the verdicts above:*

- **Continue.** All seven signals are on the "continue" side, with the caveat that four of them rest on a single data point.
- **Changes for the next iteration (PRD v3):**
  - Ask only for the fields that apply to each operation and type, and group the rest as optional (finding 4).
  - Ask for confirmation before closing a listing, and allow reopening closed listings (finding 5).
  - Prompt the agent to review the detected data without making the review longer again (finding 7).
  - Keep the rental lifecycle (finding 6) as a candidate for later.
- **Pricing:** revise the Pro hypothesis toward 50–60 Bs/month, built around visibility metrics.
- **Next experiment:** the 2-week live pilot in a real WhatsApp group ([business plan §9](../03-business/business-plan.md)). It tests what all three participants set as their condition (enough listings, kept fresh, with reminders), which a usability test cannot answer.
- **For future rounds:** export the event log in every session before resetting the demo, and record times and help given per task.
