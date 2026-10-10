# Creative Scout — Design

> **Stato**: design approvato in conversazione, in attesa di revisione scritta
> **Data**: 2026-10-10
> **Owner**: Claudio
> **Primo cliente**: Frantoio Paparella (LÓLIO Gift)

---

## 1. Obiettivo

Un nuovo agente che **ogni 2 settimane**:

1. raccoglie le ads attive di concorrenti e prodotti simili dalla Meta Ad Library;
2. stima quali stanno funzionando e ne ricava i trend creativi;
3. propone nuove creatività (brief + testi pronti);
4. pubblica tutto su una pagina web privata, con immagini e video di riferimento visibili, e invia il link a Claudio via **email e Telegram**.

L'output è solo per Claudio (regola inviolabile 1 di `core_agent.md`).

### Successo significa

- Claudio riceve puntualmente il report il 1° e il 15 del mese.
- Le ads di riferimento sono pertinenti al prodotto del cliente (poco rumore).
- Almeno una parte delle proposte è utilizzabile come base per una nuova campagna dopo revisione.
- Nessun numero inventato, nessuna violazione dei vincoli linguistici del cliente.

### Fuori perimetro (v1)

- Generazione AI di immagini o video (scelta esplicita: solo brief e testi).
- Confronto con le performance delle campagne del cliente (API Meta non ancora configurate; da aggiungere in v2).
- Trend dal web generico (Tavily o simili): escluso per ora, aggiungibile in seguito.
- Applicazione web con filtri avanzati/preferiti.

---

## 2. Decisioni prese

| Tema | Decisione | Motivo |
|---|---|---|
| Fonte ads | Apify, actor già pronto per la Meta Ad Library | Più stabile e meno manutenzione dello scraping fatto in casa |
| Selezione ads | Lista fissa di concorrenti **+** parole chiave, per cliente | Continuità tra report + scoperta di nuovi inserzionisti |
| Output creativo | Brief + testi pronti (opzione A) | Costi e complessità bassi |
| Interfaccia | Pagina web privata (Artifact claude.ai) con archivio | Leggibile, nessun server da gestire |
| Notifica | Email + Telegram | Canali già previsti dal sistema |
| Esecuzione | Routine programmata di Claude Code (sessione cloud) | Nessuna infrastruttura; riusa direttamente i file del repo |
| Frequenza | 1° e 15 di ogni mese, 07:45 Europe/Rome | "Ogni 2 settimane" espresso in modo affidabile |

---

## 3. Limiti strutturali dichiarati

- **Le metriche di performance dei concorrenti non sono pubbliche.** Il "successo" è stimato con proxy: giorni di attività, numero di varianti attive dello stesso inserzionista, copertura UE (quando la Ad Library la espone). Il report li presenta sempre come stime.
- **Video**: l'agente li analizza tramite 4-6 fotogrammi chiave e la trascrizione dell'audio; non osserva ritmo o montaggio e lo dichiara.
- **Termini Meta**: lo scraping automatico non è autorizzato da Meta; il rischio tecnico ricade sul servizio Apify, l'uso resta interno e a basso volume.
- **Diritti d'autore**: i materiali dei concorrenti sono riferimento, mai ripubblicati o copiati.

---

## 4. Architettura

```
Routine Claude Code (cron: CRON_TZ=Europe/Rome 45 7 1,15 * *)
        │
        ▼
Sessione cloud sul repo meta-analytics-agent
        │  legge: core_agent.md, analytical_framework.md,
        │         client_profile.md, benchmark_v1.md,
        │         modules/creative_scout/creative_scout_agent.md,
        │         client_profiles/<cliente>/creative_scouting.yaml
        ▼
Script di supporto (modules/creative_scout/scripts/)
  1. collect   → chiama Apify, salva JSON grezzo
  2. media     → scarica immagini/video, estrae fotogrammi + trascrizione
  3. score     → calcola proxy di successo, confronta con lo storico
        │
        ▼
Claude (nella sessione): filtro pertinenza, analisi trend,
proposte, autocontrollo
        │
        ▼
Pubblicazione: Artifact privato (index + report del giorno + media)
Storico: client_profiles/<cliente>/scouting_history/<data>.json (commit)
Notifica: email (connettore Gmail) + Telegram (Bot API)
```

