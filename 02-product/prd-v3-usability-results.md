# PRD v3: Adjustments after the usability test with real agents

> **For the implementing agent:** this document describes **only what changes** from [PRD v2](prd-v2-feedback-iteration.md) and [v1](prd-v1-mvp.md). Everything else stays the same. It is still a **prototype for testing**, with no backend and no login. The UI is in Spanish (Bolivia): Spanish UI copy is quoted as is, with an English translation in parentheses.

- **Author:** Boris Martínez
- **Status:** Draft v3, pending approval
- **Date:** October 21, 2026
- **Scope:** 3 small changes (D1 to D3) based on what 2 or more participants asked for. Larger items are out of scope (§5) or move to the pilot (§6).

---

## 1. Context: what the test showed

On October 21, the moderated test was run with **3 real agents** (María, Alejandro and Valeria). The full results are in [results.md](../04-validation/results.md).

**Verdict: continue.** All 7 signals defined in advance landed on the "continue" side: 3 of 3 published by pasting, 3 of 3 said it is better than how they work today and 3 of 3 would use it for their next client. Four of those signals rest on a single data point, because the event log was exported for only one session.

**What changes in this iteration:**

| ID | Priority | Finding | Evidence |
|---|---|---|---|
| D1 | P0 | Publishing asks for data that doesn't apply (storage unit or pets for a house sale). Solved with sections by listing type and a "No aplica" (not applicable) option the agent can adjust. | María and Valeria. In María's event log, 2 of the 3 fields left in "Te falta" (missing) were exactly pets and storage unit. Valeria suggests separating what always applies from what depends on the listing type. |
| D2 | P0 | Closing a listing is too easy, and it can't be reopened later. | María asked for a confirmation, and her event log shows a likely mis-tap: she closed an *anticrético* listing 4 seconds after confirming. Valeria points out that a rental becomes available again, and today it has to be published from scratch. |
| D3 | P1 | Nothing invites the agent to review what the parser detected. | Alejandro fears the parser will get things wrong and asks the app to prompt a review. This is in tension with the compact review (C2), so the change must not make it longer. |

**What doesn't change:** searching, sharing and bulk confirmation worked with no issues.

---

## 2. P0 changes

### D1. Ask only for the data that applies, with sections and "No aplica"

**Problem.** Today, which fields are relevant barely depends on context. For example, pets is asked for any house or apartment, including sales, and storage unit is asked for houses and commercial units. No fixed matrix covers every real exception: a building that doesn't allow pets, a house in a gated community with a storage unit.

**Solution.** The matrix sets **defaults**, and the agent can adjust any exception in one tap. The Review step is organized in **four sections**:

1. **Required:** operation, type, zone and price. Always visible while missing; they can't be removed.
2. **Recommended ("Te falta", missing):** what agents filter and ask about most for that listing type. Counts in "Detectamos X de Y" (we detected X of Y).
3. **Optional:** a collapsed block, **"Más datos (opcional) · N"** (more details, optional). Not marked as "Falta" and not counted in the total.
4. **Not applicable:** a collapsed block at the end, **"No aplica · N"**. It lists the fields that don't fit this listing, each with an **"Agregar"** (add) button.

**How a field moves:**
- Each recommended or optional field has a small **"No aplica"** button that moves it to section 4.
- "Agregar", in section 4, returns it to its default section: "Te falta" if it is recommended for that type, or "Más datos" if it is optional or not applicable by default.
- If the parser detected a value for a field that is not applicable by default (for example, "con baulera" in a house for sale), the field is shown in "Más datos" with its value. **A detected value is never discarded.**
- When the operation or type changes during review, fields are rearranged according to the matrix, but the agent's own changes are kept. The `growPending` rule still applies: a field that becomes recommended appears in "Te falta" and stays there once filled in.

**"No aplica" (not applicable) is not the same as "No".** For pets there are three different answers: *"Acepta mascotas"* (pets allowed), *"No acepta"* (not allowed, e.g., the building forbids them) and *"No aplica"* (not applicable). Therefore:
- The **listing sheet** (F2) shows **"No aplica"** for those fields, distinct from "Sin dato" (no data). It is still the same standard sheet for every listing, and it gives the other agent useful information.
- **Filters** treat "No aplica" the same as "Sin dato": a listing with pets set to "No aplica" does not appear when filtering by "Acepta mascotas".
- **Share texts** leave out fields that don't apply, just as they leave out fields with no data today.

**"Not applicable by default" is not the same as "never applies".** Some fields make no sense for certain types, such as bedrooms on a plot of land or a rental deposit on a sale. Those **never apply**: they don't appear in any section, not even under "No aplica", so it doesn't fill up with absurd options. Only the fields the matrix leaves out by convention can be added.

