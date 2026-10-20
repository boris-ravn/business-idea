# Real Estate App: Business Plan & Validation Report

> All interviews were run in Spanish. The quotes in this document are translated into English.
>
> **Links:** [MVP source code](https://github.com/boris-ravn/MVP-Real-Estate-App) · [Live MVP](https://mvp-real-estate-app.vercel.app/) · [First demo video](https://drive.google.com/file/d/1LDSivIeA8RWx8hzJWXJuEHz3vo7leXAY/view?usp=sharing) · [Interviews](../01-discovery/interviews.md) · [PRD v1](../02-product/prd-v1-mvp.md) · [PRD v2](../02-product/prd-v2-feedback-iteration.md)

---

## Executive summary

Real estate agents in Bolivia constantly work together: when a client asks for something the agent doesn't have, the agent looks for it among other agents' listings and they split the commission. Outside franchises like RE/MAX or Century 21, this collaboration happens in **WhatsApp and Facebook groups**, which are free and popular but chaotic.

I started with the idea of an open "MLS" (Multiple Listing Service) for independent agents. Interviews changed my understanding of the problem: **agents can usually *find* listings, but they waste hours or days *verifying* them.** They have to check whether a property is still available, whether the price is current and what the conditions are. One agent took **two days** to put together confirmed options for a single client. Interviews also showed that agents with a large network barely feel this pain, which narrowed my customer segment.

I built a working mobile prototype (Real Estate App) around three ideas: **visible freshness** ("confirmed 2 days ago"), **structured filters** that match what clients actually ask for, and **zero change of habits**. Agents publish by pasting the WhatsApp message they already write and share back to WhatsApp. A first walkthrough of the prototype suggested that paste-to-publish works and surfaced a new insight: agents share with **two audiences**, other agents and their own clients, and they need different information for each. A second iteration of the prototype (PRD v2) already addresses that feedback; a moderated test with real agents is the next step.

**Value proposition:** *Other agents' listings, with confirmed availability, without leaving WhatsApp.*

---

## 1. How this work was done

| Step | What I did | Output |
|---|---|---|
| 1. Initial idea | Defined a first concept from domain knowledge (someone close to me is a real estate agent) | An "open MLS" for independent agents |
| 2. Assumptions | Listed and ranked the riskiest assumptions; rejected "trust" as the main risk using domain evidence | Riskiest assumption: **pain** |
| 3. Customer interviews | Wrote a *Mom Test*-style script (past behavior, not opinions) and interviewed 3 real agents in Cochabamba by call and voice notes | 3 transcribed interviews (transcribed locally with Whisper) |
| 4. Product definition | Turned the learnings into a PRD with scope, acceptance criteria and a user-test plan with success thresholds defined in advance | PRD + low-fidelity wireframes |
| 5. Prototype | Implemented a working, mobile-first web prototype with an AI coding agent working from the PRD | Real Estate App prototype |
| 6. Prototype walkthrough | Ran the 4 tasks of the test plan on a mobile screen, with an AI agent playing the role of an end user (a heuristic review, **not** a test with real agents), and consolidated the findings | Prioritized feedback, phrased in end-user terms |
| 7. Reflection | Reviewed what changed in the idea and what is still unproven; recorded the first demo | This document + [demo video](https://drive.google.com/file/d/1LDSivIeA8RWx8hzJWXJuEHz3vo7leXAY/view?usp=sharing) |
| 8. Iteration 2 | Prioritized the feedback (P0/P1), wrote PRD v2, approved wireframes and implemented 7 changes in the prototype | [PRD v2](../02-product/prd-v2-feedback-iteration.md) + MVP v2 |
| 9. Real-user test | Moderated sessions with 3 to 5 real agents (in progress) | Next |

---

## 2. Who it's for, the problem and how they solve it today

### Customer
**Real estate agents in Cochabamba, Bolivia, who work independently or without a strong network**, who:
- Handle rentals, sales and *anticrético* (a Bolivian lease-for-deposit arrangement)
- Work mostly from their **phone**, with **WhatsApp** as their main tool
- Serve around 5 to 10 clients per month (Valeria)
- Depend on other agents' listings when a client asks for something outside their own portfolio

Expansion to Santa Cruz and La Paz is planned only after proving the model in one city.

### Problem
When a client asks for something specific (for example, *"2 bedrooms, garage, pets allowed, within budget"*), the agent has to search other agents' listings. The hard part is not finding candidates; it is **confirming** them:

- **Availability:** *"The most frustrating part is when I have a client who's really interested in visiting a property and, when I ask, it turns out it was already rented or sold."* (Valeria)
- **Time:** finding confirmed options took Valeria **about two days**, *"because I had to confirm prices, availability and some rental conditions."*
- **Digging through chat history:** *"Searching through the WhatsApp groups, going through everything back in time, takes you time… and other agents don't reply when you ask them something."* (María)
- **Missing data:** the details clients filter by (garage, pets, storage unit, HOA fees, size, paperwork in order, bank financing accepted) are often missing from posts.

What agents want, in their own words: *"The most useful thing would be for listings to be up to date, so we can quickly verify availability without having to ask several agents."* (Valeria)

### How they solve it today
- **WhatsApp groups** of agents (some with hundreds of members) to post and search listings
- **Facebook Marketplace** and Facebook groups
- **Direct calls and messages** to trusted colleagues
- **Personal workarounds:** phone notes, screenshots, Excel sheets, even a private WhatsApp chat used as a listing archive (María)

These workarounds are a strong signal: agents are already investing effort to organize information that their tools don't organize for them.

---

## 3. Competitors and why someone would choose Real Estate App

| Alternative | Strength | Weakness for this use case |
|---|---|---|
| **WhatsApp / Facebook groups** | Free, everyone is already there, fast | No search or filters, no status (available, sold), posts get buried, inconsistent formats |
| **Facebook Marketplace & listing portals** (UltraCasas, InfoCasas) | Reach end buyers and renters | Built for end buyers, not for collaboration between agents; same freshness problem |
| **Franchise / office systems** (RE/MAX, Century 21, local offices) | Structured, shared inventory | Require joining an office, exclusivity contracts and giving up part of the commission |

**Our real competitor is WhatsApp, and we don't try to replace it.** That is why someone would choose Real Estate App:

1. **Freshness you can see:** every listing shows when its availability was last confirmed.
2. **Filters that match real requests:** budget, zone, bedrooms, garage, pets, storage, bank financing, paperwork in order.
3. **Explicit collaboration terms:** commission split and "authorized by owner" are visible upfront.
4. **No new habits:** paste your existing WhatsApp post to publish, share back to WhatsApp in one tap and contact the listing agent through WhatsApp.
5. **No affiliation:** the benefits of a franchise's shared inventory without joining a franchise.

---

## 4. The experiment

### 4.1 Riskiest assumption: how it changed
- **First candidate, trust:** *"Agents won't expose their clients' properties to other agents for fear of being bypassed."* **Rejected** before testing: agents already post their clients' properties in open WhatsApp and Facebook groups, often without exclusivity contracts. The risk exists, but it doesn't stop them.
- **Chosen assumption, pain:** *"Informal channels hurt enough (in time, lost deals and reputation) that agents will adopt a dedicated tool."*

### 4.2 Method
1. **Customer interviews (3 real agents in Cochabamba).** Questions about the last real situation, not opinions about the idea. Topics: last search for a client, sources used, time spent, information they had to confirm, how they share their own listings, how they organize themselves, what they pay for today and their biggest frustration.
2. **Working prototype + task-based walkthrough** of the 4 core tasks: search with filters and contact an agent, publish by pasting a WhatsApp message, update a listing that was rented, and share a listing.

### 4.3 Interview participants

| Agent | Profile | Format |
|---|---|---|
| **María** | Mostly rentals, some sales; posts in an office system (when she has exclusivity) and in external agent groups | Live call |
| **Nicolás** | Sales; works with his family's real estate business and sells independently through TikTok; co-admins a large WhatsApp group of agencies | Voice-note answers |
| **Valeria** | About 1 year in the business, previously independent; rentals and sales; 5 to 10 clients per month | 7 voice notes, one per question |

### 4.4 What I learned

| # | Learning | Evidence |
|---|---|---|
| 1 | **The pain is real, but it is about *verifying*, not *finding*.** | Valeria: 2 days to confirm options, properties *"already rented or sold"*. María: digging back through group history; agents who don't reply |
| 2 | **Pain depends on network strength.** Agents with a large, well-managed network barely feel it. | Nicolás: *"Nothing really frustrates us… we send requirements and find things easily."* → target segment: **agents without a strong network** |
| 3 | **Collaboration between agents is the norm and the most effective channel.** | María: *"The most efficient way to work is posting among agents so that, together, a sale or rental happens much faster."* |
| 4 | **Nobody pays for tools today; they pay for ads occasionally.** | Valeria: 50 to 100 Bs per boosted post, no fixed platform. Nicolás: about 200 Bs/month in ads (family agency). María: TikTok and Meta ads |
| 5 | **The filters that matter are concrete and local.** | Garage/parking, pets, storage unit, HOA fees, size, budget, zone, bedrooms, utilities, paperwork in order, bank financing accepted |
| 6 | **Agents already maintain manual workarounds.** | Excel and a private WhatsApp archive (María), notes and screenshots (Valeria), a private group with spec sheets and commissions (Nicolás) |
| 7 | **"Requirements" are part of how they work.** | Nicolás: agents post what their client is *looking for* and others answer with matches |

### 4.5 What I'm still assuming
- **Agents will keep listings fresh** by tapping "still available" regularly. The whole value proposition depends on this.
- **Cold start:** enough agents will publish for searches to be useful.
- **Willingness to pay** for a tool, not just for ads.
- **Self-declared trust signals** (commission split, owner authorization) are enough without verification.
- **Segment size:** there are enough agents "without a strong network" in Cochabamba to sustain the product.
- **Sample size:** 3 interviews are enough to point the direction, not to prove it.

---

## 5. Prototype walkthrough and feedback

> **About this evidence:** this first round was a **heuristic walkthrough**, not a test with real agents. An AI agent played the role of an end user and ran the 4 tasks of the test plan on a mobile screen; I then consolidated the findings and phrased them as an end user would. Where a finding matches what the real interviewees said, that is noted. All of it is treated as a hypothesis until the moderated test with real agents (section 9).

The feedback, in end-user terms:

### What worked
- **"Filtering was fast and easy. I like seeing how many properties match before applying."** The walkthrough reached 3 relevant results without typing anything.
- **"Pasting my message and having the form fill itself was a surprise."** The paste-to-publish flow detected most fields from a messy, real-style WhatsApp post, including negations like "NO pets".
- **Freshness badges and the standard listing sheet** gave information that WhatsApp posts don't.
- **No account needed:** just a name and WhatsApp number.

### What needs to change
1. **"If I share a listing with my *client*, I don't want them to see the commission or that it belongs to another agent."** Agents share with **two audiences** that need different content. This is the most important finding of the walkthrough, and it matches what Valeria described: she posts for end clients on Marketplace and for agents in WhatsApp groups.
2. **"Reviewing the post after pasting is too long. Just show me what's missing."**
3. **"It didn't understand '4 mil Bs' (4 thousand) or a location like 'Av. Pando'. That's how we write here."** The parser must speak the local language: "mil", avenues and landmarks.
4. **"HOA fees (*expensas*) are missing, and they matter for rentals."** Valeria listed *expensas* among the essential filters.
5. **"If I search for 2 bedrooms, show me 3 too."**
6. **"Commission and owner authorization should be at the top of the listing, not at the bottom."**
7. **"It says 2 listings need confirmation, but which ones? Show them first or let me confirm all at once."**
8. **"I'd like to publish what my client is *looking for*, not only what I have."** This confirms learning #7 from the interviews.

---

## 6. How the idea changed

| Version | What I believed | What changed it |
|---|---|---|
| **v0: Initial idea** | An open MLS for independent agents. Competitors: RE/MAX and Century 21. Main risk: trust. | Domain knowledge: agents already share clients' properties in open groups |
| **v1: Before interviews** | The real competitor is WhatsApp and Facebook groups. Main risk: whether their chaos hurts enough. Pain = "finding takes too long." | 3 customer interviews |
| **v2: After interviews** | The pain is **verifying** (availability, price, conditions), with a cost in time and reputation. Target narrowed to **agents without a strong network**. Product built around **freshness** + **paste-to-publish** + **WhatsApp integration**. | Prototype walkthrough |
| **v3: After the prototype** | Paste-to-publish works, but the parser must understand local language. Sharing needs **two modes** (for agents / for my client). **Client requirements** ("Looking for…") should be a first-class feature. | Iteration 2 built most of this; next: real-agent test and live pilot (section 9) |

**What did *not* change:** the customer (agents in Cochabamba), the core mechanism (a shared listing network) and the decision to work *with* WhatsApp instead of against it.

---

## 7. Smallest useful version, mockup and how it's built and tested

### 7.1 Smallest useful version (what the prototype includes)
1. **Search with filters:** operation (sale, rent, anticrético), type, zone, price, bedrooms, plus garage, pets, storage, bank financing, paperwork and utilities. Live result count and smart empty state ("Try removing *Storage*").
2. **Standard listing sheet:** price → **availability freshness** → features → conditions → collaboration (commission split, owner authorization) → agent.
3. **Contact via WhatsApp:** opens WhatsApp with a pre-filled message: *"Hi Carla, I saw your apartment in Cala Cala (Bs 3,800/month)… is it still available? I have an interested client."*
4. **Publish by pasting:** paste the WhatsApp post → fields are extracted automatically → review what's missing → publish. No account needed.
5. **My listings:** one-tap "Still available", "Change price" and "Mark as rented/sold" (with undo). Reminder banner for listings unconfirmed for more than 7 days.
6. **Share to WhatsApp:** generates a clean, standard post with a link to the listing.

**Freshness rule:** confirmed ≤ 3 days ago is green; 4 to 14 days, amber; more than 14 days, grey.

### 7.2 Mockup
- Low-fidelity wireframes of the 6 core screens: [wireframes v1](../02-product/wireframes/v1.html); screens changed in iteration 2: [wireframes v2](../02-product/wireframes/v2.html)
- Working clickable prototype: [live MVP](https://mvp-real-estate-app.vercel.app/) (best viewed on a phone or a 360×800 viewport) · [source code](https://github.com/boris-ravn/MVP-Real-Estate-App)

### 7.3 How it was built
- **Process:** interview insights → PRD with acceptance criteria → low-fi wireframes (approved before coding) → implementation by an AI coding agent working from the PRD.
- **Stack:** Vite, React, TypeScript, Tailwind CSS. No backend: data lives in the browser (localStorage), preloaded with 25 to 30 realistic fictional listings from Cochabamba zones.
- **Parser:** rule-based extraction (prices in $us and Bs, bedrooms, m², zones, garage, pets, commission…) behind a single interface, so it can be swapped for an LLM later without changing the UI. Covered by unit tests.
- **Built-in measurement:** local event log (filters used, contacts, publishes, fields detected, shares) and an in-app feedback button.

### 7.4 How it's tested
- **Moderated sessions of about 15 minutes** with 3 to 5 agents, on their own phones, thinking aloud.
- **4 tasks:** (1) find a 2-bedroom rental with garage and pets under 4,000 Bs and contact the agent; (2) publish a listing by pasting your last group post; (3) update a listing that was just rented; (4) share a listing to a group.
- **Success thresholds defined before testing** (see section 9).

### 7.5 Iteration 2: what the second build added
The feedback from section 5 was prioritized and specified in [PRD v2](../02-product/prd-v2-feedback-iteration.md), then implemented (details and traceability in [PRD evolution](../02-product/prd-evolution.md)):

- **P0:** two-mode sharing (*for agents* / *for my client*, without commission or the listing agent's details); a compact publish review that shows only what's missing; local-language parsing ("4 mil", zones written with hyphens, zone suggested from an avenue); and "Confirm all" for listings that need confirmation, with undo.
- **P1:** HOA fees (*expensas*) captured at publish time and shown on cards; a "2+ bedrooms" (minimum) filter; collaboration terms moved to the top of the listing sheet.
- **Deferred:** the **"Looking for…" requirements board** and more compact cards. An LLM-based parser stays an option if the real-agent test shows the rule-based parser is not enough.

---

## 8. Pricing, marketing and first paying customers

### 8.1 Pricing
- **Anchor from interviews:** agents don't pay for tools, but they **do pay for visibility**: 50 to 100 Bs per boosted post (Valeria); about 200 Bs/month in ads (Nicolás's family agency).
- **Model: freemium.**
  - **Free forever:** search, contact and publish. Supply is the product's most valuable asset, so publishing must never have a cost barrier.
  - **Pro plan (hypothesis: about 70 Bs/month, below the cost of a single Meta ad campaign):** branded listing pages to send to clients (an upgrade of the free, text-only "for my client" sharing mode added in iteration 2), featured listings, instant alerts when a matching listing or requirement is posted, and unlimited active listings.
- **Why not a commission per deal:** deals close offline and over weeks, so it would be impossible to track and would feel like the franchise model agents are avoiding.

### 8.2 Marketing
- **Message:** *"Stop asking 'is it still available?'. See confirmed listings from other agents, without leaving WhatsApp."*
- **Channels:**
  - **WhatsApp group admins as partners.** Every shared listing carries a link back to the app, so the groups become a distribution channel instead of a competitor. Nicolás co-admins a large agency group: a natural first partner.
  - **TikTok:** 2 of the 3 agents interviewed already use it to sell; short videos showing the "paste your post → structured listing" moment.
  - **Word of mouth:** each shared listing is an ad for the tool among agents.

### 8.3 First paying customers
1. **Founding members:** recruit 20 to 30 agents from 1 or 2 WhatsApp groups in Cochabamba through my close contact and group admins.
2. **Concierge onboarding:** I load their current listings myself by pasting their posts, removing the cold-start effort.
3. **3 months free**, then a lifetime founder price for Pro.
4. **First conversion target:** the most active agents, those who share with clients most often and benefit most from branded client pages.

---

## 9. What I'd test next and what would make me change direction

### Test 1: moderated prototype test with real agents (in progress)

Iteration 2 adds signals for the new features (choosing "for my client" without help, finishing the review without expanding it, using "Confirm all"); see [PRD v2 §7](../02-product/prd-v2-feedback-iteration.md).

| Signal | Keep going | Change direction |
|---|---|---|
| Agents complete "publish by pasting" | ≥ 60% | < 40%, or they say they wouldn't use it |
| Parser detects ≥ 5 fields from their real post | ≥ 60% of posts | < 30% → switch to an LLM parser |
| "Better than how I do it today" | ≥ 3 of 5 | ≤ 1 of 5 |
| "I'd use it for my next client" (yes/maybe) | ≥ 3 of 5 | ≤ 1 of 5 |

### Test 2: 2-week live pilot in one real WhatsApp group (with a shared backend)
The main open risk is **freshness upkeep**.

| Signal | Keep going | Change direction |
|---|---|---|
| Active listings re-confirmed at least once per week | ≥ 50% | < 50% → add automatic WhatsApp reminders; if that fails, pivot (see below) |
| Agents who publish ≥ 1 listing | ≥ 40% | < 20% |
| Agents who contact another agent through the app | ≥ 25% | Almost none |

### Pivot options already identified
- **Agents search but don't publish** → pivot to an **aggregator** that reads listings from existing WhatsApp groups and structures them with AI, so no one has to publish anything new.
- **Agents mostly use "Looking for…"** → pivot to a **requirements-matching** product: post what your client needs, get notified when a match appears.
- **No pain among agents with networks, and too few without one** → reconsider the segment (for example, new agents entering the market) or the city.

### What would make me stop
If agents in the pilot keep verifying every listing through WhatsApp anyway (meaning the freshness signal doesn't change their behavior), the core value proposition fails. I would stop building the network and go back to the interviews to find a different problem.