### 4.1 Unità e responsabilità

| Unità | Cosa fa | Dipende da |
|---|---|---|
| `modules/creative_scout/creative_scout_agent.md` | Regole e processo dell'agente (universale, multi-cliente) | `core_agent.md`, `analytical_framework.md` |
| `modules/creative_scout/routine_prompt.md` | Istruzione passo-passo che la routine esegue a ogni avvio | i file sopra |
| `client_profiles/<cliente>/creative_scouting.yaml` | Concorrenti, parole chiave, paese, limiti, soglia costo | — |
| `scripts/collect` | Chiamata Apify con tetto ads e tetto spesa; salva JSON grezzo | `APIFY_API_TOKEN` |
| `scripts/media` | Download media; per i video: fotogrammi (ffmpeg) + trascrizione | ffmpeg, modello di trascrizione locale |
| `scripts/score` | Proxy di successo, deduplica varianti, diff con storico | JSON grezzo + ultimo storico |
| `scripts/notify` | Invio messaggio Telegram con link | `TELEGRAM_BOT_TOKEN`, `TELEGRAM_CHAT_ID_CLAUDIO` |
| Template pagina | HTML del report e dell'indice archivio | — |

Le parti meccaniche (chiamate API, download, conteggi) sono script deterministici; le parti di giudizio (pertinenza, trend, proposte, autocontrollo) sono di Claude. Ogni script si può eseguire e testare da solo.

### 4.2 Formato `creative_scouting.yaml` (esempio)

```yaml
client: frantoio_paparella
country: IT
language: it
competitors:          # pagine Facebook/Instagram da monitorare (fornite da Claudio)
  - name: "<concorrente>"
    page_id: "<id pagina>"
keywords:
  - "olio regalo"
  - "confezione regalo olio"
  - "gift box gastronomica"
limits:
  max_ads_collected: 150
  max_reference_ads: 20
  max_proposals: 5
  max_cost_eur_per_run: 5
```

I valori reali di concorrenti e parole chiave sono forniti da Claudio prima del primo avvio.

---

## 5. Processo a ogni esecuzione

1. **Carica istruzioni e configurazione** del cliente attivo.
2. **Raccolta**: ads attive in Italia per ogni concorrente e ogni parola chiave, entro `max_ads_collected` e `max_cost_eur_per_run`.
3. **Media**: download immediato (i link Meta scadono). Video → 4-6 fotogrammi + trascrizione.
4. **Filtro e punteggio**: Claude scarta le ads non pertinenti; lo script calcola i proxy (giorni attivi, varianti, copertura UE); selezione delle migliori `max_reference_ads`.
5. **Confronto con lo storico**: classifica ogni ad come nuova / spenta / ancora attiva; le ads che persistono tra più report sono il segnale più forte.
6. **Analisi trend** su: categorie di hook (mappate su `frantoio_hook_taxonomy`), formati, occasioni, offerte, CTA, stile visivo. Ogni trend etichettato *nuovo / in crescita / stabile / in calo*.
7. **Proposte** (max `max_proposals`): brief nel formato `brief_template` + testi pronti (testo principale, titolo, CTA) in 2 varianti + trend di origine + ads di ispirazione.
8. **Autocontrollo**: vincoli linguistici del cliente (`line_constraints`), nessun numero inventato, stime marcate, somiglianza eccessiva con un'ad di riferimento.
9. **Pubblicazione** della pagina e aggiornamento dell'indice; **commit dello storico**; **notifica** email + Telegram con il link.

---

## 6. Pagina del report

- **Riepilogo** (5 righe): periodo, ads raccolte/selezionate, 2-3 trend principali, avvisi.
- **Trend della quindicina**: schede con titolo, descrizione, numero di ads, etichetta di andamento.
- **Ads di riferimento**: griglia di schede (immagine o video + fotogrammi, inserzionista, giorni attivi, varianti, copertura, testo, motivo della scelta, link Ad Library); filtri per concorrente, formato, trend.
- **Proposte creative**: brief, testi in 2 varianti con tasto "copia", trend di origine, miniature di ispirazione, esito autocontrollo (✓ / ⚠ con spiegazione).
- **Note e limiti**: metodo di stima, cosa l'agente non poteva vedere.
- **Archivio**: pagina indice allo stesso link con tutti i report passati.

