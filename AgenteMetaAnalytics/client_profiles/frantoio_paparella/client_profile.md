# Client Profile — Frantoio Paparella

> **Cosa contiene questo file**: tutto ciò che è specifico del cliente Frantoio Paparella per le campagne LÓLIO Gift. Include identità, vincoli tecnici (timezone), struttura campagne, geografia, target, stagionalità, vincoli linguistici, principi creative, gestione knowledge, supporto customer care, automation, metriche specifiche, alert dedicati Profile Visits, registro consolidato dei TODO operativi.
>
> **Caratteristica**: si carica sopra `core_agent.md` e `analytical_framework.md`. Sostituibile interamente quando si cambia cliente.
>
> **File complementari**: `benchmark_v1.md` (numeri di riferimento per soglie dinamiche).
>
> **Versione**: v1.2 (maggio 2026)
>
> **Changelog v1.2**:
> - Aggiunto blocco `<technical_constraints>` con dichiarazione esplicita del gap timezone (Operativo: Europe/Rome | Account Meta: America/Los_Angeles)
> - Documentate le implicazioni storiche per i benchmark v1
> - Documentata la considerazione strategica sulla migrazione futura
>
> **Changelog v1.1**:
> - `<client_overview>` strutturato con sub-tag XML (history, lines summary, active lines, positioning)
> - `<status>` di `<ecommerce_evolution>` riformulato in modo time-agnostic
> - Rimosso `<andromeda_aware_principle>` da `<targeting>` (spostato nel Framework, è universale)
> - Aggiunto blocco `<frantoio_profile_visits_alerts>` con soglie e trigger dedicati
> - `<naming_status>` rinominato in `<creative_naming_status>` per disambiguare da naming convention adset
> - `<frantoio_todos>` consolidato come single source of truth per tutti i TODO Frantoio (anche quelli del Benchmark v1)
> - Convenzione uniforme per riferimenti incrociati

---

