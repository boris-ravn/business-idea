# Implementation plan: MVP Prototype "Shared Property Network"

> Based on [prd-v1-mvp.md](prd-v1-mvp.md). Follows the process in §10: wireframes first, **approval**, then phased implementation, verifying each flow on mobile (360×800).

## Phase 0 — Low-fi wireframes (*wireframer* skill) · ⏸ requires approval

**Deliverable:** `wireframes/index.html`, a single clickable HTML file (vanilla SPA, `wired-elements` via CDN, doodle icons as inline SVG, Patrick Hand font, grid background). Opens with a double-click, no build.

Decisions:
- **Vanilla HTML, separate from the app.** The wireframes are throwaway; they must not bring `wired-elements` or the "sketchy" style into the real React project.
- **360 px phone frame** centered on the page, because the product is mobile-first.
- **Skill rules scoped to `wireframes/`.** The skill asks to write its aesthetic rules into the project's `agents.md`/`CLAUDE.md`; I will record them with the explicit scope "applies only to `wireframes/`", so they do not contaminate the final UI (Tailwind).
- Real copy in Spanish (Bolivia) with plausible seed data; no lorem ipsum and no emojis (skill rule; emojis only appear in the final app, in the F6 text).

Screens (the 6 from §10) and navigation between them:

| # | Screen | Key content | Navigates to |
|---|---|---|---|
| 1 | Home with filters | Fixed chip bar (Operation, Type, Zone, Price, Beds), counter "12 propiedades" ("12 properties"), "Limpiar filtros" ("Clear filters"), cards with freshness badge and "Comparte 50%" ("Shares 50%"), "+ Publicar" ("+ Publish") button, floating "Opinar" ("Give feedback") button, access to "Mis propiedades" ("My properties") | 2, 3, 4, 6 |
| 2 | More filters (bottom sheet) | Toggles: Garage, Pets, *Baulera* (storage unit), Bank financing, Paperwork up to date, Basic utilities; "Ver 8 resultados" ("See 8 results") | 1 |
| 3 | Detail (standard listing sheet) | Gallery, blocks in the PRD order (Price → Availability → Features → Conditions → Collaboration → Agent → Description), fixed bar "Consultar por WhatsApp" ("Ask via WhatsApp") / "Compartir" ("Share"), `DEMO_MODE` notice | 1 |
| 4 | Publish · step 1 (paste) | Textarea with a sample WhatsApp message, "Prefiero llenarlo manualmente" ("I'd rather fill it in manually"), "Continuar" ("Continue") | 5 |
| 5 | Publish · step 2 (review) | Prefilled listing sheet: fields marked "detectado" ("detected") and missing ones highlighted "Falta: agrégalo…" ("Missing: add it…"), commission chips 30/40/50/other, authorization checkbox, picker of 6 placeholder photos, "Publicar" ("Publish") | 6 (via success) |
| 6 | My properties | Banner "3 propiedades necesitan confirmación" ("3 properties need confirmation"), Active/Closed tabs, actions "Sigue disponible" ("Still available"), "Cambiar precio" ("Change price"), "Marcar vendida/alquilada" ("Mark as sold/rented") | 1, 3 |

Outside the 6 wireframes (designed directly during implementation): publish success screen, name/WhatsApp sign-up, share sheet (F6), feedback form (F7), and `/debug`. If you prefer, I can add them as additional sketches.

**Checkpoint:** I present the wireframes to you and do not move on to Phase 1 until you approve or request changes.

---

## Phase 1 — Base project, model, and seed data

- `npm create vite` → React + TypeScript; Tailwind CSS; React Router with **HashRouter** (static deploy without configuring rewrites; `/#/debug` works on any host).
- Vitest for the parser tests.
- Structure:
  ```
  src/
    config.ts            PRODUCT_NAME, DEMO_MODE, FEEDBACK_WHATSAPP_NUMBER
    domain/              listing.ts (Listing type), freshness.ts, format.ts (USD/Bs price, share text, wa.me URLs)
    parser/              parseListing.ts + parseListing.test.ts
    store/               storage.ts (localStorage + seed loading + reset), events.ts (event log)
    seed/                listings.ts (25–30), agents.ts (8–10), messages.ts (8 messages)
    screens/             Home, Filters, Detail, Publish (steps 1–3), MyListings, Debug
    components/          ListingCard, FreshnessBadge, Chip, Toggle, BottomSheet, FeedbackButton, ShareSheet
  ```
- `Listing` model exactly as in §7. `lastConfirmedAt` dates in the seed are **relative to "today"** (e.g. `daysAgo(2)`), so the 40/35/25 freshness mix holds no matter when it is tested.
- Persistence in a single `store` module (React context + `useReducer`, no extra libraries) that reads from and writes to `localStorage`.

## Phase 2 — F1 Browse and search

- Filters in URL state or in the store; filtering logic as a pure function (`filterListings(listings, filters)`) with tests.
- Sorted by `lastConfirmedAt` desc. Counter and "Limpiar filtros" ("Clear filters").
- Empty state: computes which active filter, when removed, returns more results and suggests it ("Prueba quitando *Baulera*" — "Try removing *Baulera*").
- `filter_applied` event.
- Verification: acceptance criterion "Departamento + Alquiler + Cala Cala + Garaje + Mascotas" ("Apartment + Rent + Cala Cala + Garage + Pets") in ≤3 taps per filter.

