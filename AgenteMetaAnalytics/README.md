# Agente Meta Ads — Architettura modulare

> Sistema di analisi automatizzata delle campagne Meta Ads di proprietà di Claudio.
> Pensato per supportare la consulenza Meta Ads su più clienti con riuso massimo di knowledge e codice.
>
> **Versione architetturale**: v1.0 (maggio 2026)
> **Primo cliente attivo**: Frantoio Paparella (LÓLIO Gift)

---

## Struttura del repository

```
agente_meta_ads/
│
├── core/
│   └── core_agent.md                    [LIVELLO 1] Identità + regole inviolabili
│
├── framework/
│   └── analytical_framework.md          [LIVELLO 2] Metodo + metriche + soglie + alert + automation
│
├── client_profiles/
│   └── frantoio_paparella/
│       ├── client_profile.md            [LIVELLO 3] Cliente specifico
│       └── benchmark_v1.md              Numeri di riferimento (soglie dinamiche)
│
├── config/
│   └── runtime_config_template.yaml     [LIVELLO 4] Parametri tecnici (NON caricato in Claude)
│
└── README.md                            Questo file
```

---

## I 4 livelli — cosa contiene cosa

### Livello 1 — Core Agent
**File**: `core/core_agent.md`
**Caratteristica**: universale, immutabile.
**Cosa contiene**: chi è l'agente, come comunica, postura cognitiva, self-awareness, regole inviolabili, gerarchia di priorità in caso di conflitto.
**Quando si modifica**: solo per evoluzione filosofica/comportamentale dell'agente. Beneficia tutti i clienti.

### Livello 2 — Analytical Framework
**File**: `framework/analytical_framework.md`
**Caratteristica**: universale, evolutivo.
**Cosa contiene**: i 9 principi metodologici, le metriche universali (4 famiglie), le metriche secondarie diagnostiche, le soglie statiche generali, la logica di alert e formato report, le regole di volume/aggregazione, il framework universale per le automation, il framework opzionale di customer care support.
**Quando si modifica**: per evoluzione algoritmo Meta (Andromeda → versione successiva), nuove metriche, raffinamento metodologico.

### Livello 3 — Client Profile
**Cartella**: `client_profiles/[nome_cliente]/`
**Caratteristica**: specifico per cliente.
**Cosa contiene** per ogni cliente:
- `client_profile.md` — tutto ciò che è specifico (identità azienda, struttura campagne, geografia, target, stagionalità, vincoli linguistici, principi creative, knowledge maintenance, customer care config, automation specifiche, soglie dinamiche)
- `benchmark_v1.md` — numeri di riferimento calibrati sui dati storici reali
- (futuri) `notes_storiche.md` — log di eventi/decisioni cliente nel tempo

**Quando si modifica**: per evoluzione del cliente (nuove linee, cambi strutturali, nuovi obiettivi).
**Quando si sostituisce**: cambio cliente totale.

### Livello 4 — Runtime Config
**File**: `config/runtime_config_template.yaml`
**Caratteristica**: specifico per istanza tecnica.
**Cosa contiene**: account Meta IDs, credenziali (in vault), canali di comunicazione, schedulazione cron, parametri operativi.
**NON caricato in Claude come prompt**: configurazione del wrapper tecnico (n8n).

---

## Come si compone il prompt finale per Claude

Quando l'agente parte per analizzare il cliente attivo:

```
PROMPT FINALE = 
  core_agent.md 
  + analytical_framework.md 
  + client_profiles/[cliente_attivo]/client_profile.md 
  + client_profiles/[cliente_attivo]/benchmark_v1.md
```

**Token totali stimati per Frantoio**: circa 6.000 token totali (era ~12.000 nella v1 monolitica originale).

---

## Workflow operativi

### Aggiungere un nuovo cliente

1. Creare cartella `client_profiles/[nome_nuovo_cliente]/`
2. Copiare `client_profile.md` di Frantoio come template
3. Sostituire integralmente il contenuto con i dati del nuovo cliente, mantenendo la struttura XML
4. Costruire `benchmark_v1.md` partendo da estrazione dati Meta del nuovo cliente (vedere processo seguito per Frantoio)
5. Aggiungere il nuovo cliente in `config/clients.yaml`, indicando timezone, env var Meta e percorsi file
6. Cambiare `active_client` in `config/clients.yaml` se il workflow deve lavorare su quel cliente
7. Test su 7-14 giorni in modalità osservativa
8. Attivazione

**Tempo stimato** per onboardare un cliente con caratteristiche simili a Frantoio: 1-2 giornate (vs ~2 settimane di lavoro from-scratch).

### Aggiornare il framework algoritmico

Quando Meta rilascia novità (es. nuova versione Andromeda):
1. Modificare `framework/analytical_framework.md`
2. Bumpare versione (`v1.0` → `v1.1`)
3. Documentare nel changelog del file
4. Bumpare riferimento in `runtime_config_template.yaml` sotto `loaded_modules.analytical_framework_version`
5. **Beneficia automaticamente tutti i clienti attivi**

### Aggiornare i benchmark di un cliente

