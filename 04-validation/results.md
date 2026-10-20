# Results: moderated test with real agents

**Status:** in progress · Participants completed: 0 of 3–5 · Last update: —

This test validates MVP v2 with real agents. The first round of feedback came from a heuristic walkthrough (see [PRD evolution](../02-product/prd-evolution.md)). The thresholds below were **defined before running the test** ([PRD v1 §12](../02-product/prd-v1-mvp.md), [PRD v2 §7](../02-product/prd-v2-feedback-iteration.md)) and must not be changed after seeing the results.

## Participants

| # | Experience | Mainly works on | Network | Format | Notes |
|---|---|---|---|---|---|
| P1 | | | | | `participant-1.md` (pending) |
| P2 | | | | | |
| P3 | | | | | |
| P4 | | | | | |
| P5 | | | | | |

## Task completion

✅ done alone · 🟡 done with help · ❌ not done

| Task | P1 | P2 | P3 | P4 | P5 | Done alone |
|---|---|---|---|---|---|---|
| 1. Find and send to a client | | | | | | _ of _ |
| 2. Publish by pasting | | | | | | _ of _ |
| 3. Bring listings up to date | | | | | | _ of _ |
| 4. Mark as rented | | | | | | _ of _ |
| 5. Share with agents | | | | | | _ of _ |

## Signals against the thresholds

| Signal | Source | Continue | Change course / revisit | Result | Verdict |
|---|---|---|---|---|---|
| Task 2 completed (publish by pasting) | PRD v1 | ≥ 60% of participants | < 40%, or they say they wouldn't use it | | |
| Parser detects ≥ 5 fields with their real message | PRD v1 | ≥ 60% of messages | < 30% → evaluate an LLM-based parser | | |
| "Better than how I do it today" | PRD v1 | ≥ 3 of 5 | ≤ 1 of 5 | | |
| "I'd use it for my next client" (yes/maybe) | PRD v1 | ≥ 3 of 5 | ≤ 1 of 5 | | |
| Task 1: choose or accept "Para mi cliente" without help | PRD v2 · C1 | ≥ 60% | < 40% | | |
| Task 2: finish the review without opening "Editar" | PRD v2 · C2 | ≥ 50% | They almost always open it → the parser is wrong too often | | |
| Task 3: use "Confirmar todas" | PRD v2 · C4 | ≥ 60% | They ignore it or don't find the pending listings | | |

Verdict: ✅ continue · ⚠️ between thresholds (watch) · ❌ change course / revisit

## Parser accuracy with real messages

| # | Fields detected | Wrong | Missed | Zone (detected / suggested / missing) | Unrecognized local expressions |
|---|---|---|---|---|---|
| P1 | _ of _ | | | | |
| P2 | | | | | |
| P3 | | | | | |
| P4 | | | | | |
| P5 | | | | | |

## Willingness to pay

| # | "If it cost something, how much would make sense?" |
|---|---|
| P1 | |

The pricing hypothesis is a Pro plan at about 70 Bs/month (see the [business plan §8](../03-business/business-plan.md)).

## Key findings

*Fill in after the last session: 3 to 5 findings, each with its evidence (who said or did what) and how many participants it applies to.*

1.
2.
3.

## Best quotes

*Translated into English, with the participant number.*

- 

## Decision

*Based only on the verdicts above:*

- **Continue / adjust / change course:**
- **Changes for the next iteration (PRD v3):**
- **Next experiment:** if the results say continue, run the 2-week live pilot in a real WhatsApp group ([business plan §9](../03-business/business-plan.md)) to test the riskiest assumption: whether agents keep their listings fresh.