## Phase 3 — F2 Detail and F3 Contact

- Standard listing sheet with the blocks in the PRD order; missing fields are shown as "Sin dato" ("No data") (reinforces the value of the standard listing sheet).
- `buildContactUrl(listing)` → `https://wa.me/<número>?text=<mensaje>`; with `DEMO_MODE` it first shows the notice with the message and the URL. Events `listing_viewed`, `contact_click`.

## Phase 4 — F4 Publish by pasting (key flow) + parser

- `parseListing(text): Partial<Listing> & { detected: string[] }`, deterministic (regex + dictionaries), normalizes accents and capitalization. Covers everything in §6, including `95k`, `$us 95.000`, `2.500 Bs`, "dpto", "c/garaje", "50/50".
- **TDD:** first the tests with the 8 messages in `seed/messages.ts` (minimum: operation, type, price, bedrooms, and zone) and edge cases; then the parser.
- Step 2 with chips and toggles; "detectado" ("detected") / "Falta" ("Missing") indicator. Name and WhatsApp sign-up the first time (localStorage).
- Step 3: inserts with `isMine: true`, `lastConfirmedAt: now`; success screen with the CTA "Compartir en tus grupos" ("Share in your groups").
- Events `publish_started`, `publish_parsed` (number of fields), `publish_completed`.

## Phase 5 — F5 My properties

- Active / Closed; "Sigue disponible" ("Still available") (`confirm_available`), "Cambiar precio" ("Change price") (numeric input in a sheet), "Marcar vendida/alquilada" ("Mark as sold/rented") (`mark_closed`, removed from the public listing).
- Banner for those that have gone >7 days without confirmation. The seed includes 2–3 old `isMine` properties so the banner appears during the test.

## Phase 6 — F6 Share

- `buildShareText(listing)` with the PRD format (tested). "Copiar texto" ("Copy text") (Clipboard API with fallback) and "Abrir WhatsApp" ("Open WhatsApp") (`wa.me/?text=`). `share_click` event.
- The "link to the listing sheet" uses `location.origin + '/#/p/<id>'`; since there is no backend, it only works on the same device. This is a known limitation of the prototype.

## Phase 7 — F7 Feedback, `/debug`, and Reset demo

- Floating "Opinar" ("Give feedback") button with the 3 questions; builds the message with answers + session event summary. With `FEEDBACK_WHATSAPP_NUMBER` empty, it shows the text to copy. `feedback_sent` event.
- `/#/debug`: event table and "Reiniciar demo" ("Reset demo") button. Also via a long press on the logo.

## Phase 8 — Wrap-up

- Mobile quality pass: 360×800, touch targets ≥44 px, AA contrast, no console errors, lightweight production build (no heavy UI libraries; photos as gradient + SVG icon).
- Walk through the 10 acceptance criteria in §11 and the 4 user test tasks (§12) end to end.
- README: run, reset the demo, configure the feedback number, deploy on Vercel/Netlify (I will not deploy without your confirmation).

## Verification per phase

Each phase ends with: `npm run test` green, `npm run build` without errors, and the flow walked through in the built-in browser with a mobile viewport before moving on to the next.

## Assumptions and open questions

1. **Product name:** stays as `[NOMBRE_PRODUCTO]` in `config.ts` until you define it. (It was later set to "Real Estate App".)
2. **Should have** (Favorites, Requirements): outside this plan; only if you ask me after Phase 8.
3. **Freshness colors** (green/amber/gray) apply in the final app; in the wireframes, which are grayscale, they are distinguished with icon + text.

---

## Status at close (2026-10-15)

Phases 0 to 8 complete. App in `app/` (see `app/README.md`) and wireframes in `wireframes/index.html`.

Acceptance criteria (§11), verified on a 360×800 viewport:

| # | Criterion | Result |
|---|---|---|
| 1 | Apartment + Rent + Cala Cala + Garage + Pets, ≤3 taps per filter | ✅ 3 correct results (2 taps per chip; 3 for Zone) |
| 2 | Freshness badge with the correct color | ✅ green ≤3 days, amber 4–14, gray >14 |
| 3 | Correct `wa.me` URL with prefilled message (DEMO_MODE) | ✅ `wa.me/59170000001?text=Hola Carla…` |
| 4 | Parser: operation, type, price, bedrooms, and zone in the 8 messages; tests green | ✅ (for land and commercial spaces, bedrooms are not required because they do not apply) |
| 5 | Published property appears first, "Confirmado hoy" ("Confirmed today"), and in My properties | ✅ |
| 6 | "Sigue disponible" ("Still available") updates freshness; "Marcar vendida" ("Mark as sold") removes it from the listing | ✅ (with Undo) |
| 7 | "Compartir" ("Share") produces the F6 text and copies it | ✅ |
| 8 | Feedback opens WhatsApp or shows the text with answers and events | ✅ (no number: prototype notice + message; answers in /debug) |
| 9 | "Reiniciar demo" ("Reset demo") restores the seed and clears the events | ✅ |
| 10 | No backend and no console errors | ✅ also tested with the production build |

Pending for Boris: define `PRODUCT_NAME` in `app/src/config.ts` and deploy (see README). `FEEDBACK_WHATSAPP_NUMBER` is optional. (Done later: set to "Real Estate App" and deployed to Vercel.)
