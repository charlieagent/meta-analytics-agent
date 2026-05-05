# Benchmark Frantoio Lólio Gift — Versione 1

**Data di estrazione**: maggio 2026
**Periodo di riferimento**: ottobre 2025 → aprile 2026 (7 mesi post-svolta funnel)
**Fonte dati**: export Meta Ads Manager (mensile 24m + giornaliero 6m + adset 12m)
**Funnel**: form nativo Meta + Advantage+ Audience (Interessi)

**Revisione**: rev. 1.1 (maggio 2026)
- Riferimenti corretti ai tag XML del Client Profile (era "Sezione 4")
- TODO consolidati nel Client Profile (questo file rimanda)
- Linguaggio ammorbidito su RT_Boccette per evitare bias dell'agente

---

## 0. Cosa è cambiato a ottobre 2025 (svolta operativa)

A ottobre 2025 il funnel è passato da **landing page con form esterno** a **form nativi Meta Lead Gen ad alta intenzione**. Questo ha causato un calo del CPL del prospect del ~60%.

⚠️ **Implicazione critica per l'agente**: i dati pre-ottobre 2025 NON sono confrontabili con quelli post-ottobre 2025 a livello di efficienza CPL. Il benchmark ufficiale si basa SOLO sui 7 mesi post-svolta.

⚠️ **Limite di interpretazione**: il calo di CPL post-svolta riflette anche un cambio di "qualità lead" (form nativo = lead più "leggero", landing = lead più "qualificato"). La verifica della qualità reale dei lead va fatta nel CRM/Delera, non in Meta. Vedere `known_blind_spot` nel Client Profile.

---

## 1. Tipologia di campagne attive (mappa post-svolta)

### CORE Prospect (Advantage+ Interessi)
Logica naming: `[LINEA]_Advantage+_Interessi_[Destinazione]`

| Adset | Periodo | Spesa | Lead | CPL aggregato |
|---|---|---|---|---|
| BOCCETTE_Advantage+ | mag 2025-gen 2026 | €4.526 | 1.049 | €4,31 |
| **CERAMICHE_Advantage+** | ott 2025-apr 2026 | €4.317 | 2.641 | **€1,63** ⭐ |
| NOPRINCIPE_Advantage+ | mar 2026-apr 2026 | €1.964 | 430 | €4,57 |
| GLASS_Advantage+ | apr 2026-attiva | €706 | 134 | €5,27 |

**Logica strategica delle audience**:
- Stesse audience Advantage+ Interessi per tutti
- **Differenziazione attraverso le creative**, non via targeting
- NOPRINCIPE = creative senza Piccolo Principe + copy matrimonio (per filtrare audience più matrimoni-oriented)
- GLASS = solo creative Glass Collection (lancio recente)

### RETARGETING (VideoViews+ 365gg)
Logica naming: `VideoViews+_[Linea]_365gg`

| Adset | Periodo | Spesa | Lead | CPL aggregato |
|---|---|---|---|---|
| **VideoViews+_Ceramic_365gg - TEST** | ott 2025-apr 2026 | €5.782 | 3.037 | **€1,90** ⭐ |
| VideoViews+_Ceramic_365gg | mag 2025-ott 2025 | €4.828 | 1.519 | €3,18 |
| VideoViews+_Boccette_365gg | mag 2025-nov 2025 | €1.616 | 286 | €5,65 |
| VideoViews+_Boccette_365gg - TEST | ott 2025-mar 2026 | €1.356 | 366 | €3,70 |

⚠️ **Nota naming**: il suffisso "TEST" su alcuni adset NON significa che siano in fase di test. Sono test storici che sono stati promossi come versione consolidata. **L'agente NON deve trattare automaticamente "TEST" nel nome come un test attivo** — va verificato.

### SPOT
- **2026_Wedding_Ceramic** (gen-apr 2026): €3.650 spesi, 1.378 lead, CPL €2,65
- Spot stagionale per cerimonie matrimoniali

### TEST
- 2 micro-test in ottobre 2025, durata 1 mese ciascuno (ora chiusi)

---

## 2. CPL di riferimento per tipologia × fase stagionale

### 🔵 CORE PROSPECT (Advantage+)

