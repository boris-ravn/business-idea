# PRD v4: MVP for the pilot with real agents

> **For the implementing agent:** this document describes **what changes** to go from the prototype ([PRD v3](prd-v3-usability-results.md), [v2](prd-v2-feedback-iteration.md) and [v1](prd-v1-mvp.md)) to an MVP that a real group of agents can use during the pilot. Screens, flows and domain logic do not change: what changes is **where the data lives and who sees it**. The UI is still in Spanish (Bolivia): Spanish UI copy is quoted as is, with an English translation in parentheses.

- **Author:** Boris Martínez
- **Status:** Approved on October 22, 2026, with the 7 recommended decisions (§8)
- **Date:** October 22, 2026
- **Scope:** 6 changes (E1 to E6) so the product works with data shared between real agents, plus the pilot plan (§5). Open decisions are marked **⚠️ Decision** and summarized in §8.

---

## 1. Context: why the prototype is not enough

The usability test (October 21, 3 agents) said **continue**, and iteration 3 fixed what it asked for. But all 3 participants made their use conditional on the same thing: **other agents being there, enough listings, and listings kept up to date**. Only real use can answer that, which is Test 2 of the [business plan](../03-business/business-plan.md): a pilot in a real WhatsApp group.

The prototype cannot run that pilot:

| Today (prototype) | The pilot needs |
|---|---|
| Each phone stores its data in `localStorage`: what one agent publishes, nobody else sees. | A shared database: everyone sees everyone's listings. |
| No accounts: the profile is a name and a WhatsApp number on the phone. | Knowing who each agent is, so "Mis propiedades" (My listings) belong to them and only the owner can edit them. |
| 28 sample listings with fictitious agents and `DEMO_MODE`. | Real data only, and real contact through WhatsApp. |
| Sample photos (gradient + icon). | Real photos of each property. |
| Events stay on each phone and are copied by hand from `/#/debug`. | Everyone's events in one place, to measure the pilot signals. |
| The "Ver ficha" (view listing) link only opens on the same phone. | A link that opens the listing on any phone. |

**Principle for this iteration:** build **the minimum for the pilot to be real and measurable**, and do by hand (concierge) everything that can be done by hand. What the pilot decides (automatic reminders, payments, visibility metrics) gets built afterwards.

---

## 2. Changes

| ID | Change | Type |
|---|---|---|
| E1 | Shared data: backend and database | Code |
| E2 | Invitation-only access | Code |
| E3 | Real photos | Code |
| E4 | Server-side measurement | Code |
| E5 | Ready for real use: no demo, network states and privacy notice | Code and content |
| E6 | Concierge operation: initial loading, reminders and moderation | Operations (almost no code) |

### E1. Shared data: backend and database

**Solution.** Use **Supabase** (managed Postgres, authentication, file storage and an admin dashboard) instead of writing our own server. The frontend (Vite + React) stays the same and talks to Supabase directly through its client.

> ⚠️ **Decision 1: backend platform.** Recommended: **Supabase**, free plan, São Paulo region (the closest one). It covers everything the pilot needs without a server of our own, and the dashboard works as the admin tool (E6). Alternative: Firebase (similar, but without SQL, which makes the E4 metrics harder to compute).

**Data model** (a direct translation of `Listing` and `Agent`):

| Table | Main fields | Access rules (RLS) |
|---|---|---|
| `agents` | `id` (= authenticated user), `name`, `whatsapp`, `member_since`, `invited_by`, `accepted_terms_at` | All pilot agents can read name and WhatsApp. Each agent edits only their own row. |
| `listings` | The `Listing` fields (including `not_applicable`, `closed_at`), `agent_id`, `photos` (paths), `created_at`, `last_confirmed_at`, `status` | Everyone reads **active** listings. Each agent also reads their own closed ones. Only the owner creates, edits, closes or publishes again. |
| `events` | `name`, `at`, `session_id`, `agent_id`, `data` (jsonb) | Each agent can only insert their own events. Nobody reads them from the app; they are queried from the dashboard. |

**Changes in the app:**
- A new data layer (`src/data/`) replaces `storage.ts` and the store's persistence: loading the list, publishing, confirming, confirming all, changing the price, closing, publishing again, and their "Deshacer" (undo).
- The reducer and all the logic in `src/domain/` stay. Actions are applied optimistically (the screen changes instantly) and rolled back with a notice if the network fails.
- Confirmation dates (`last_confirmed_at`, `closed_at`) are set by the server, not the phone, so freshness doesn't depend on each agent's clock.
- A listing opens by its id from any phone (`/#/p/:id`), so the "Ver ficha" link in the text for agents works.