```xml
<client_overview>

<company_history>
Frantoio Paparella è uno storico frantoio di Barletta, Puglia, fondato nel 
1891. Produce olio extra vergine di oliva da cultivar Coratina pugliese.
</company_history>

<product_lines_summary>
Il brand ha più linee di prodotto, articolate in funzione del canale e 
del target:

- LÓLIO (linea base, oggi B2B verso ristorazione professionale)
- LÓLIO Gift (linea regalistica B2C — IN CAMPAGNA OGGI)
- LÓLIO 1891 (lancio previsto 2027, embargo confidenziale)
- Linea Frantoio (B2B, export verso filiera professionale)
</product_lines_summary>

<active_lines>
Solo LÓLIO Gift è oggetto delle campagne attualmente gestite. Le altre 
linee non sono in campagna oggi.

LÓLIO Gift ha sotto-linee in campagna:
- Ceramic Collection
- Boccette LÓLIO
- Glass Collection (più recente)
</active_lines>

<positioning>
Regalistica gourmet premium, fascia medio-alta, italiano artigianale, 
storia di famiglia.
</positioning>

</client_overview>


<technical_constraints>

<timezone_configuration>
Configurazione timezone Frantoio:

TIMEZONE OPERATIVO (la verità, ciò che usa l'agente per ragionare): 
**Europe/Rome**
Frantoio opera in Italia. Tutto il calendario, le festività, gli eventi 
stagionali, i weekend, le campagne stagionali (matrimoni, comunioni, 
cresime), le interazioni con Claudio sono in timezone Roma.

TIMEZONE STRUTTURALE DELL'AD ACCOUNT META (un dettaglio tecnico ereditato): 
**America/Los_Angeles**
L'Ad Account Meta è stato configurato a Los Angeles all'origine (errore 
di setup ereditato, precedente al lavoro attuale di Claudio). Meta non 
permette di cambiare timezone di un account dopo la creazione. Il dato 
storico di 24 mesi è quindi tutto basato su giorni LA.

GAP STRUTTURALE: 9 ore (Roma è UTC+1/+2 a seconda di DST, LA è UTC-7/-8). 
Questo gap impatta il modo in cui Meta API restituisce i dati giornalieri.

Implicazione: l'agente applica integralmente la `<timezone_awareness>` 
del Framework. In particolare:
- MAI usare `date_preset` di Meta API (yesterday, last_7_days, ecc.)
- SEMPRE usare `time_range` esplicito calcolato dal timezone Roma
- Comunicazione con Claudio SEMPRE in timezone Roma (festività italiane, 
  weekend italiani, "ieri" italiano)
- Il timezone LA è dettaglio tecnico, non viene mai esposto nei report 
  quotidiani
</timezone_configuration>

<historical_data_caveat>
Tutti i dati storici Frantoio (inclusi i Benchmark v1) sono stati estratti 
dall'API Meta che li restituisce aggregati per giorno LA. Quindi:

- I benchmark sono internamente coerenti (utili come baseline di confronto 
  vs nuovi dati estratti con stessa logica)
- Ma il loro mapping su festività/calendario reale italiano è "sfocato" 
  per il gap di 9 ore
- Esempio: la "Golden Window gen-feb" identificata nel benchmark è 
  identificata su giorni LA — i picchi reali in calendario italiano 
  potrebbero essere leggermente sfasati di mezza giornata

L'agente ne tiene conto quando confronta dati attuali con i benchmark v1, 
ma per il momento usa i benchmark come riferimento (l'alternativa sarebbe 
ricostruirli da zero, e non è prioritario oggi).
</historical_data_caveat>

<future_migration_consideration>
Una migrazione a un Ad Account con timezone Europe/Rome è pianificabile 
in futuro, idealmente in finestra di bassa stagione (luglio-settembre 
2026 o equivalente). La migrazione richiederebbe:
- Replica delle campagne attive
- Perdita temporanea della learning phase di Andromeda (7-14 giorni di 
  CPL più alti)
- Ricostruzione delle audience custom (VideoViews 365gg)
- Comunicazione coordinata con Frantoio

Decisione strategica corrente: NON è prevista migrazione immediata. 
L'agente convive col gap timezone come configurazione strutturale.
</future_migration_consideration>

</technical_constraints>


<campaign_taxonomy>

<active_product_lines>
LÓLIO Gift è la sola linea in campagna oggi, articolata in: Ceramic 
Collection, Boccette LÓLIO, Glass Collection.

Le altre linee del brand (LÓLIO B2B, Linea Frantoio, LÓLIO 1891) NON 
sono in campagna. Se l'agente rileva attività su linee diverse, lo segnala 
come anomalia da chiarire con Claudio.
</active_product_lines>

<campaign_dimensions>
Ogni campagna si colloca su due assi indipendenti che l'agente identifica 
prima di analizzarla:

ASSE 1 — DURATA E FUNZIONE
- CORE: campagne sempre-on, lunga durata, far lavorare il machine learning
- SPOT: campagne stagionali, vita breve (20-60gg), amplificazione finestre 
  specifiche

ASSE 2 — TIPOLOGIA TARGETING  
- PROSPECT (cold): audience nuove, oggi su Broad/Advantage+ Interessi
- RETARGETING (warm): audience custom basate su interazioni passate
- TEST: sperimentazione di varianti, vita 7-14gg, esito binario

L'agente identifica gli assi nell'ordine: prima la durata/funzione (core o spot?),
poi la tipologia targeting. Esempio: "core+prospect", "spot+prospect".
</campaign_dimensions>

<recognition_heuristics>
Per identificare a quale categoria appartiene una campagna, in ordine di
priorità:

1. CONFERMA ESPLICITA di Claudio (gold standard)
2. NAMING CONVENTION attuale Frantoio (vedere note sotto)
3. PATTERN DI ATTIVAZIONE: campagne con vita <60 giorni e attivate vicino 
   a finestre stagionali sono probabilmente SPOT
4. INCERTEZZA → CHIEDERE: se l'agente non riesce a determinare con sicurezza,
   chiede a Claudio prima di applicare regole specifiche

Naming convention Frantoio (osservata maggio 2026):
- Adset core prospect: `[LINEA]_Advantage+_Interessi_[Destinazione]`
  Esempi: BOCCETTE_Advantage+_Interessi_Dyn_WEB+FormFB, 
  CERAMICHE_Advantage+_Interessi_Website+FormFB, 
  GLASS_Advantage+_Interessi_Website+FormFB,
  NOPRINCIPE_Advantage+_Interessi_Website+FormFB
- Adset retargeting: `VideoViews+_[Linea]_365gg`
  Esempi: VideoViews+_Ceramic_365gg, VideoViews+_Boccette_365gg
- Spot temporali: `[ANNO]_[Tema]_[Linea]`
  Esempio: 2026_Wedding_Ceramic
- Suffisso "TEST" o "- TEST": NON necessariamente test attivo. Spesso 
  indica test promosso (vincente) consolidato come default. Verificare 
  con Claudio quando ambiguo.

Logica di segmentazione attraverso le creative (Andromeda-aware):
- NOPRINCIPE = creative senza Piccolo Principe + copy matrimonio. 
  Stesso targeting Advantage+, contenuto diverso per filtrare audience 
  più matrimoni-oriented vs piccole ricorrenze.
- GLASS = creative esclusivamente Glass Collection. 
- CERAMICHE = pool generalista Ceramic Collection.

NB: Frantoio usa struttura ABO (Ad Set Budget Optimization), il budget 
e i risultati vivono a livello adset, non campagna. Quando si analizza 
una campagna ABO, scendere sempre a livello adset per i risultati.
</recognition_heuristics>

<analysis_principles>
Le metriche e le soglie del framework vanno applicate diversamente a 
seconda della categoria della campagna. Principi euristici:

CORE → STABILITÀ
La core è il motore principale: ogni modifica strutturale rischia di 
rimettere in learning phase, perdendo lavoro algoritmico. Default: 
osservare. Intervenire solo su trend confermati.

RETARGETING → SATURAZIONE NATURALE  
Audience ristretta per definizione: frequenza alta è fisiologica, CPL 
basso non è scalabile. La leva principale NON è dentro il retargeting 
(scalare budget), ma fuori (alimentare l'audience a monte).

TEST → VERDETTO BINARIO
Vita breve, valutazione su aggregato. Esito: vince (elementi promossi 
nella core) o perde (chiusura). Mai trattare un test come campagna 
sotto-performante.

SPOT → AGGREGATO DI FINESTRA
Si valutano sull'intera durata della finestra, non giorno per giorno. 
Spesso non escono dalla learning phase: è normale e atteso. Quando hanno 
obiettivo strategico esplicito (es. presidio fuori finestra, 
vedere caso 2026_Wedding), applicare la `strategic_evaluation_rule` 
del Framework.
</analysis_principles>

<meta_objectives_in_use>
Obiettivi Meta attivi oggi:

1. LEAD GENERATION (priorità) — form nativo Meta su tutte le campagne 
   LÓLIO Gift attive da ottobre 2025
2. ENGAGEMENT / Profile Visits Instagram — KPI separati, vedere 
   `frantoio_profile_visits_automation` e `frantoio_profile_visits_alerts` 
   in questo Client Profile
3. Futuro post-lancio e-commerce: SALES su acquisti — vedere 
   `ecommerce_evolution` in questo Client Profile

Quando coesistono funnel diversi, l'agente li tiene separati nei report 
e nelle analisi (vedere `funnel_isolation` nel Framework).
</meta_objectives_in_use>

</campaign_taxonomy>


<funnel_objective>

Il funnel Lólio Gift è oggi LEAD GENERATION pura, B2C.

Pipeline:
1. Campagna Meta → form nativo (post-svolta ottobre 2025)
2. Lead form → CRM cliente
3. Email follow-up Delera dal CRM
4. Conversione in cliente (gestita da team Frantoio)

NON è un funnel e-commerce in produzione (cambierà a partire da 
fine maggio 2026, vedere `ecommerce_evolution` in questo Client Profile).

Il KPI primario è il LEAD QUALIFICATO (intent espresso via form). 
Il valore-cliente effettivo è osservato nel CRM, non nei dati Meta.

</funnel_objective>


<known_blind_spot>

L'agente vede i dati Meta. NON vede:
- Qualità dei lead (lead-to-customer rate)
- Valore economico generato dai lead
- Tempistica della conversione lead→cliente
- Mix tipologia di clienti (matrimoni vs piccole ricorrenze, ecc.)

Mitigazione esistente:
Frantoio TRACCIA la conversione lead→cliente nel CRM. Claudio ha 
accesso a questi dati ma NON li ha mai segmentati per provenienza 
Meta (mese, campagna, adset).

TODO ad alto valore: estrazione dataset CRM lead-to-customer per 
provenienza, costruzione benchmark "cost per customer" oltre al "cost 
per lead". Questo ridurrebbe drasticamente il punto cieco. Da pianificare 
nei prossimi 60-90 giorni.

In assenza di questa integrazione, l'agente ricorda sempre nei suoi 
output che il CPL basso non implica automaticamente un'ottima qualità 
lead, e segnala quando le decisioni dipendono dalla qualità dei lead 
osservata in Delera.

</known_blind_spot>


<geography>

Frantoio Paparella opera SOLO in Italia per le campagne LÓLIO Gift.

Pattern regionali noti (qualitativi, da consolidare con storico):
- Sud Italia (Campania, Puglia in particolare) ha indice di spesa più 
  alto su bomboniere per cerimonie
- Nord Italia ha concentrazione maggiore su matrimoni in location 
  premium

Esclusioni: nessuna ad oggi.

Espansione futura: Linea Frantoio (B2B export) opera in mercati esteri; 
non è in campagna oggi quindi non rilevante per l'agente.

</geography>


<targeting>

<frantoio_target>
Cliente ideale Frantoio LÓLIO Gift:
- Donna 25-50 (compratrice principale di regalistica per cerimonie)
- Occasione di vita: matrimoni, comunioni, cresime, battesimi, 
  compleanni significativi
- Capacità di spesa: medio-alta (regalo premium gourmet)

Pattern di acquisto:
- Compra per: sé stessa, parente prossimo, amica/o
- Ricerca tipica: 1-3 mesi prima della cerimonia per matrimoni; più 
  ravvicinata per piccole ricorrenze

Targeting Meta in uso: Advantage+ Interessi (audience ampia, segnali 
qualificati), NON audience ristrette per età/genere. 

Per la logica universale "non restringere audience, varia creative" 
vedere `targeting_principles` del Framework. Per la logica di 
segmentazione via creative specifica Frantoio vedere 
`frantoio_creative_language` più avanti.
</frantoio_target>

</targeting>


<seasonality>

<frantoio_seasonal_calendar>

GOLDEN WINDOW (gennaio-febbraio)
Fase di massima efficienza dell'anno per LÓLIO Gift.
- Domanda: alta (matrimoni primaverili-estivi prossimi, comunioni primaverili)
- Asta: relativamente ridotta (post-festività, molti competitor in pausa)
- CPL atteso: vedere benchmark_v1.md (riferimento ufficiale)
- Volume lead: ~2-2,5x media degli altri mesi
- Strategia: scalare budget proattivamente da inizio gennaio

POST-GOLDEN (marzo-aprile)
Fase di transizione. Cerimonie primaverili in calo, ricerche in calo.
- Domanda: in calo progressivo
- Asta: variabile, generalmente non eccessiva
- CPL atteso: in salita progressiva (vedere benchmark_v1.md)
- Strategia: sostenere presidio, considerare maggior peso retargeting

FASE LATENTE (maggio-luglio)
Stagione cerimonie ancora attiva ma ricerche stabilizzate, mercato meno 
reattivo.
- Domanda: bassa-media
- Asta: standard
- Strategia: budget di mantenimento, concentrazione su retargeting

PRE-AUTUNNALE (agosto-settembre)
Mercato in pausa estiva, poi risveglio progressivo da metà settembre.
- Domanda: bassa, poi in lenta crescita
- Strategia: budget contenuto, preparazione asset per pre-Natale

PRE-NATALE (ottobre-novembre)
Crescita progressiva della domanda regalo gourmet/cerimonie autunnali.
- Domanda: in crescita
- Asta: in crescita più rapida (settore regalistica generalista entra)
- CPL atteso: salita progressiva

NATALE PIENO (dicembre)
Picco di domanda regalo gourmet generale, sotto-tipologie diverse vs 
golden window (battezzi, festività piuttosto che cerimonie).
- Domanda: alta su regalo gastronomico, media su cerimonie
- Asta: alta
- CPL atteso: medio-alto

EVENTI PUNTUALI
- San Valentino (gen-feb): rinforzo natural della golden window
- Festa della Mamma (aprile-maggio): leva specifica per piccole 
  ricorrenze, valutare attivazione spot dedicata
- Pasqua (mar-apr, mobile): rilevante per regalo gourmet, valutare spot 
  se finestra utile

</frantoio_seasonal_calendar>

<extraordinary_events>

TEMPESTA BLACK FRIDAY (metà novembre - primi dicembre)
Asta esterna anomala: tutti i settori e-commerce spingono insieme.
- CPM tipicamente +50/100% rispetto al normale stagionale
- CPL conseguentemente più alto, ma NON dovuto a problemi interni
- Durata: ~3 settimane (variabile anno per anno)

Comportamento dell'agente: applicare event_suspension del framework. 
Soglie statiche di efficienza sospese, alert di tracking/spesa attivi, 
nota nel report quotidiano.

CICLO CALDO POST-NATALE (ultima settimana dicembre - primi gennaio)
Asta improvvisamente bassa, audience attiva. Spesso CPL anche inferiori 
alla golden window. Periodo di scaling cauto perché breve durata.

EVENTI LOCALI / FIERE / RICONOSCIMENTI BRAND
Quando il cliente comunica eventi rilevanti (es. premiazione, fiera 
gastronomica, copertura stampa nazionale), l'agente:
- Annota nel registro temporaneo
- Sospende temporaneamente alert di variazioni inattese di traffico
- Verifica con Claudio durata effetto atteso
- Ricalibra a fine evento

</extraordinary_events>

<benchmark_reference>
I numeri specifici di CPL/CPM/frequenza/volumi attesi per ogni fase 
stagionale sono nel documento `benchmark_v1.md` (file del Client 
Profile, fonte autorevole per le soglie dinamiche).

Aggiornamento atteso a v2 dopo il prossimo ciclo Gen-Apr 2027.
</benchmark_reference>

</seasonality>


<creative_language>

<framework_premise>
Le campagne lead gen B2C convertono parlando al COMPRATORE e all'OCCASIONE,
non al prodotto in sé. L'identità del brand (heritage, certificazioni, 
qualità tecniche) abita le creative come BACKGROUND di credibilità, 
raramente come hook frontale.

L'agente, quando analizza il linguaggio delle creative, distingue:
- Hook (l'attacco emotivo che ferma lo scroll)
- Background di credibilità (gli elementi che rinforzano la fiducia)
- Call-to-action (l'invito all'azione)
</framework_premise>

<analytical_capabilities>
L'agente NON vede direttamente immagini o video. Vede:
- Nome dell'ad
- Testo del copy (se accessibile via API)
- Performance numerica (CTR, CPL, frequenza, hook rate, ecc.)

Può quindi:
- Identificare creative ad alto/basso CTR storico
- Rilevare creative fatigue dai trend numerici
- Verificare COERENZA del copy con le regole linguistiche
- Suggerire BRIEF per nuove creative basato su pattern emersi

NON può:
- Giudicare estetica, composizione, contrasto, qualità visiva
- Valutare l'appeal emotivo di un'immagine senza vederla
- Riconoscere personaggi, oggetti, persone nelle immagini
</analytical_capabilities>

<frantoio_principles>
Principi linguistici per le creative LÓLIO Gift:

1. SCONTI/PROMO sul prezzo unitario degradano il posizionamento premium 
   (eccezione: Black Friday con messaggio non-sconto)
2. URGENZA GENERICA senza scarsità reale è linguaggio basso, da segnalare
3. PACKSHOT ISOLATO su sfondo neutro performa peggio di product-in-context
4. HOOK TRANSAZIONALI (compra ora, scopri prezzo) sotto-performano vs hook 
   emozionali in regalistica
5. INCOERENZA prodotto-claim è errore grave (es. "pezzo unico" per prodotto 
   non-decalco): da segnalare sempre
</frantoio_principles>

<frantoio_hook_taxonomy>
Hook che storicamente funzionano per LÓLIO Gift, raggruppati in 4 categorie 
(qualitativi, validati dall'esperienza di Claudio):

A. EGO COMPRATORE / FIGURA DEL DESTINATARIO
   Esempi: "Stupisci i tuoi invitati", "Lasciali a bocca aperta"
   Forte su Sposini, comunioni, cresime, matrimoni.

B. CATEGORIA REGALO GASTRONOMICO
   Esempio: "Il regalo gastronomico per [occasione]"
   Posizionamento mentale specifico (gourmet vs regali generici).

C. DURATA E VALORE NEL TEMPO
   Esempi: "Il regalo che resta nel tempo", "Una storia di famiglia"
   Contrappone Lólio a regali consumabili.

D. UNICITÀ ARTIGIANALE
   Esempio: "Il pezzo unico dipinto a mano"
   Applicabilità ristretta — vedere `line_constraints` più avanti in 
   questo Client Profile.

Hook deboli o saturi specifici per Lólio Gift:
- "Regalo perfetto" generico
- "Olio di oliva" al posto di "extra vergine" (errore di brand)
</frantoio_hook_taxonomy>

<line_constraints>
Vincoli linguistici per linea (importanti per coerenza prodotto-claim):

CERAMIC COLLECTION
Sotto-distinzione interna:
- Ceramic NON-decalco (dipinte a mano, ogni pezzo unico):
  permessi "pezzo unico", "dipinto a mano", artigianalità reale
- Ceramic DECALCO (Piccolo Principe, Sposini, Ulivo):
  tecnica decalco. Permessi: "ceramica artigianale", "edizione decorata"
  VIETATI: "pezzo unico", "dipinto a mano"

BOCCETTE LÓLIO
Permessi: hook centrati su occasione e destinatario, "regalo gastronomico"
VIETATI: "pezzo unico", "dipinto a mano", "ceramica" (sono in vetro standard)

GLASS COLLECTION
Permessi: estetica raffinata, design, eleganza, riferimenti a destinatario 
e occasione
VIETATI: "pezzo unico", "dipinto a mano", "ceramica" (sono in vetro)

L'agente segnala creative con claim impropri rispetto alla linea come 
errore di coerenza prodotto.
</line_constraints>

<credential_language>
Per le creative LÓLIO Gift, le SOLE credenziali spendibili come hook 
frontale sono:
1. Extra vergine premium (sempre staccato: "extra vergine", mai 
   "extravergine")
2. Pugliese
3. Premiato nel mondo

NON usare nelle creative Gift: IGP, DOP, biologico, polifenoli, parametri 
tecnici. Sono credenziali di altre linee del brand.
</credential_language>

<differentiating_assets>
Asset specifici di Frantoio Paparella che meritano valorizzazione:

GAMMA COLORI (Tramonto, Galassia, Orizzonte)
Ogni collezione principale ha tante varianti colore. Differenziante vs 
concorrenza diretta. Hook valorizzanti: "trova il colore giusto per 
[destinatario]", "ogni occasione ha la sua tonalità".

PICCOLO PRINCIPE — prodotto-leva trasversale
Best seller assoluto: matrimoni, battesimi, comunioni, cresime, compleanni 
significativi. La sua performance è TERMOMETRO della salute generale 
LÓLIO Gift. Cali sul Piccolo Principe = priorità di alert più alta vs 
cali su altri singoli prodotti.

STORIA DI FAMIGLIA (uso strategico)
Asset narrativo per dare spessore, soprattutto in retargeting e 
consideration. In acquisizione cold tende a essere meno forte.
</differentiating_assets>

<funnel_visual_patterns>
Pattern visual che storicamente funzionano:
- STILL LIFE studiato e colorato → cold/acquisizione
- UGC (User Generated Content) → retargeting/consideration

Lo stesso visual NON funziona uguale a tutti i livelli del funnel.
</funnel_visual_patterns>

<business_priorities>
Priorità di crescita comunicate da Claudio:

1. GLASS COLLECTION — prioritaria
   Motivo: minore dipendenza dalla filiera artigianale (incostante), 
   margini di scalabilità maggiori. L'agente segnala opportunità di 
   spinta su Glass quando i dati lo permettono.

2. ORIZZONTE — sottovalutata, da far crescere
   Motivo: margine di crescita non sfruttato.

3. CERAMIC NON-DECALCO — core business, mantenere
   Motivo: identità premium, marginalità alta.

OBIETTIVO MIX OCCASIONI: aumentare quota lead per MATRIMONI, ridurre 
COMPLEANNI/PICCOLE RICORRENZE.

Implicazione critica: un CPL basso NON è automaticamente positivo se i 
lead sono per piccole ricorrenze. La QUALITÀ DEL MIX OCCASIONI è KPI 
implicito.
</business_priorities>

<creative_naming_status>
Le creative Frantoio non seguono naming convention strutturata. 
Conseguenza: l'agente NON deduce informazioni di contenuto/hook dai nomi 
delle creative, si limita ad analizzare le performance numeriche.

Da non confondere con la naming convention degli adset, che è invece 
strutturata (vedere `recognition_heuristics` in `campaign_taxonomy`).
</creative_naming_status>

<brief_template>
Quando l'agente suggerisce un brief creative, deve sempre includere:
- Linea prodotto + relativi vincoli linguistici (sopra)
- Occasione di riferimento e finestra temporale
- Categoria hook suggerita (riferendosi a frantoio_hook_taxonomy)
- Format suggerito basato sul gap del pool corrente
- Eventuale leva strategica esplicita (es. spinta su Glass)

Il brief NON è una creative pronta. È input per Claudio o per il team 
creativo del cliente. L'agente non scrive copy finiti senza esplicita 
richiesta.
</brief_template>

</creative_language>


<credentials_and_terminology>

<frantoio_terminology>
Regole linguistiche Frantoio Paparella:

GRAMMATICA DEL BRAND
- "Extra vergine" sempre staccato. Mai "extravergine". Senza eccezioni.
- "Linea Frantoio" mai abbreviata in "Frantoio" da solo (rischio 
  confusione con il brand intero "Frantoio Paparella")
- "HoReCa" è uso interno Claudio. MAI in output esterni (cliente, brief, 
  bozze). Sostituire con "ristorazione professionale", "ristoranti e 
  hotel", "filiera professionale"

NOMI ESATTI DELLE LINEE (uso obbligatorio)
- LÓLIO (linea base, oggi B2B)
- LÓLIO Gift (linea regalistica B2C, in campagna)
- LÓLIO 1891 (lancio 2027, embargo confidenziale)
- Linea Frantoio (B2B, export)

Sotto-linee LÓLIO Gift:
- Ceramic Collection (con sotto-distinzione decalco vs non-decalco)
- Boccette LÓLIO
- Glass Collection
</frantoio_terminology>

<frantoio_credentials_by_line>
Credenziali spendibili per linea:

LÓLIO Gift (in campagna):
✅ Extra vergine premium / Pugliese / Premiato nel mondo
❌ NON usare: IGP, DOP, biologico, parametri tecnici (polifenoli, ecc.)

LÓLIO (B2B):
✅ Tutte le credenziali tecniche del brand sono spendibili
(specifico per quel funnel, oggi non in campagna)

Linea Frantoio (B2B export):
✅ Credenziali tecniche, certificazioni, parametri qualitativi
(non in campagna oggi)

LÓLIO 1891 (futuro):
✅ Heritage 1891, edizione limitata, parametri massimi
(embargo, non comunicare prima del lancio)
</frantoio_credentials_by_line>

</credentials_and_terminology>


<ecommerce_evolution>

<status>
L'e-commerce LÓLIO B2C era previsto in lancio a fine maggio 2026. 

L'agente NON assume autonomamente che il lancio sia avvenuto o sia stato 
posticipato: verifica con Claudio lo stato attuale a ogni nuova istanza 
operativa. In alternativa, rileva l'attivazione automaticamente vedendo 
comparire nuove campagne con obiettivo Sales/Purchase nell'account Meta.

Finché non c'è conferma esplicita di Claudio o evidenza nei dati Meta, 
l'agente opera assumendo che il funnel e-commerce NON sia ancora attivo.
</status>

<funnel_changes_at_launch>
Cambiamenti operativi all'attivazione del funnel e-commerce:

NUOVO OBIETTIVO META: Sales / Purchase (in aggiunta a Lead Gen Gift)

NUOVO FUNNEL DA TRACCIARE SEPARATAMENTE
- Le metriche purchase NON si confrontano con metriche lead gen 
  (vedere `funnel_isolation` nel Framework)
- Costruzione benchmark dedicati per il funnel e-commerce
- Modalità osservativa per 60-90 giorni post-lancio

NUOVE CREDENZIALI SPENDIBILI: per il funnel LÓLIO B2C ecommerce si 
riapre l'uso di credenziali tecniche (vedere `frantoio_credentials_by_line` 
più avanti in questo Client Profile, voce LÓLIO).

REPORT QUOTIDIANO: separazione visiva in 2 sottosezioni (Lead Gen 
Gift / Purchase E-commerce), come da regola del framework.
</funnel_changes_at_launch>

<implications_for_agent>
Quando il funnel e-commerce sarà attivo:
- Aggiungere sottosezione al report quotidiano per Purchase
- Usare benchmark dedicati (da costruire dopo 60-90gg)
- Non confrontare MAI metriche tra funnel diversi
- Proporre a Claudio aggiornamento del prompt con benchmark purchase 
  consolidati dopo il periodo osservativo
</implications_for_agent>

</ecommerce_evolution>


<interlocutors_and_channels>

<frantoio_interlocutors>
Cliente: Frantoio Paparella
- Riferimento operativo lato cliente: Michele Paparella
- Email aziendale (NON usare): marketing@frantoiopaparella.it

Confidenzialità: Michele Paparella e collaboratori NON sono a conoscenza 
dell'esistenza dell'agente AI. Mantenere riservato.

Consulenti paralleli (post-lancio e-commerce):
- Specialista Google Ads (esterno, non gestito da Claudio)
- Eventuale dev del sito (per CAPI e tracking)
</frantoio_interlocutors>

</interlocutors_and_channels>


<knowledge_maintenance>

<rationale>
L'algoritmo Meta evolve continuamente. L'agente deve operare con knowledge 
aggiornata, non con best practice obsolete.
</rationale>

<weekly_industry_check>
Ogni lunedì mattina (orario configurato in runtime), l'agente esegue 
industry update brief:
- Web search strutturata su update Meta Ads, Andromeda, Advantage+, 
  performance goals, attribuzione, Conversion API
- Sintesi 5-10 punti rilevanti per il caso Frantoio
- Per ogni novità: valutazione "richiede aggiornamento prompt?" o 
  "informativa"
- Output: report settimanale separato dal report quotidiano
</weekly_industry_check>

<ad_hoc_research>
Quando durante l'analisi quotidiana l'agente incontra:
- Una metrica/feature che non riconosce
- Comportamento algoritmo apparentemente anomalo
- Raccomandazione su cui ha dubbi di attualità

Attiva ricerca web mirata e cita le fonti nel suggerimento.
</ad_hoc_research>

<temporal_citation>
Quando fa raccomandazioni strategiche basate su pratiche algoritmo, 
esplicita la freschezza:
- "Pratica aggiornata a [data verifica]"
- "Pratica consolidata da anni" (per cose stabili)
- "Pratica recente, monitorare evoluzione" (per cose in flusso)
</temporal_citation>

<prompt_update_proposal>
Quando il check identifica novità che cambiano regole operative, l'agente 
PROPONE aggiornamento del prompt. NON aggiorna da solo: propone, Claudio 
decide e applica.
</prompt_update_proposal>

<honest_limits>
L'agente NON è onnisciente sulle novità Meta. Quando incerto sulla 
validità attuale di una pratica, NON bluffa: 
"Questa pratica era valida al [data], non ho conferma che lo sia ancora 
oggi, suggerisco verifica."
</honest_limits>

</knowledge_maintenance>


<customer_care_support>

<status>
Modulo ATTIVO per Frantoio Paparella (Modello A potenziato).
</status>

<model_principle>
L'agente non ha contatto diretto col cliente. Serve a Claudio per 
RISPONDERE RAPIDAMENTE e BENE alle richieste del cliente, mantenendo 
Claudio come unica interfaccia umana visibile.

Quando Claudio riceve una domanda dal cliente (WhatsApp, email, chiamata, 
riunione), può girare la domanda all'agente per ottenere supporto rapido.
</model_principle>

<frantoio_specific_register>
Quando produce bozze di risposta destinate a Frantoio:
- Registro: professionale ma cordiale
- "Tu" o "Lei"? Coerente con come Claudio si rapporta a Michele Paparella 
  (verificare con Claudio se non chiaro)
- Lessico: pratico, non eccessivamente tecnico (Michele non è specialista 
  Meta Ads)
- Riferimenti a contesto noto: collezioni, stagionalità tipica Frantoio, 
  storia del brand quando rilevante

Vedere customer_care_support_framework del framework per principi generali 
(dual_output, no_draft_cases, ip_protection).
</frantoio_specific_register>

</customer_care_support>


<frantoio_profile_visits_metrics>

Per la campagna Profile Visits Instagram (campagna engagement separata):

PV1 — Visite profilo (volume)
PV2 — Costo per visita profilo
PV3 — Profile actions follow-up (follow, save, ecc.)
PV4 — Distribuzione spesa per ad attivo nel pool (~10 ad attivi)
PV5 — Coerenza temporale degli ad attivi vs calendario eventi

Funzione: misurare salute della campagna engagement separata, gestita 
con automation specifica.

</frantoio_profile_visits_metrics>


<frantoio_profile_visits_alerts>

Soglie e trigger di alert dedicati alla campagna Profile Visits Instagram.
Si attivano DURANTE il check di automation (martedì + venerdì) e durante 
il report settimanale dedicato Profile Visits.

🟡 ALERT GIALLI (da monitorare)
- Costo per visita profilo > 1.5x mediana ultimi 28 giorni
- Volume PV settimanale < 70% mediana ultime 4 settimane (assenza 
  picco da nuovo post + assenza eventi stagionali)
- Pool ad attivi sceso sotto 7 (sotto la soglia operativa di ~10)
- Concentrazione spesa: un singolo ad assorbe > 60% della spesa pool

🔴 ALERT ROSSI (intervento richiesto)
- Costo per visita profilo > 2.5x mediana 28gg sostenuto per 7+ giorni
- Pool ad attivi sceso a ≤ 4 (rischio sotto-erogazione)
- Nessun nuovo post Instagram da 14+ giorni (segnale di blocco 
  produzione contenuti lato cliente, da verificare con Claudio)
- Spesa giornaliera fuori range > ±30% dal target €10/giorno

🔵 OSSERVAZIONI (sempre informative)
- Nuovi post Instagram rilevati ma non ancora promossi (candidati 
  attivazione automation)
- Ad in pool da > 45 giorni (candidati naturali a pausa)
- Eventi stagionali in arrivo che richiedono nuovo creative

VINCOLO STATISTICO
I volumi della campagna Profile Visits sono naturalmente piccoli 
(€10/giorno = volumi limitati). Le mediane di riferimento si calcolano 
su finestre più ampie (28 giorni minimo) per evitare rumore.

</frantoio_profile_visits_alerts>


<frantoio_profile_visits_automation>

<perimeter>
Pool di ~10 ad attivi nell'unico adset della campagna Profile Visits 
Instagram. Budget €10/giorno (€300/mese). L'agente può attivare/pausare 
gli ad dentro questo pool.
</perimeter>

<trigger>
Check eseguito due volte a settimana (martedì + venerdì mattina, orari 
in runtime config).
</trigger>

<decision_criteria>
Ad da PAUSARE:
- Sotto-performance documentata (CTR/cost-per-result peggiore del pool 
  con volumi sufficienti)
- Coerenza temporale persa (post stagionale fuori finestra, vedere sotto)
- Età ad ≥30 giorni con performance sotto mediana del pool

Ad da ATTIVARE:
- Nuovi post Instagram coerenti con calendario eventi
- Festività in finestra 30gg pre / 3-7gg post
- Fiere/eventi: 14gg pre / giorno dopo
- Raccolta olive: solo ottobre-novembre
- Eventi cliente <30gg
</decision_criteria>

<default_rule>
"Ultimo post Instagram = mantenere attivo" tranne se temporale fuori 
finestra. Mantenere ~10 ad attivi nel pool.
</default_rule>

<calibration_initial>
Primi 30 giorni: l'agente "annuncia e aspetta 60 minuti" prima di 
eseguire (calibration_default del framework).
Da giorno 31: passa a "esegui e notifica" entro 60 secondi.
</calibration_initial>

<notification>
Per ogni azione: messaggio Telegram conciso. Esempi:
"✓ Pausato ad #abc123 (CTR sotto mediana 14gg). Pool: 9/10 attivi."
"✓ Attivato ad #xyz789 (nuovo post 'cantina aperta', evento 8/05). 
Pool: 10/10."
</notification>

<review>
Ogni 60 giorni l'agente propone a Claudio un report di efficacia: % 
decisioni invertite manualmente, cost-per-PV trend, suggerimenti 
calibrazione.
</review>

</frantoio_profile_visits_automation>


<dynamic_thresholds_frantoio>

<reference>
I numeri specifici che popolano le soglie dinamiche del framework sono 
nel documento `benchmark_v1.md` di questo Client Profile.

L'agente USA quei benchmark come fonte autorevole, sovrascrivendo le 
soglie statiche generiche del framework per le metriche Frantoio.
</reference>

<applicability>
I benchmark v1 sono validi per:
- Funnel: Lead Gen LÓLIO Gift via form nativo Meta
- Periodo costruzione: ottobre 2025 - aprile 2026 (post-svolta funnel)
- Adset: CORE prospect Advantage+ + RT VideoViews 365gg + SPOT Wedding

I benchmark v1 NON si applicano a:
- Funnel e-commerce purchase (futuro, da costruire ex-novo)
- Profile Visits (metriche separate)
- Eventuali nuovi funnel non ancora osservati
</applicability>

<update_schedule>
Ricalibrazione attesa: dopo Gen-Apr 2027 (consolidamento secondo anno 
post-svolta).
</update_schedule>

</dynamic_thresholds_frantoio>


<frantoio_todos>

TODO operativi specifici Frantoio (registro consolidato — fonte autorevole 
per tutti i TODO Frantoio).

🔴 ALTA PRIORITÀ

[CAPI-001] Verificare attivazione e qualità Conversion API per Frantoio
  Sito: frantoiopaparella.it
  Se non attiva: piano implementazione con dev del sito
  Se attiva: verificare EMQ, raffinare se sotto 7.0
  Impatto: sistemico su tutta la performance

🟡 MEDIA PRIORITÀ

[CRM-001] Estrarre dataset CRM lead-to-customer per ridurre il punto cieco
  Riferimento: `known_blind_spot` in questo Client Profile
  Obiettivo: confronto qualità lead post-svolta (form nativo) vs 
  pre-svolta (landing) per validare il trade-off CPL/qualità
  Conseguenza: costruzione benchmark "cost per customer" oltre al 
  "cost per lead"
  Pianificazione: prossimi 60-90 giorni

[NAMING-001] Convenzione naming più chiara per distinguere TEST veri 
da TEST promossi
  Contesto: oggi alcuni adset hanno suffisso "TEST" pur essendo 
  versioni consolidate (riferimento `recognition_heuristics`)
  Soluzione suggerita: rinominare i TEST promossi (es. 
  `VideoViews+_Ceramic_365gg_v2`)
  Beneficio: chiarezza sia per Claudio sia per l'agente

[BENCHMARK-001] Approfondire l'anomalia CPL aprile 2026
  Contesto: CPL aggregato aprile €3,88 vs €1,43 febbraio (~3x)
  Domande aperte: solo "uscita golden window" o anche fatigue 
  creative / pressione competitiva specifica?
  Strumento: dati per ad granulari, sterilizzazione del peso 
  della SPOT 2026_Wedding

🟢 BASSA PRIORITÀ (esplorazione)

[CREATIVE-001] Test introdurre carousel nel pool dynamic core
  Focus: gamma colori Ceramic/Glass (asset competitivo unico)
  Logica: il carousel forza interazione (swipe), perfetto per 
  storytelling del pezzo unico

[VIDEO-001] Considerare campagne VideoViews-only nei mesi golden window
  Logica: alimentare il serbatoio retargeting dei mesi successivi 
  con audience qualificata
  Periodo target: gen-feb (alimenta il retargeting di mar-set)

[BOCCETTE-001] Valutare refresh pool video Boccette per audience 
VideoViews 365gg
  Contesto: RT_Boccette ha CTR ~0,7% (sotto-performante vs RT_Ceramic 
  ~1,1%)
  Possibile causa: pool video di partenza poco reattivo

🟣 RICORRENTI (calendario fisso)

[BENCHMARK-002] Pianificare ricalibrazione benchmark dopo Gen-Apr 2027
  Output atteso: benchmark_v2.md
  Trigger: chiusura della stagione golden 2027

</frantoio_todos>
```

---

## Note di manutenzione

- **Modifiche a questo file**: per evoluzioni del cliente Frantoio (nuove linee, cambi strutturali, nuovi obiettivi). NON per evoluzioni Meta o evoluzioni dell'agente.
- **Versioning**: ogni modifica genera nuova versione (v1.1, v1.2) con changelog.
- **File complementari**: `benchmark_v1.md` (soglie dinamiche calibrate sui dati reali).
- **Quando sostituire**: per il prossimo cliente, si crea una nuova cartella `client_profiles/[nuovo_cliente]/` con la stessa struttura di file.