Quando si conclude un nuovo ciclo di osservazione (es. dopo 12 mesi di dati):
1. Estrarre nuovi dati Meta del cliente
2. Costruire `benchmark_v2.md` (mantenere v1 in archivio)
3. Aggiornare riferimento nel `client_profile.md` del cliente
4. Bumpare versioni

### Aggiungere una nuova automation

1. Definirla nel `client_profile.md` del cliente sotto `<*_automation>` seguendo la struttura del `operational_automations_framework`
2. Definire perimetro, trigger, criteri, notifica, calibrazione
3. Configurare cron in `runtime_config_template.yaml`
4. I primi 30 giorni l'agente opera in modalità "annuncia e aspetta" (default del framework)

---

## Stato attuale del sistema

### Lavoro concettuale
- ✅ Core Agent v1.0 — completo
- ✅ Analytical Framework v1.0 — completo
- ✅ Client Profile Frantoio Paparella v1.0 — completo
- ✅ Benchmark Frantoio v1 — completo (calibrato su dati ott 2025-apr 2026)
- ✅ Runtime Config template v1.1 — completo
- ✅ Architettura modulare definitiva

### Lavoro tecnico (da fare)
- ⏸ Setup n8n self-hosted
- ⏸ Configurazione Meta Marketing API
- ⏸ Configurazione Telegram bot + email
- ⏸ Test calibrazione 30 giorni
- ⏸ Go-live

### TODO operativi alti (specifici Frantoio)
- 🔴 Verificare attivazione e qualità Conversion API
- 🟡 Estrarre dataset CRM lead-to-customer per ridurre punto cieco
- 🟢 Test introdurre carousel nel pool dynamic core
- 🟢 Considerare campagne VideoViews-only in golden window

---

## Principi architetturali alla base

Il sistema è stato progettato seguendo queste decisioni guida:

1. **Modularità per riuso multi-cliente**: ogni cliente è una cartella sostituibile, il Core e il Framework sono universali.

2. **Best practice Anthropic 2025-2026**: 
   - Goldilocks zone (principi euristici, non if-else hardcoded)
   - Brevità ad alto segnale
   - XML structuring
   - Curatura euristica (esempi diversi e canonici, non liste ridondanti)

3. **Self-awareness e gestione del dubbio**: l'agente riconosce i propri limiti, non bluffa, dichiara incertezza, chiede a Claudio quando appropriato.

4. **Valutazione strategica vs efficienza pura**: le campagne si giudicano sull'obiettivo strategico esplicito, non solo sulle metriche assolute.

5. **Disciplina statistica**: sotto-soglie di volume → osservazione, non alert. Mai concludere su rumore statistico.

6. **Distinzione domanda vs asta**: ogni movimento di CPL si analizza distinguendo le due forze.

7. **Coerenza prodotto-claim**: ogni linea ha vincoli linguistici e credenziali specifiche; gli errori di coerenza sono alert.

8. **Confidenzialità verso il cliente**: l'esistenza dell'agente è informazione privata di Claudio.

---

## Versioning del repository

| Componente | Versione attuale | Ultima modifica |
|---|---|---|
| Architettura | v1.0 | maggio 2026 |
| Core Agent | v1.1 | maggio 2026 |
| Analytical Framework | v1.3 | maggio 2026 |
| Client Profile Frantoio | v1.2 | maggio 2026 |
| Benchmark Frantoio | v1 (rev. 1.1) | maggio 2026 |
| Runtime Config template | v1.1 | maggio 2026 |

### Cosa Ã¨ cambiato in v1.3 / runtime v1.1 (timezone Frantoio)

**Analytical Framework**: corretta la regola temporale: l'agente interpreta 
"ieri", "oggi", "questa settimana" e le rolling window sempre nel timezone 
operativo di Claudio/cliente, compensando eventuali timezone errati 
dell'Ad Account Meta.

**Runtime Config template**: separati `operational_timezone` e 
`account_timezone`; per Frantoio l'operativo resta `Europe/Rome`, mentre 
l'account Meta ereditato resta `America/Los_Angeles`.

### Cosa è cambiato in v1.1 (audit di consolidamento)

**Core Agent**: riferimenti agnostici al Client Profile, eliminata ridondanza con Framework, aggiunta nota su delegazione esplicita gerarchica.

**Analytical Framework**: aggiunto blocco `<targeting_principles>` (universale), rimosso `<purchase_static>` (verrà aggiunto quando attivo), chiarezza sul modulo customer care opzionale.

**Client Profile Frantoio**: `<client_overview>` strutturato con sub-tag, ecommerce time-agnostic, aggiunto `<frantoio_profile_visits_alerts>`, rinomina `creative_naming_status`, TODO consolidati come single source of truth.

**Convenzione riferimenti**: tutti i `vedere ...` sono uniformi nel formato `vedere \`<tag>\` in [FILE]`.

---

## Contatti operativi

Owner: Claudio
Email: br1claud@gmail.com

In caso di necessità di modifiche strutturali al sistema, riprendere la conversazione di setup con Claude Opus dove è stato co-progettato il sistema.