**Acceptance criteria.**
- [ ] What one agent publishes appears in another agent's list after a reload.
- [ ] An agent cannot edit, close or confirm another agent's listings, even by calling the API directly (tested against the RLS rules).
- [ ] "Confirmado hace N días" (confirmed N days ago) is computed from the server date.
- [ ] Every action in My listings, including its "Deshacer", persists in the database.
- [ ] If the network fails, the action is rolled back and "No se pudo guardar. Revisa tu conexión." (couldn't save, check your connection) is shown, without losing what the agent typed.
- [ ] The current domain tests still pass; the data layer has tests with a mocked client.

### E2. Invitation-only access

**Problem.** For "Mis propiedades" to belong to each agent and nobody to edit someone else's listings, we need to know who each agent is. But the pilot is a small, known group: open sign-up is not needed.

**Solution.**
- **Invitation only.** Boris shares a **pilot invitation link** in the group (or privately). Without an invitation, the app shows "Esta red es solo por invitación" (this network is invitation-only) and a contact.
- **Passwordless sign-in.** The agent signs in with a **magic link sent to their email**: they type their email, tap the link they receive, and stay signed in on that phone for a long time.
- **Profile on first sign-in.** Name and WhatsApp (the same sheet that today appears when publishing or sharing), plus accepting the privacy notice and terms (E5).
- Listings and the list are **only visible when signed in**. A "Ver ficha" link reaching someone outside the pilot shows them the invitation screen.

> ⚠️ **Decision 2: how agents sign in.** Recommended: **email magic link** (built into Supabase, no cost). Alternatives: a code by SMS or WhatsApp (more natural for agents, but needs a paid provider and more setup), or a personal link per agent with no email (simpler, but lost if they change phones). Risk to validate: whether agents use and check their email on their phone.
>
> ⚠️ **Decision 3: is the listing page public?** Recommended: **no**, signed-in only, because it shows the commission and the agent's WhatsApp. Alternative: a public listing page without commission or internal data, useful if agents want to send the link to their clients (today the "Para mi cliente" mode no longer includes the link).

**Acceptance criteria.**
- [ ] Without a session, no route shows listings; the sign-in or invitation screen is shown.
- [ ] With the invitation link, a new agent signs in, completes their profile and accepts the terms in under 2 minutes.
- [ ] The session persists after closing and reopening the phone's browser.
- [ ] An agent's access can be revoked from the dashboard.

### E3. Real photos

**Solution.**
- In Revisar (review), the photos block lets the agent **pick up to 5 photos from the gallery or take them with the camera**, instead of the sample photos.
- Photos are **resized on the phone** before upload (longest side 1280 px, JPEG ~80%), so they upload quickly over mobile data.
- They are stored in Supabase storage, linked to the listing. Only the owner can upload or delete them.
- Publishing without photos still works: the card shows the type icon, as today.
- The parser doesn't change: photos from WhatsApp messages don't get pasted along with the text, so the agent picks them separately.

**Acceptance criteria.**
- [ ] A 4 MB phone photo uploads in under 10 seconds on 4G and shows on the card and in the listing gallery.
- [ ] If a photo fails to upload, it can be retried or removed without losing the rest of the draft.
- [ ] The first photo in the order is the cover.

### E4. Server-side measurement

**Solution.**
- `logEvent` stays the same in the code, but writes to the `events` table (with `agent_id`) instead of `localStorage`. Without a network, events are queued and sent later.
- The existing `contact_click` event records the `agent_id` of the listing's owner, to measure contacts **between** agents.
- Saved SQL queries in the dashboard for each pilot signal (§5.3), per week and per agent.
- `/#/debug` and "Reiniciar demo" (reset demo) are removed from the pilot version.

**Acceptance criteria.**
- [ ] Each signal in §5.3 comes from a saved query, with nothing exported by hand.
- [ ] An event logged offline appears in the table once the network is back.

### E5. Ready for real use

- **No demo:** the 28 sample listings and agents are removed, `DEMO_MODE` becomes `false`, and "Consultar" (ask) opens WhatsApp with the agent's real number.
- **Network states:** loading the list (card skeletons), empty list ("Todavía no hay propiedades en la red. ¡Publica la primera!" = no listings in the network yet, publish the first one), connection error with "Reintentar" (retry).
- **Privacy notice and terms** (one page each, in plain language):
  - What data is stored (name, WhatsApp, email, listings, photos and app usage) and why (to make the network work and to measure the pilot).
  - That owner authorization and commission are declared by the agent and the app does not verify them.
  - That agents can ask for their data to be deleted when the pilot ends.
- **Final name and link:** the product name (today "Real Estate App") and the domain or subdomain to share.
- **Backup:** a weekly export of the database (the free plan has no automatic backups).

> ⚠️ **Decision 4: product name and domain for the pilot.** It can stay "Real Estate App" on a Vercel subdomain; a name of its own builds more trust when shared in the group.

**Acceptance criteria.**
- [ ] The pilot version contains no fictitious data or numbers.
- [ ] Offline, the list shows the error with "Reintentar", not a blank screen.
- [ ] The terms are accepted once and the date is recorded (`accepted_terms_at`).

### E6. Concierge operation (almost no code)

| Task | How it's done in the pilot | What gets built |
|---|---|---|
| **Initial loading** (avoid the cold start) | During onboarding, Boris sits (or calls) with each agent and they publish the agent's current listings together by pasting their messages. Listings are under the agent's name, not Boris's. Goal: **≥ 30 active listings** before opening the network to the whole group. | Nothing new: the Publish flow already exists. |
| **Reminders** | A manual WhatsApp message every Monday to anyone with listings unconfirmed for 7 days or more, with a link to My listings. | A saved query listing the agents to remind and their WhatsApp. |
| **Moderation** | Fix or close from the dashboard a listing that is duplicated, has wrong data or was reported by another agent. | Nothing: the Supabase dashboard. |
| **Support** | A WhatsApp chat with Boris for questions and problems. | A "¿Problemas? Escríbeme" (problems? message me) link in the menu. |

Automatic reminders are **not built yet**: the business plan says they are added only if weekly re-confirmation stays below 50% (§5.3).

---

## 3. What doesn't change

- Every screen and flow from PRD v1 to v3: search with filters, standard listing sheet with freshness, two-mode sharing, publish by pasting with the 4 sections, My listings with confirm all, closing with confirmation and publishing again.
- The logic in `src/domain/` and the parser (with their 111 tests).
- Owner authorization and commission are still self-declared.

---

## 4. Out of scope (candidates depending on what the pilot shows)

| Candidate | Built if… |
|---|---|
| Automatic reminders (WhatsApp or email) | Weekly re-confirmation stays < 50% with manual reminders. |
| Client requirements ("Busco…" = looking for) | Many requirements are posted in the group and agents ask for them in the app (a possible pivot, see the business plan). During the pilot they are counted by hand in the group. |
| Visibility metrics for agents (who saw or shared my listings) | The basis of the pricing hypothesis (50–60 Bs/month); validated after the pilot. |
| Payments and plans | After retention is validated. |
| Rental lifecycle (contract start and end dates) | Asked for by 1 of 3 participants; re-evaluated with real use. |
| AI (LLM) parser | If it detects < 5 fields in more than 30% of real messages. |
| Native app, push notifications, more cities | Not for the pilot. |

---

## 5. Pilot plan

### 5.1 Participants

- **One real WhatsApp group of agents in Cochabamba**, with its admin's permission.
- **10 to 20 invited agents**, ideally a mix of agents with and without a network (the target segment is agents **without** a strong network). The 3 usability-test participants are the first candidates.

> ⚠️ **Decision 5: which group and how many agents.** Recommended: a group where Boris already has contacts, and 10 to 20 agents. With fewer than 10, the network signals (contacting another agent) can hardly happen.

### 5.2 Duration and phases

| Phase | Duration | What happens |
|---|---|---|
| 0. Build and internal test | Until E1 to E5 are done | Implement, test with 2 accounts of our own, and run QA. |
| 1. Onboarding | 1 week | Invitations, sign-in, profile and concierge loading of listings (E6). The network opens to everyone once there are ≥ 30 active listings. |
| 2. Use | 3 weeks | Normal use. Manual reminders every Monday. No product changes except blocking bugs. |
| 3. Wrap-up | A few days | A short interview with each agent (what changed in how you work? would you pay? how much?) and analysis of the signals. |

> ⚠️ **Decision 6: length of the use phase.** The business plan says 2 weeks. Recommended: **3 weeks**, because the main signal is weekly (re-confirmation) and 2 weeks give only 2 measurements.

### 5.3 Signals and thresholds

Defined **before** starting and not changed after seeing the results. The first three come from the business plan (Test 2).

| Signal | How it's measured | Continue | Change course |
|---|---|---|---|
| Active listings re-confirmed at least once a week | `confirm_available`, price change or publish again, over that week's active listings | ≥ 50% | < 50% → add automatic reminders; if still low, pivot |
| Agents who publish at least 1 listing on their own (without concierge loading) | `publish_completed` after each agent's onboarding session (the date of each one is recorded) | ≥ 40% | < 20% |
| Agents who contact another agent through the app | `contact_click` on another agent's listings | ≥ 25% | Almost none |
| Agents active in the last week (opened the app on at least 2 days) | `session_start` per agent and day | ≥ 50% | < 25% |
| Verifying through WhatsApp despite freshness | Wrap-up interview: "did you still ask in the group whether a listing was available even when it said 'Confirmado hace 2 días'?" | Most trust the freshness | Most still verify everything → the value proposition fails (business plan: "what would make me stop") |

**Also recorded, with no threshold:** use of "Para mi cliente" and "Para asesores" (for my client / for agents), `field_marked_na` and `field_added_back` per matrix cell, undone closings (`close_undone`), and "Busco…" requirements posted in the group (manual count).

### 5.4 Pilot risks

| Risk | Mitigation |
|---|---|
| Cold start: agents join and there's nothing there | Concierge loading, and opening the network only at ≥ 30 listings. |
| Agents don't check email and can't sign in | Test sign-in with the first 3 agents before inviting the rest; have an alternative ready (Decision 2). |
| Sensitive data (phone numbers, third-party properties) | Invitation-only access, RLS, privacy notice and deletion on request. |
| Something breaks with real users | QA before opening, a support chat, and daily error monitoring during the first week. |
| The group admin doesn't allow it | Ask for permission first and present it as a tool for the group, not a competitor. |

---

## 6. Technical changes (summary for the agent)

| Item | Change |
|---|---|
| `src/store/storage.ts`, `store.tsx` | Replaced by `src/data/` (Supabase client and one function per action). The store loads from the server and applies optimistic actions. |
| `src/seed/`, `Debug.tsx`, `useResetDemo` | Out of the pilot build (the sample messages can stay for tests only). |
| `src/config.ts` | `DEMO_MODE = false`; environment variables for the Supabase URL and public key. |
| New routes | Sign-in (email), invitation, first-time profile, privacy notice and terms. |
| `ReviewStep` | Real photo picker with compression and upload (E3). |
| `logEvent` | Writes to `events` with an offline queue (E4). |
| Database | Versioned SQL migrations in the repo (tables, RLS and the §5.3 queries), so the database can be recreated. |
| Tests | Domain tests stay. New: the data layer with a mocked client, and a script that checks the RLS rules against a test database. |

> ⚠️ **Decision 7: repository.** Recommended: **keep the same repo** (`app/`), tag the current prototype (`prototype-v3`) and keep its Vercel deploy pointing at that tag as a demo, so it still works in presentations. Alternative: a new repo for the MVP.

---

## 7. Costs

On the Supabase and Vercel free plans, the pilot costs **0 USD**. The only possible costs are a custom domain (~10–15 USD/year, Decision 4) or sending codes by SMS or WhatsApp if that sign-in option is chosen (Decision 2).

---

## 8. Decisions (resolved)

Boris approved the 7 recommendations on October 22, 2026.

| # | Decision | Resolution |
|---|---|---|
| 1 | Backend platform | Supabase, free plan, São Paulo region |
| 2 | How agents sign in | Email magic link, with invitation |
| 3 | Is the listing page public? | No, signed-in only |
| 4 | Product name and domain | Stays "Real Estate App" on a Vercel subdomain; can change before the pilot opens |
| 5 | Group and number of agents | A group with existing contacts, 10 to 20 agents |
| 6 | Length of the use phase | 3 weeks (plus 1 week of onboarding) |
| 7 | Repository | Same repo, with a `prototype-v3` tag for the demo |

**Wireframes:** proposed only for the new screens: email sign-in, invitation, first-time profile with terms, photo picker, and the loading, empty and error states. The other screens don't change.
