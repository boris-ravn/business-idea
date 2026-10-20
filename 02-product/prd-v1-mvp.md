# PRD: MVP Prototype "Shared Property Network" (Cochabamba)

> **For the implementing agent:** this document is all the context you need. It is a **prototype for user testing**, not a production product. Prioritize being quick to build, working well on a phone, and making the flows feel real. Do not add a backend, authentication, or features outside the scope.

> **Update:** the changes made after the feedback from the first test are in [`prd-v2-feedback-iteration.md`](prd-v2-feedback-iteration.md).

- **Author:** Boris Martínez
- **Status:** Draft v1 · Superseded in part by PRD v2 (Oct 20, 2026)
- **Product name:** Real Estate App (working name; a single constant so it can be changed)
- **UI language:** Spanish (Bolivia). Informal "tú" form of address, a warm and professional tone.

---

## 1. Context and problem

Real estate agents in Cochabamba collaborate with each other to close deals: when a client is looking for something they do not have in their own portfolio, they look for it among other agents' properties and share the commission. Today that collaboration happens in **WhatsApp and Facebook groups**, which are informal and disorganized.

**Evidence from the real interviews (2):**
- *"Having to search the WhatsApp groups [going through] everything further back takes you time… And also other agents not answering when you ask them something."* (María)
- Filters mentioned as essential: **budget, zone, bedrooms, garage/parking, pets, *baulera* (storage unit), basic utilities, up-to-date paperwork, and whether bank financing is accepted**.
- Counter-evidence: an agent with a large, well-managed network says *"we send out requirements and find things easily"* (Nicolás). Hypothesis: the pain is greater for the agent **without** a strong network.

**Complementary hypotheses (from preparatory interviews, not validated):** the main cost is **verifying** (availability, updated price, missing data, and commission terms), and agents **do not want to enter properties by hand or leave WhatsApp**.

## 2. Prototype goal

Put the product in the hands of 3 to 5 real agents to observe them and collect feedback on three questions:

1. **Search:** is finding a property with filters clearly better than going through WhatsApp groups?
2. **Publish:** is publishing by pasting the message they already use on WhatsApp easy enough that they will actually do it?
3. **Trust:** do the "confirmed X days ago" indicators, the explicit shared commission, and the owner's authorization reduce the need to verify via chat?

It is **not** meant to validate willingness to pay, scale, or retention.

## 3. Target user

**Real estate agent in Cochabamba**, independent or with little office structure. Works from their **phone** with WhatsApp as their main tool. Operates in sales, rentals, and *anticrético* (a Bolivian lease where the tenant lends the owner a lump sum instead of paying rent). Has little tolerance for long forms.

## 4. Design principles

1. **Mobile-first.** It will be tested on mid-range Android phones. Desktop is secondary.
2. **WhatsApp is the ally, not the competitor.** Contacting opens WhatsApp, sharing generates text for WhatsApp, and publishing starts from a WhatsApp message.
3. **Less typing.** Paste instead of filling in; chips and toggles instead of text fields.
4. **Information freshness is visible.** Each property shows when it was last confirmed.
5. **Standard listing sheet.** All properties show the same key fields in the same order.

## 5. Functional scope

### Must have

#### F1. Browse and search with filters (home screen)
- List of property cards, sorted by **most recent confirmation**.
- Fixed filter bar with chips: **Operation** (Sale / Rent / *Anticrético*), **Type** (House / Apartment / Land / Commercial space or office), **Zone** (multi-select), **Price** (min/max), **Bedrooms** (1, 2, 3, 4+).
- "More filters" panel: Garage, Pets allowed, *Baulera*, Accepts bank financing, Paperwork up to date, Full basic utilities.
- Results counter ("12 propiedades" — "12 properties") and a "Limpiar filtros" ("Clear filters") button.
- Helpful empty state: "No hay resultados. Prueba quitando *Baulera*" ("No results. Try removing *Baulera*"), suggesting the most restrictive filter.
- **Card:** photo, price (USD for sale and *anticrético*; Bs for rent), type and zone, bedrooms, bathrooms, m², garage and pet icons, **freshness badge**, and shared commission ("Comparte 50%" — "Shares 50%").

#### F2. Property detail (standard listing sheet)
- Photo gallery (1 to 5 placeholder photos).
- Blocks in this order: **Price and operation** → **Availability** (freshness badge with date) → **Features** (bedrooms, bathrooms, built and land m², garage, pets, *baulera*, utilities) → **Conditions** (bank financing, paperwork up to date; for rentals: deposit and *expensas* (monthly building/HOA fees)) → **Collaboration** (shared commission %, "Autorizado por el propietario" ("Authorized by the owner") badge if applicable) → **Agent** (name and time in the network) → Free-text description.
- Fixed buttons at the bottom: **"Consultar por WhatsApp"** ("Ask via WhatsApp") (F3) and **"Compartir"** ("Share") (F6).