Italiano, leggibile da telefono, tema chiaro/scuro.

---

## 7. Regole dell'agente

Eredita tutte le `inviolable_rules` di `core_agent.md`. In più:

1. **Ispirazione, mai copia**: si riprende la struttura (hook, formato, occasione), mai frasi, immagini o slogan identici.
2. **Successo concorrenti = stima**, sempre con i proxy esplicitati; nessuna metrica di performance inventata.
3. **Vincoli del cliente prima dei trend**: un trend incompatibile (es. "biologico", "DOP" per la linea Gift) è riportato come trend di mercato ma non diventa proposta, con spiegazione.
4. **Testi pronti = proposte** da rivedere e firmare da Claudio.
5. **Video: solo ciò che è osservabile** da fotogrammi e trascrizione, dichiarato.
6. **Media dei concorrenti solo nella pagina privata**; nel repository solo storico testuale.
7. **Costi**: tetti su ads, proposte e spesa Apify per esecuzione; superata la soglia, l'agente si ferma e avvisa.

---

## 8. Gestione errori

| Situazione | Comportamento |
|---|---|
| Apify fallisce o restituisce 0 ads | Nessun report inventato; email + Telegram con descrizione dell'errore |
| Poche ads (< 10 pertinenti) | Report pubblicato con avviso in cima; proposte ridotte o assenti |
| Un concorrente senza ads attive | Riportato negli avvisi |
| Download di un media fallito | Scheda mostrata senza media, con nota |
| Trascrizione non disponibile | Analisi video solo da fotogrammi, dichiarata |
| Soglia costo superata | Stop prima della raccolta successiva, avviso |
| Telegram non configurato o in errore | Resta l'email; errore segnalato nel report |

---

## 9. Modifiche ai file esistenti (solo aggiunte)

- `core/core_agent.md`: precisare in `<self_awareness>` che il modulo Creative Scout vede immagini e fotogrammi video; bump versione v1.2.
- `client_profiles/frantoio_paparella/client_profile.md`: in `<brief_template>` aggiungere l'eccezione per i testi pronti del Creative Scout; bump versione.
- `client_profiles/frantoio_paparella/creative_scouting.yaml`: nuovo.
- `config/clients.yaml`: aggiungere per il cliente la sezione `creative_scout` (cron, env var).
- `config/runtime_config_template.yaml`: aggiungere il task programmato `creative_scout`.
- `README.md`: nuovo modulo nella struttura, nei workflow e nella tabella versioni.

---

## 10. Prerequisiti (a cura di Claudio, guidati)

- Account Apify e token, salvato come `APIFY_API_TOKEN` nelle impostazioni dell'ambiente cloud (mai in chat).
- Bot Telegram creato con @BotFather; `TELEGRAM_BOT_TOKEN` e `TELEGRAM_CHAT_ID_CLAUDIO` nelle impostazioni dell'ambiente.
- Accesso di rete dell'ambiente consentito verso `api.apify.com` e `api.telegram.org` (e i domini dei media Meta per il download).
- Connettore Gmail attivo per la routine.
- Lista concorrenti e parole chiave per Frantoio.

---

## 11. Verifica e collaudo

- Ogni script testato da solo con dati di esempio salvati (nessuna chiamata reale nei test).
- Prima esecuzione **manuale** della routine, revisionata insieme a Claudio.
- Le prime 2 esecuzioni programmate (≈ 30 giorni, coerente con `calibration_phase_days`) sono in **modalità osservativa**: Claudio valuta pertinenza delle ads e utilità delle proposte; si calibrano parole chiave, tetti e punteggio.

---

## 12. Da scegliere nella fase di piano (con criteri)

- **Actor Apify specifico**: deve restituire URL diretti di immagini/video, data di inizio, piattaforme, copertura UE quando disponibile, e permettere un tetto di risultati; si sceglie confrontando costo per 150 ads e campi restituiti su una prova reale.
- **Strumento di trascrizione**: deve girare nell'ambiente cloud senza chiavi aggiuntive (modello locale leggero); se troppo lento, si ripiega su soli fotogrammi (vedi §8).
