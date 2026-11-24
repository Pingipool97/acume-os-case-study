# Acume OS — case study

> Il codice è privato: è il sistema operativo interno di un'azienda. Qui racconto cosa ho costruito, come e perché.

**Acume OS** è la dashboard operativa di Acume, un'azienda in cui ogni ruolo è un agente AI. L'ho progettata e sviluppata da solo, ed è in produzione: gestisce clienti veri, messaggi veri e offerte vere.

**Stack:** Next.js 15 (App Router, TypeScript strict) · Supabase (Postgres, 70+ migrazioni SQL) · Vercel · Stripe · Anthropic e OpenAI · WhatsApp Cloud API · Sentry

---

## Il problema

Una piccola azienda di servizi AI deve fare tutto: trovare clienti, rispondere ai messaggi, preparare offerte, costruire demo, seguire i progetti, controllare i costi. Acume OS mette questi lavori in mano a una squadra di agenti, con una persona sola che approva.

## Cosa fa

- **14 agenti in 7 reparti.** Ogni agente ha un ruolo (scouting dei clienti, prima risposta, offerte, voce, monitoraggio). Aggiungere un agente vuol dire aggiungere una voce a un elenco: niente icone o configurazioni sparse.
- **Assistente su WhatsApp.** Legge i dati con il tool calling, crea bozze di offerte e anagrafiche clienti. Tutto ciò che tocca un cliente (invio di un'offerta, email) non parte da lì: l'assistente risponde con il link alla pagina dove approvare.
- **Fabbrica delle demo.** Una squadra di agenti cloud (ricerca, progetto, costruzione, collaudo, revisione) costruisce una demo navigabile per il cliente. Una persona approva con un pulsante monouso a scadenza, poi al cliente parte solo l'email con il PDF.
- **Monitor dei progetti dei clienti.** Disponibilità, certificati in scadenza ed errori di tutte le app consegnate, con uno standard unico per qualunque tecnologia. Email solo per quello che blocca, il resto nella scheda.

## Le scelte di progetto che contano

1. **Il modello propone, il codice decide.** Il modello dice *chi* e *cosa*; prezzi, dati del cliente e regole li decide il codice. Un dato dell'anagrafica si salva solo se è scritto a parole intere nel messaggio dell'utente. Un comando che nasce dal testo di un cliente (prompt injection) non crea niente.
2. **Ogni chiamata a pagamento passa da un registro dei costi.** Ogni chiamata a un modello o a un servizio registra quanto costa. Così il costo di ogni agente è visibile.
3. **Le integrazioni si spengono da sole.** Un servizio senza chiave fa "niente, tracciato" invece di rompere la dashboard.
4. **Stati che cambiano in un punto solo.** Gli stati di una richiesta cambiano solo con una funzione SQL che parte dallo stato atteso e scrive l'evento nella stessa transazione. Due processi insieme non si pestano i piedi.
5. **Privacy prima dell'osservabilità.** Gli errori vanno a Sentry passando da un filtro che toglie i dati personali, con un tetto giornaliero.
6. **Revisione prima di ogni rilascio.** Typecheck, build, una batteria di verifiche offline e una revisione del diff da parte di un secondo modello (Codex) prima di ogni push in produzione.

## Come ci ho lavorato

Sviluppo con Claude Code come compagno di lavoro: io decido l'architettura e le regole, scrivo le specifiche e verifico ogni modifica. Le regole del progetto stanno in un file unico che leggono tutti gli strumenti, così non si contraddicono.

---

### In English

Acume OS is the internal operating system of Acume, a company where every role is an AI agent. I designed and built it on my own (Next.js, TypeScript, Supabase, Vercel). It runs in production with 14 agents, a WhatsApp assistant with tool calling and strict validation, a cloud multi-agent "demo factory", a client-app monitor, a per-call cost ledger, and a privacy filter in front of Sentry. Code is private; this page describes the architecture and design choices.
