# Session guide: moderated test with real agents

Use this guide in every session so the results can be compared. The test plan comes from [PRD v1 §12](../02-product/prd-v1-mvp.md) and [PRD v2 §7](../02-product/prd-v2-feedback-iteration.md). The lines in *italics* are the Spanish script to read aloud; the English translation follows each one.

- **Participants:** 3 to 5 agents in Cochabamba, ideally with different network sizes (with and without a strong network).
- **Format:** moderated session of about 15 to 20 minutes, in person or by video call, on the **participant's own phone**. They think aloud.
- **Moderator's rule:** don't help or explain the interface. If they ask, answer *"¿Qué harías tú?"* ("What would you do?"). Step in only if they are stuck for more than 1 minute, and record that the task was completed **with help**.

---

## 1. Before the session

- [ ] The [live MVP](https://mvp-real-estate-app.vercel.app/) has the iteration 2 changes deployed (two-mode sharing, "Confirmar todas", the "2+" filter).
- [ ] Reset the demo on the participant's phone: **long-press the logo** → "Reiniciar demo" (reset demo). This restores the sample listings, clears the event log and **removes the profile**, so task 1 also tests the "no profile yet" flow.
- [ ] Ask the participant to have at hand **the last WhatsApp message** they posted to an agents group (for task 2).
- [ ] Copy [participant-template.md](participant-template.md) as `participant-N.md` and fill in the profile section.
- [ ] Have a timer ready (the phone's stopwatch is enough).

## 2. Introduction (1 to 2 minutes)

> *"Gracias por tu tiempo. Estoy probando un prototipo para asesores inmobiliarios. No te estoy evaluando a ti: estoy evaluando la app. Si algo no se entiende, es culpa de la app, no tuya. Te voy a pedir que pienses en voz alta mientras lo usas: qué buscas, qué esperas que pase y qué te confunde. ¿Te parece bien si tomo notas? No voy a publicar tu nombre completo."*
>
> "Thanks for your time. I'm testing a prototype for real estate agents. I'm not evaluating you: I'm evaluating the app. If something isn't clear, that's the app's fault, not yours. Please think aloud while you use it: what you're looking for, what you expect to happen and what confuses you. Is it OK if I take notes? I won't publish your full name."

Open the link on their phone and wait a few seconds for them to look around before starting task 1.

## 3. Tasks

Read each task aloud and start the timer. Stop it when they reach the end state or give up.

### Task 1: find and send to a client · validates C1, C6

> *"Tu cliente busca un departamento en alquiler, de 2 dormitorios o más, con garaje y que acepte mascotas, hasta 4.000 Bs. Encuentra opciones y envíale una a tu cliente."*
>
> "Your client is looking for an apartment to rent, with 2 or more bedrooms, with a garage and pets allowed, up to 4,000 Bs. Find options and send one to your client."

- **Done when:** they reach the share text in "Para mi cliente" (for my client) mode and tap "Copiar texto" (copy) or "Abrir WhatsApp".
- **Observe:**
  - Which filters they use, and in what order.
  - Whether they notice that "2+" includes 3 bedrooms.
  - Whether they **choose or accept "Para mi cliente" without help**.
  - How they react when asked for their name and WhatsApp.
  - Whether they read the freshness badge before choosing.
- **Ask afterwards:** *"¿Le mandarías ese texto tal cual a tu cliente? ¿Qué le cambiarías?"* ("Would you send that text to your client as is? What would you change?")

### Task 2: publish by pasting · validates C2, C3, C5

> *"Publica una propiedad tuya: copia el último mensaje que mandaste a un grupo y pégalo aquí."*
>
> "Publish one of your properties: copy the last message you sent to a group and paste it here."

- **Done when:** the success screen ("¡Publicada!", published) appears.
- **Observe:**
  - The "Detectamos X de Y" (we detected X of Y) count on the review screen.
  - Whether they open "Editar" (edit) to correct detected data.
  - Which fields they fill in under "Te falta" (missing).
  - Whether the zone was detected, suggested or missing.
  - Whether they understand the "Expensas" (HOA fees) field.
- **Record:** **with their permission**, copy their original message into the notes. The parser's accuracy is measured against real messages, so this is key evidence.

### Task 3: bring listings up to date · validates C4

> *"Tienes propiedades que hace días no confirmas. Ponlas al día."*
>
> "You have properties you haven't confirmed in days. Bring them up to date."

- **Done when:** no listing in "Mis propiedades" (my listings) needs confirmation.
- **Observe:**
  - Whether they find "Mis propiedades" by themselves.
  - Whether they read the banner.
  - Whether they use **"Confirmar todas"** (confirm all) or confirm one by one.

### Task 4: update a rented listing

> *"Una de tus propiedades ya se alquiló. Actualízala."*
>
> "One of your properties has already been rented. Update it."

- **Done when:** the listing moves to "Cerradas" (closed).
- **Observe:** whether they find "Marcar alquilada" (mark as rented) and whether they notice "Deshacer" (undo).

### Task 5: share with agents · validates C1

> *"Comparte una de tus propiedades en tu grupo de asesores."*
>
> "Share one of your properties in your agents group."

- **Done when:** they copy the text in "Para asesores" (for agents) mode or open WhatsApp.
- **Observe:** whether they keep the default "Para asesores" mode and whether they understand the difference from task 1.

## 4. Closing questions (3 to 5 minutes)

Ask them in this order and write down the **exact words** when possible.

1. *"Comparado con cómo lo haces hoy, ¿esto es mejor, igual o peor? ¿Por qué?"* ("Compared to how you do it today, is this better, the same or worse? Why?")
2. *"¿Lo usarías para tu próximo cliente? ¿Sí, tal vez o no?"* ("Would you use it for your next client? Yes, maybe or no?")
3. *"¿Qué tendría que pasar para que lo uses con tu próximo cliente?"* ("What would have to happen for you to use it with your next client?")
4. *"¿Confirmarías tus propiedades cada semana? ¿Qué te haría hacerlo?"* ("Would you confirm your listings every week? What would make you do it?")
5. *"Si costara algo, ¿cuánto tendría sentido?"* ("If it cost something, how much would make sense?")
6. *"¿Qué le falta o qué cambiarías?"* ("What's missing or what would you change?")

## 5. After the session

- [ ] On the participant's phone, open `https://mvp-real-estate-app.vercel.app/#/debug` and copy the event summary into the notes. It includes the parser's detected-field count (`publish_parsed`), the share mode (`share_click.mode`) and whether they confirmed in bulk (`confirm_available.bulk`).
- [ ] Finish `participant-N.md` within the same day, while the details are fresh.
- [ ] Update the running totals in [results.md](results.md).