| Fase | CPL atteso | Note |
|---|---|---|
| **Golden window** (gen-feb) | **€1,10 – €1,55** | Massima efficienza |
| Pre-Natale (ott-nov) | €2,00 – €3,00 | Asta inizia a salire |
| Natale pieno (dic) | €3,00 – €3,50 | Volume alto, costi medio-alti |
| Post-golden (mar-apr) | €2,50 – €4,50 | Volume cala, costi salgono |
| **Soglia allerta** | **> €5** nel periodo, **> €2** in golden window | |

### 🔵 RT_CERAMIC

| Fase | CPL atteso |
|---|---|
| Golden window | €1,28 – €1,40 |
| Resto anno | €1,90 – €3,00 |
| **Soglia allerta** | **> €4** (audience saturata o messa male) |

### 🔵 RT_BOCCETTE

| Fase | CPL atteso |
|---|---|
| Range tipico | €2,80 – €5,40 |
| Performer più debole della famiglia retargeting | |
| **Soglia allerta** | **> €6** |

### 🔵 SPOT WEDDING (campagna `2026_Wedding`, adset `2026_Wedding_Ceramic`)

⚠️ **Importante**: questa campagna ha un **obiettivo strategico esplicito** dichiarato da Claudio: presidio dell'audience cerimonie per tutta la stagione (gen → set), accettando CPL più alto fuori golden window. NON va valutata sulla sola efficienza CPL.

| Periodo | CPL osservato | Lettura strategica |
|---|---|---|
| Gen 2026 | €1,91 (361 lead) | Massima efficienza, golden window |
| Feb 2026 | €1,67 (416 lead) | Picco di efficienza |
| Mar 2026 | €3,25 (347 lead) | CPL fisiologicamente in salita, presidio attivo |
| Apr 2026 | €4,46 (254 lead) | CPL alto ma coerente con scelta presidio |

**Pattern atteso e accettabile** (NON è un alert):
- CPL cresce gradualmente da marzo in poi
- Volume lead in calo da marzo in poi
- Spesa anche raddoppiata mar-apr senza aumento volume → atteso

**Quando diventa un vero alert** (da segnalare):
- CPL > €6 sostenuto (oltre il "presidio caro")
- Volume lead < 100/mese in golden window (gen-feb) → sintomo di fatigue creative o problema setup
- Pattern di performance NON coerente col presidio dichiarato (es. crollo improvviso a febbraio)

**Timing replicabile per future SPOT Wedding annuali**:
- Attivazione: fine dicembre / inizio gennaio
- Mantenimento: tutta la stagione cerimonie (gen → set)
- Da rivalutare: ogni anno se la strategia di presidio cambia

---

## 3. Frequenza di riferimento

- **Core prospect**: range tipico **1,7 – 2,5** | allerta > 3
- **Retargeting**: range tipico **2,0 – 2,6** | allerta > 3,5

⚠️ **Importante**: il retargeting Frantoio non sale tipicamente sopra 2.6, anche dopo lunghi periodi di attività. La soglia generale di settore "frequenza retargeting accettabile fino a 5-7" **è troppo permissiva per il caso Frantoio**. Usare le soglie locali sopra.

---

## 4. CTR di riferimento (Link CTR)

- **Range tipico**: 0,9% – 1,5%
- **CTR per linea**: Boccette tipicamente più basso (0,6% – 0,9%)
- **Allerta**: < 0,5% sostenuto per più giorni

---

## 5. CPM di riferimento

| Tipologia | Range tipico |
|---|---|
| Core prospect | €2,50 – €4,00 |
| RT Ceramic | €3,30 – €4,60 |
| RT Boccette | €5,00 – €6,50 |
| **Allerta tempesta asta** | CPM > 1,5x mediana stagionale |

---

## 6. Andamento mensile aggregato — la "Golden Window" quantificata

Vista aggregata di tutte le campagne attive (escluso TEST):