#### F3. Contact the agent via WhatsApp
- Opens `https://wa.me/<numero>?text=<mensaje>` with a prefilled message:
  > "Hola [nombre], vi tu [tipo] en [zona] ([precio]) en Real Estate App. ¿Sigue disponible? Tengo un cliente interesado."
  >
  > ("Hi [name], I saw your [type] in [zone] ([price]) on Real Estate App. Is it still available? I have an interested client.")
- Logs the `contact_click` event (see §7).
- Since the test numbers are fake, first show a notice: "Prototipo: esto abriría WhatsApp con este mensaje" ("Prototype: this would open WhatsApp with this message"), with the option to continue or cancel. Controlled by the `DEMO_MODE` flag.

#### F4. Publish by pasting a message (key flow)
1. "+ Publicar" ("+ Publish") button always visible.
2. **Step 1, Paste:** text area for the message exactly as they would post it on WhatsApp, with a placeholder example. Secondary link "Prefiero llenarlo manualmente" ("I'd rather fill it in manually").
3. **Step 2, Review:** the parser (see §6) prefills a listing sheet. Each detected field is marked as "detectado" ("detected"); missing ones are highlighted as "Falta: agrégalo para que te consulten menos" ("Missing: add it so you get fewer questions"). Everything is edited with chips and toggles.
4. Collaboration fields: **% of shared commission** (chips 30/40/50/other) and the checkbox **"Tengo autorización del propietario"** ("I have the owner's authorization").
5. Photos: in the prototype it is enough to choose from 6 placeholders (no real file upload required).
6. **Step 3, Publish:** the property appears in the listing with the status "Confirmado hoy" ("Confirmed today"). Success screen with the CTA "Compartir en tus grupos" ("Share in your groups") (F6).
- The first time someone publishes, they are asked for their **name and WhatsApp number** (stored locally). No account or password.
- Events: `publish_started`, `publish_parsed` (with the number of detected fields), `publish_completed`.

#### F5. My properties: keeping information up to date
- List of the user's own properties, each with its status and freshness.
- One-tap actions: **"Sigue disponible"** ("Still available") (resets freshness to "today"), **"Cambiar precio"** ("Change price"), **"Marcar vendida/alquilada"** ("Mark as sold/rented") (removed from the public listing and kept under "Cerradas" ("Closed")).
- Banner when a property has gone **more than 7 days without confirmation**: "3 propiedades necesitan confirmación. ¿Siguen disponibles?" ("3 properties need confirmation. Are they still available?") This simulates the reminder that in production would arrive via WhatsApp.

#### F6. Share to WhatsApp
- Generates text in a standard format to paste into groups (moderate emojis, key fields, and a link to the listing sheet), for example:
  ```
  🏠 Departamento en venta – Queru Queru
  💵 $us 95.000 · 3 dorm · 2 baños · 110 m²
  🚗 Garaje · 🐾 Acepta mascotas
  🤝 Comparte comisión 50%
  ✅ Confirmado hoy
  Ver ficha: <link>
  ```
  English translation:
  ```
  🏠 Apartment for sale – Queru Queru
  💵 $us 95.000 · 3 bed · 2 baths · 110 m²
  🚗 Garage · 🐾 Pets allowed
  🤝 Shares commission 50%
  ✅ Confirmed today
  View listing: <link>
  ```
- "Copiar texto" ("Copy text") and "Abrir WhatsApp" ("Open WhatsApp") buttons (`wa.me/?text=`).

#### F7. Feedback button (for the test)
- Floating "💬 Opinar" ("💬 Give feedback") button, always visible.
- Mini form: (a) "¿Qué tan útil te parece?" ("How useful do you find it?") from 1 to 5; (b) "¿Qué le falta o qué cambiarías?" ("What is missing or what would you change?") as free text; (c) "¿Lo usarías para tu próximo cliente?" ("Would you use it for your next client?") (Sí / Tal vez / No — Yes / Maybe / No).
- On submit, opens WhatsApp with the answers and the **session event summary** addressed to the researcher's number (constant `FEEDBACK_WHATSAPP_NUMBER`, empty by default, configured by Boris). If the number is empty, the button shows the text so it can be copied.