**Matrix** (broadly validated by Boris on October 21). **R** = recommended · **O** = optional · **N** = not applicable by default (can be added) · **—** = never applies.

| Field | House sale | House rent | House *anticrético* | Apt. sale | Apt. rent | Apt. *anticrético* | Land | Commercial unit / office |
|---|---|---|---|---|---|---|---|---|
| Bedrooms | R | R | R | R | R | R | — | — |
| Bathrooms | R | R | R | R | R | R | — | R |
| Built m² | R | R | R | R | R | R | N | R |
| Land m² | R | O | O | — | — | — | R | N |
| Garage | R | R | R | R | R | R | — | O |
| Pets allowed | N | R | R | **O** | R | R | — | — |
| Storage unit (*baulera*) | N | N | N | O | O | O | — | N |
| Utilities | O | O | O | O | O | O | R | O |
| Paperwork in order | R | O | R | R | O | R | R | O |
| Bank financing | R | — | O | R | — | O | R | R (sale) · — (rent) |
| Rental deposit | — | R | — | — | R | — | — | R (rent) · — (sale) |
| HOA fees (*expensas*) | — | O | O | — | R | O | — | O (rent) · — (sale) |
| Commission split | R | R | R | R | R | R | R | R |

Notes:
- **Pets for an apartment sale** is optional, because a building may not allow them. **For a house sale** it is "No aplica", but it can be added, for example for a house in a gated community.
- **HOA fees** remain a rental and *anticrético* field only, as in the current model. Recording HOA fees for sales would be a separate model change.

**Acceptance criteria.**
- [ ] With a house-sale message, "Te falta" doesn't ask for pets or storage unit. Both appear under "No aplica · N" and can be added.
- [ ] An apartment for sale shows "Acepta mascotas" under "Más datos (opcional)".
- [ ] An apartment for rent asks for pets, rental deposit and HOA fees under "Te falta".
- [ ] "Más datos (opcional)" and "No aplica" start collapsed and show how many fields they contain.
- [ ] Tapping "No aplica" on a field moves it to section 4; "Agregar" returns it to its default section. Neither move loses a value already entered or the focus of another field.
- [ ] Fields that never apply (for example, bedrooms on land) don't appear in any section.
- [ ] A detected value for an "N" field appears under "Más datos" with its value and is saved on publish.
- [ ] The listing sheet shows "No aplica" for fields marked that way, and "Sin dato" for fields left empty.
- [ ] With the "Acepta mascotas" filter, listings with pets set to "No aplica" don't appear.
- [ ] "Detectamos X de Y" counts only required and recommended fields.
- [ ] Switching from sale to rent moves pets to "Te falta", and it stays there once filled in.
- [ ] On a 360×800 viewport, the summary and the first "Te falta" field are still visible without scrolling (the C2 criterion).
- [ ] Tests for the matrix (one test per column) and for moves between sections.

### D2. Confirm before closing, and allow publishing again

**Problem.** "Marcar vendida/alquilada" (mark as sold/rented) closes the listing with one tap. There is an "Deshacer" (undo), but it lasts 5 seconds. After that, the listing stays under "Cerradas" (closed) with no way to reactivate it, even though a rental becomes available again when the lease ends.

**Solution.**
- **Confirmation:** tapping "Marcar alquilada", "Marcar vendida" or "Marcar cerrada" opens a sheet with the listing's name. For example: *"¿Marcar como alquilada? Departamento en Queru Queru (Bs 3.200 / mes). Ya no aparecerá en el listado."* (Mark as rented? Apartment in Queru Queru, Bs 3,200/month. It will no longer appear in the listings.) It has the buttons **"Sí, marcar alquilada"** (yes, mark as rented) and **"Cancelar"** (cancel). The toast with "Deshacer" stays.
- **Publish again:** each card in the Cerradas tab has a **"Volver a publicar"** (publish again) button. The listing returns to Active with **"Confirmado hoy"** (confirmed today), because if the agent reactivates it, it is available. Toast: *"Publicada de nuevo · Confirmado hoy"* (published again · confirmed today), with "Deshacer".
- **Listing sheet of one's own closed listing:** after "Ya no aparece en el listado." (no longer in the listings), add *"Puedes volver a publicarla desde Mis propiedades → Cerradas."* (You can publish it again from My listings → Closed), with a link.
- **Measurement:**
  - `mark_closed` records only confirmed closings.
  - New event `close_cancelled` when the confirmation sheet is cancelled: it measures mis-taps.
  - New event `listing_reopened`, with `daysClosed`.

**Acceptance criteria.**
- [ ] Tapping "Marcar alquilada" closes nothing until confirmed. "Cancelar" leaves everything as is and records `close_cancelled`.
- [ ] In Cerradas, "Volver a publicar" returns the listing to Active, first in the public list, with "Confirmado hoy". "Deshacer" returns it to Cerradas with its previous date.
- [ ] The listing sheet of one's own closed listing shows the link to Mis propiedades.
- [ ] Reducer tests for publishing again and undoing it.