| Mese | Spesa | Lead | CPL | Note |
|---|---|---|---|---|
| Ott 2025 | €1.968 | 679 | €2,90 | Inizio nuovo setup |
| Nov 2025 | €1.734 | 697 | €2,49 | Pre-Natale |
| Dic 2025 | €2.256 | 726 | €3,11 | Natale alto |
| **Gen 2026** | **€3.171** | **2.062** | **€1,54** | **Golden Window** ⭐ |
| **Feb 2026** | **€2.579** | **1.809** | **€1,43** | **Golden Window** ⭐ |
| Mar 2026 | €3.728 | 1.504 | €2,48 | Post-golden |
| Apr 2026 | €4.089 | 1.053 | €3,88 | Volume in calo |

**Insight strategici quantificati**:
- L'efficienza Gen-Feb è **circa 2x** quella degli altri mesi
- Il volume lead Gen-Feb è **circa 2,5x** quello degli altri mesi
- Il momento di scalare budget è **inizio gennaio**, non quando il CPL è già crollato (sarebbe tardi)

---

## 7. Pattern di transizione stagionale visibili nei dati

- **Inizio finestra "alta intensità"**: gennaio (calo CPL marcato)
- **Fine finestra "alta intensità"**: fine febbraio (CPL inizia a salire a marzo)
- **Aprile = transizione difficile**: CPL sale del 60% vs marzo
- **Da maggio in avanti** (non coperto da questi dati): atteso ulteriore calo volume secondo esperienza qualitativa di Claudio

---

## 8. Limiti di questi benchmark (da sapere)

1. **Periodo limitato**: 7 mesi post-svolta, una sola "stagione" di Golden Window osservata
2. **Manca confronto multi-anno**: non possiamo dire se questo Gen-Feb 2026 è stato "tipico" o atipico
3. **Manca giudizio qualità lead**: tutto è basato su Meta, nessun dato CRM/Delera
4. **Adset NOPRINCIPE e GLASS troppo recenti**: solo 1-2 mesi di osservazione, non baseline solide

**Quando ricalibrare**: dopo il prossimo ciclo Gen-Apr 2027 (consolidamento di un secondo anno).

---

## 9. Osservazioni che meritano attenzione operativa

### Aprile 2026: CPL anomalo o normale?
CPL aprile 2026 (€3,88 aggregato) è quasi 3x quello di febbraio. Da capire se è solo "uscita golden window" o se c'è anche fatigue del pool creativo / pressione competitiva specifica. Da approfondire con dati per ad.

**Importante**: parte di questo CPL alto aprile è dovuto al peso della SPOT 2026_Wedding (mantenuta on per scelta strategica di presidio). Se si sterilizza la SPOT, il CPL aggregato ad aprile sulla parte non-spot va ricalcolato per capire il dato "puro".

### RT_Boccette: performer più debole della famiglia retargeting
Spesa €1.587 in 6 mesi per soli 408 lead, CTR ~0,7%. Audience VideoViews 365gg Boccette appare meno reattiva del Ceramic equivalente. Considerare refresh del pool video di partenza. Da rivalutare periodicamente: la performance non è da considerarsi strutturalmente irrecuperabile, ma sotto-mediana rispetto al RT_Ceramic.

### NOPRINCIPE in fase di valutazione
Lanciato a marzo 2026 (post-golden), CPL €4,57 in 2 mesi. Da rivalutare quando avrà attraversato una golden window completa.

### GLASS troppo recente
Solo aprile 2026 (1 mese), CPL €5,27. Periodo sfavorevole per il lancio (post-golden). Da rivalutare a fine 2026.

---

## 10. TODO operativi associati a questi benchmark

I TODO operativi specifici Frantoio sono mantenuti in modo consolidato nel Client Profile (`<frantoio_todos>`), che è la fonte autorevole.

I TODO che hanno relazione diretta con questi benchmark sono:
- **CRM-001**: validare la qualità lead post-svolta vs pre-svolta nel CRM
- **BENCHMARK-001**: approfondire l'anomalia CPL aprile 2026
- **BENCHMARK-002**: ricalibrazione benchmark dopo Gen-Apr 2027 → `benchmark_v2.md`
- **NAMING-001**: convenzione TEST veri vs promossi
- **BOCCETTE-001**: refresh pool video Boccette

Per la formulazione completa e priorità di ognuno, vedere `<frantoio_todos>` nel Client Profile.
