# Participant 1 (María): session notes

*Notes taken in Spanish during the session and summarized here in English. Quotes keep the original Spanish followed by an English translation. This is the only session with an exported event log.*

## Profile

| | |
|---|---|
| **Participant** | P1 · María |
| **Date / format** | 2026-10-21 · format not recorded |
| **Experience** | Agent working mainly through an office; posts in office groups and in groups of outside agents (from the [interview](../01-discovery/interviews.md)) |
| **Mainly works on** | Rentals, some sales |
| **Clients per month** | Not recorded |
| **Network** | Medium: office groups plus outside agents' groups |
| **Phone** | Not recorded |
| **Interviewed before?** | Presumably yes: same first name as a discovery interviewee (to confirm). If so, possible positive bias, and the profile above comes from that interview. |

## Tasks

Result: ✅ done alone · 🟡 done with help · ❌ not done

| # | Task | Result | Time (from the event log) | Where they hesitated |
|---|---|---|---|---|
| 1 | Find (2+ bedrooms, garage, pets, ≤ 4,000 Bs) and send to a client | ✅ | ~1.5 min (first filter → copy) | Did not use the price filter |
| 2 | Publish by pasting their last group message | ✅ | ~2.5 min (paste → published) | Asked for fields that don't apply to a house sale |
| 3 | Bring pending listings up to date | ✅ | A few seconds | — |
| 4 | Mark a listing as rented | 🟡 | ~1.5 min | First closed a different listing, probably by mistake (see below) |
| 5 | Share with their agents group | ✅ | Not in the exported log | — |

### Task details

**Task 1: find and send to a client**
- Filters used, in order: 2+ bedrooms → Alquiler → Departamento → Garaje → Mascotas (5 results). **The price filter was not used**, so the result set included options above 4,000 Bs.
- Opened one listing (Bs 2,900, Cala Cala) and copied it in **"Para mi cliente"** mode (`share_click.mode = client`): chose or accepted client mode without help.
- Moderator note: *"Buscar: todo bien"* (Search: all good).

**Task 2: publish by pasting**
- Message: a house for sale (original text not kept).
- "Detectamos **12 de 15**" (detected 12 of 15) → **≥ 5 fields detected: yes**.
- Fields detected: operation, type, zone, price, currency, bedrooms, bathrooms, built m², land m², garage, bank credit, paperwork, commission.
- Left in "Te falta" (missing): **pets, storage unit and utilities**. Two of the three are exactly the fields she said don't apply to a house sale.
- Did **not** open "Editar" (no `publish_review_expanded` event).
- Quote: *"Venta de casa me pregunta si tiene baulera, creo que no aplica. Mascotas no siempre, pero puede aplicar si es algún condominio o similar, pero aun así no creo."* (For a house sale it asks if it has a storage unit; I don't think that applies. Pets, not usually; it could apply to a gated community or similar, but even so I don't think so.)

**Task 3: bring listings up to date**
- Confirmed one listing individually, then used **"Confirmar todas"** (confirm all) for the 2 pending ones (`bulk: true, count: 2`).

**Task 4: mark as rented**
- **4 seconds after "Confirmar todas", she marked an *anticrético* listing as closed**; 1.5 minutes later she marked the rental the task asked for. The first close was most likely a mis-tap, which matches her own comment.
- Quote: *"Tal vez pedir una confirmación al marcar como vendida, fácil darle click por error."* (Maybe ask for confirmation when marking as sold; it's easy to click by mistake.)

**Task 5: share with agents**
- *"Compartir: todo bien"* (Sharing: all good).

**Exploration after the tasks:** tapped "Consultar por WhatsApp" on a listing (demo notice) and tried the zone filter with several zones.

## Closing questions

| Question | Answer |
|---|---|
| Better, the same or worse than today? Why? | **Better**: *"Me gusta que ahorra tiempo porque puedo usar y generar mensajes de WhatsApp que comparto en los grupos y porque puedo buscar propiedades más fácilmente."* (It saves time: I can generate WhatsApp messages to share in groups and search properties more easily.) |
| Would you use it for your next client? | **Yes** |
| What would have to happen for you to use it? | *"Si veo que la aplicación la usan otros asesores y realmente puedo encontrar opciones reales más rápido ahí."* (If I see other agents use it and I can really find real options faster there.) |
| Would you confirm your listings every week? What would make you do it? | *"Si hay mucha actividad en la app y encuentro las propiedades que necesito, entraría seguido y sería más fácil mantener mis propiedades actualizadas. Sería útil un recordatorio."* (With a lot of activity I'd come back often and keep them updated. A reminder would help.) |
| If it cost something, how much would make sense? | **Up to 100 Bs/month**, if it showed extra data *"como cuánta gente ve mi publicación"* (like how many people see my listing), to know whether publishing there gives real visibility. |
| What's missing or what would you change? | *"Cuando quiero publicar a veces me pregunta por cosas innecesarias o que no corresponden."* (When publishing it sometimes asks for unnecessary things or things that don't apply.) |

## Event log (`/#/debug`)

Exported on 2026-10-21 (session `5ff8l9lz`). Summary: `session_start` ×1 · `filter_applied` ×11 · `listing_viewed` ×2 · `share_click` ×1 (client mode, copy) · `publish_started` ×1 · `publish_parsed` ×1 (12 of 15, 0 suggested) · `publish_completed` ×1 (`missingAtStart` 3) · `confirm_available` ×3 (1 bulk of 2) · `mark_closed` ×2 · `contact_click` ×1.

## Moderator notes

- **Most important observation:** the mis-tap on "Marcar cerrada" right after confirming. It is a real risk, and today there is no way to reopen a closed listing.
- **Best quote:** *"Me gusta que ahorra tiempo…"* (I like that it saves time…)
- **Bugs or confusing moments:** the fields that don't apply (pets, storage) on a house sale.
- **Surprises:** she would pay for visibility metrics (views of her listing), not for the client-branded page assumed in the pricing hypothesis.