### Should have (only if time allows)
- **Favorites:** save properties for a client.
- **Requirements:** post what a client is looking for ("Busco depto 2 dorm, Cala Cala, hasta 600 $us…" — "Looking for a 2-bed apartment, Cala Cala, up to 600 $us…") and see matches. This is the practice Nicolás described.

### Out of scope
Backend, shared database, login and identity verification, real image upload, maps, payments, real push or WhatsApp notifications, calls to a real LLM, admin panel, multiple cities, and internationalization.

## 6. "Paste message → listing sheet" parser

- **Prototype implementation:** deterministic client-side parser (regex and dictionaries). It is designed behind an interface `parseListing(text): Partial<Listing> & { detected: string[] }` so it can later be swapped for an LLM without touching the UI.
- **It must detect at a minimum:**
  - Operation: "venta", "vendo", "alquiler", "alquilo", "anticrético" (sale, selling, rent, renting, *anticrético*).
  - Type: casa, depto/departamento, terreno, lote, local, oficina (house, apartment, land, lot, commercial space, office).
  - Price: `$us`, `USD`, `$`, `dólares`, `Bs`, `bolivianos`, with `.` or `,` separators and "k" (for example `95k`).
  - Bedrooms and bathrooms: "3 dorm", "3 dormitorios", "3 habitaciones", "2 baños" (3 bed, 3 bedrooms, 3 rooms, 2 bathrooms).
  - Area: `m2`, `m²`, `mts`.
  - Booleans: garaje/parqueo/cochera (garage/parking/carport), mascotas (pets), baulera (*baulera*), crédito bancario (bank financing), papeles/documentación al día (paperwork/documentation up to date).
  - Zone: match against the zone list in §8 (case- and accent-insensitive).
  - Commission: "comparto 50%", "comisión compartida 50/50" ("I share 50%", "shared commission 50/50").
- Include **unit tests** for the parser with at least the 8 sample messages in `seed/messages.ts`.

## 7. Data and measurement

- **Persistence:** `localStorage`. Seed data is loaded on first use. Add a hidden "Reiniciar demo" ("Reset demo") button (in Settings or via a long press on the logo) to reset between participants.
- **`Listing` model:** `id, operation, type, zone, price, currency, bedrooms, bathrooms, builtM2, landM2, garage, pets, storage, bankCredit, docsOk, utilities, rentGuarantee?, rentExpenses?, commissionShare, ownerAuthorized, photos[], description, agent {name, whatsapp, memberSince}, lastConfirmedAt, status (active|closed), createdAt, isMine`.
- **Freshness badge:** ≤3 days, green "Confirmado hace X días" ("Confirmed X days ago"); 4 to 14 days, amber; more than 14 days, gray "Sin confirmar hace X días" ("Not confirmed for X days").
- **Events (local log with timestamp):** `session_start`, `filter_applied` (filters), `listing_viewed`, `contact_click`, `share_click`, `publish_started`, `publish_parsed`, `publish_completed`, `confirm_available`, `mark_closed`, `feedback_sent`. The summary is attached to the feedback (F7) and can be viewed at a hidden `/debug` route.

## 8. Seed data

- **25 to 30 fictitious properties** in Cochabamba with a realistic mix:
  - Operation: ~50% sale, ~35% rent, ~15% *anticrético*.
  - Type: apartments, houses, land, and a few commercial spaces.
  - **Zones:** Queru Queru, Cala Cala, Sarco, Zona Norte, Centro, Recoleta, Mayorazgo, Tupuraya, Zona Sur, Tiquipaya, Sacaba, Colcapirhua, Quillacollo, Temporal, Muyurina.
  - Plausible prices: apartments for sale from 55k to 180k $us; houses from 90k to 350k $us; apartment rent from 2.000 to 6.000 Bs; *anticrético* from 20k to 60k $us.
  - Varied freshness: about 40% green, 35% amber, and 25% gray.
  - Some with incomplete data, to show the value of the standard listing sheet.
  - 8 to 10 fictitious agents with common Bolivian names and clearly fake numbers (`+591 70000001`…).
- **8 WhatsApp-style sample messages** in `seed/messages.ts` to test the parser, with realistic formatting variations (capitalization, emojis, abbreviations such as "dpto", "dorm", "$us", "c/garaje").
- Photos: neutral placeholders (gradient with a house or apartment icon). No third-party images.

## 9. Stack and delivery

- **Vite + React + TypeScript + Tailwind CSS.** No backend. Routing with React Router (or a simple hash router).
- A single `config.ts` with `PRODUCT_NAME`, `DEMO_MODE`, `FEEDBACK_WHATSAPP_NUMBER`.
- Static deploy (Vercel or Netlify) to share a link via WhatsApp. It must load quickly on 4G.
- Test on a 360×800 viewport. Touch targets of at least 44 px. AA contrast.
- README covering: how to run it, how to reset the demo, how to configure the feedback number, and how to deploy.