---

## 3. P1 change

### D3. Invite a review of the detected data, without lengthening the review

**Problem.** The compact review (C2) hides the detected data behind "Editar" (edit). An agent who doesn't trust the parser gets no clear signal that they should check it.

**Solution.**
- The summary card title changes from "Detectamos X de Y datos" (we detected X of Y) to **"Revisa lo que detectamos (X de Y)"** (review what we detected).
- Below the summary, one line: *"Si algo no está bien, toca **Editar**."* (If something isn't right, tap Edit.) "Editar" looks like a button, not a link.
- No step or mandatory confirmation is added.

**Acceptance criteria.**
- [ ] The summary says "Revisa lo que detectamos" and shows the invitation to edit.
- [ ] On a 360×800 viewport, the summary and the first "Te falta" field are still visible without scrolling (the C2 criterion).

---

## 4. Model and measurement changes

| Element | Change |
|---|---|
| Field matrix | `relevantFields` returns each field's default level according to the D1 matrix: required, recommended, optional, not applicable or never applies. |
| `Draft` and `Listing` | New field `notApplicable: DraftField[]`, with the fields marked "No aplica" (by default or by the agent). Their value is saved as `null`. In data already stored in `localStorage`, a missing value is read as `[]`. |
| `Draft` | New field `addedFields: DraftField[]`, with the fields the agent added back from "No aplica" or that the parser detected even though they didn't apply by default. |
| `pendingAtStart` / `growPending` | Consider only recommended fields not marked "No aplica". Optional fields live in their own block. |
| Listing sheet (F2) | Shows "No aplica" for the fields in `notApplicable`. Fields that never apply stay hidden, as today. |
| Filters and share text | Treat "No aplica" the same as "Sin dato". |
| `field_marked_na` / `field_added_back` | New events, with the field, operation and type (D1). They show which cells of the matrix should be corrected. |
| Reducer | New action to publish again (`status: 'active'` and `lastConfirmedAt` = now) and to undo it (D2). |
| `close_cancelled` | New event (D2). |
| `listing_reopened` | New event, with `daysClosed` (D2). |

---

## 5. Out of scope (candidates for later)

- **Rental lifecycle (Valeria):** record when a rental or *anticrético* starts and ends, remind the agent when the property becomes available again, and prompt them to contact the tenant about renewing. It fits with D2: marking a listing as rented could optionally ask "¿Hasta cuándo?" (until when?). It is a new feature, not a usability fix, and it relies on reminders that need a backend.
- **Visibility metrics (María and Alejandro):** how many people viewed or interacted with my listing. It needs a shared backend and is the best candidate for the Pro plan (§7).
- **Reminders to confirm (María and Alejandro):** a WhatsApp reminder when a listing has gone more than 7 days without confirmation. Tested in the pilot (§6).
- **Minor items from the QA:** the old price inside the description after "Cambiar precio" (change price), the success screen text ("aparece primera en Mis propiedades"), and returning to Pegar (paste) from the sheet of a just-published listing.
- **Requirements board ("Busco…", looking for…)** and **LLM-based parser:** unchanged from v2.

---

## 6. Next experiment: pilot (not part of this PRD)

All 3 participants made their use conditional on the same thing: other agents being there, enough listings, and listings kept up to date. A usability test can't answer that. The next step is the **2-week pilot in a real WhatsApp group** (Test 2 in the [business plan](../03-business/business-plan.md)). It needs its own plan, because it requires:
- A **shared backend**, so agents can see each other's listings.
- **Reminders** to confirm availability.
- **Concierge loading** of the initial listings, to avoid the cold start.

---

## 7. Pricing (for the business plan, not the app)

Willingness to pay was 50, 60 and up to 100 Bs a month (median: 60). The proposal is to update the Pro plan hypothesis to **about 50 to 60 Bs a month**, built around **visibility metrics**, which is what two participants said they would pay for. The client-branded listing page becomes secondary.

---

## 8. Open decisions (before implementing)

1. **D1 matrix:** resolved. Boris broadly validated it on October 21; pets for an apartment sale became optional. Since the agent can adjust any field with "No aplica" and "Agregar", the `field_marked_na` and `field_added_back` events will show which cells to correct later.
2. **Wireframes:** proposed only for the review with the 4 sections and the "No aplica" and "Agregar" buttons (D1 and D3), the listing sheet with a field set to "No aplica" (D1), and the confirmation sheet with "Volver a publicar" (D2).
3. **Validating this change:** the proposal is a short test of tasks 2 and 4 with 2 or 3 agents (ideally new ones), exporting the event log in every session. The alternative is to validate it directly in the pilot.