## 10. Suggested process for the agent

1. **Low-fi wireframes first** (*wireframer* skill) of 6 screens: Home with filters, More filters, Detail, Publish step 1 (paste), Publish step 2 (review), and My properties. **Stop and ask for approval** before implementing.
2. Implement in this order: seed data and model → F1 → F2 → F3 → F4 (parser with tests) → F5 → F6 → F7 → `/debug`.
3. Verify each flow on a mobile viewport before moving on to the next.

## 11. Acceptance criteria

- [ ] From the home screen, applying "Departamento + Alquiler + Cala Cala + Garaje + Mascotas" ("Apartment + Rent + Cala Cala + Garage + Pets") shows the correct results and their counter in fewer than 3 taps per filter.
- [ ] Every card and every listing sheet shows the freshness badge with the correct color based on `lastConfirmedAt`.
- [ ] "Consultar por WhatsApp" ("Ask via WhatsApp") generates the correct `wa.me` URL with the prefilled message (verifiable in `DEMO_MODE`).
- [ ] When pasting any of the 8 seed messages, at least operation, type, price, bedrooms, and zone are detected. The parser tests pass.
- [ ] A published property appears first in the listing as "Confirmado hoy" ("Confirmed today") and in "Mis propiedades" ("My properties").
- [ ] "Sigue disponible" ("Still available") updates the freshness; "Marcar vendida" ("Mark as sold") removes it from the public listing.
- [ ] "Compartir" ("Share") produces the text in the F6 format and copies it to the clipboard.
- [ ] The feedback opens WhatsApp (or shows the text to copy) with the answers and the event summary.
- [ ] "Reiniciar demo" ("Reset demo") restores the seed data and clears the events.
- [ ] Everything works without a connection to a backend and without console errors.

## 12. User test plan (run by Boris; context for the agent)

- **Participants:** 3 to 5 agents in Cochabamba, ideally with different network sizes.
- **Format:** moderated session of about 15 minutes (in person or video call), on the participant's phone. Think aloud.
- **Tasks:**
  1. "Tu cliente busca un departamento en alquiler, 2 dormitorios, con garaje y que acepte mascotas, hasta 4.000 Bs. Encuentra opciones y consulta al asesor." ("Your client is looking for an apartment to rent, 2 bedrooms, with a garage and pets allowed, up to 4.000 Bs. Find options and contact the agent.")
  2. "Publica una propiedad tuya: copia el último mensaje que mandaste a un grupo y pégalo aquí." ("Publish one of your properties: copy the last message you sent to a group and paste it here.")
  3. "Una de tus propiedades ya se alquiló. Actualízala." ("One of your properties has been rented. Update it.")
  4. "Comparte una propiedad en tu grupo de WhatsApp." ("Share a property in your WhatsApp group.")
- **What is observed:** whether they complete each task, how long it takes, where they hesitate, and how many fields the parser detected with their real message.
- **Closing questions:** "Comparado con cómo lo haces hoy, ¿esto es mejor, igual o peor? ¿Por qué?" ("Compared to how you do it today, is this better, the same, or worse? Why?") · "¿Qué tendría que pasar para que lo uses con tu próximo cliente?" ("What would have to happen for you to use it with your next client?") · "Si costara algo, ¿cuánto tendría sentido?" ("If it cost something, how much would make sense?")
- **Criteria defined in advance:**

  | Signal | Continue | Change course |
  |---|---|---|
  | Task 2 completed (publish by pasting) | ≥ 60% of participants | < 40%, or they say they would not use it |
  | The parser detects ≥ 5 fields with their real message | ≥ 60% of messages | < 30% (in that case a real LLM is needed or the approach does not work) |
  | "Better than how I do it today" | ≥ 3 of 5 | ≤ 1 of 5 |
  | "I would use it for my next client" (Yes/Maybe) | ≥ 3 of 5 | ≤ 1 of 5 |

## 13. Risks and open assumptions

- **Cold start:** in the test it is mitigated with the seed data; in reality it remains the main risk.
- **Self-declared freshness:** "confirmed" depends on the agent pressing the button. We do not know whether they will do it without a real reminder.
- **Self-declared authorization and commission:** nothing is verified. It is a trust risk for a later phase.
- **Segment:** the two real interviewees work with an office or a family-run real estate agency. Validation with purely independent agents is still missing.
- **Willingness to pay:** not validated in this prototype.
