# TODO Odoo 19 – Rifugio/Ristorante in Italia

**Versione verificata:** 10 settembre 2026  
**Scenario:** Odoo 19 self-hosted, ristorante + struttura ricettiva/rifugio in Italia.

> **Importante:** questa checklist è una baseline tecnico-operativa e normativa nazionale.  
> Gli adempimenti relativi a classificazione del rifugio, SCIA/SUAP, statistica territoriale, imposta di soggiorno e alcune prescrizioni antincendio/sanitarie dipendono da Regione/Provincia autonoma e Comune e devono essere verificati localmente.

## Legenda

- **[LEGGE]** requisito derivante da normativa o indicazione ufficiale.
- **[ODOO]** requisito/configurazione specifica di Odoo.
- **[BP]** best practice tecnica/operativa, non obbligo normativo nella forma specifica proposta.
- **[LOCALE]** dipende da Regione, Provincia autonoma, Comune, ASL/APSS o SUAP.
- **[VALIDARE]** configurazione da far confermare al professionista competente.

---

# 0. Gate di go-live

Non considerare il sistema pronto finché tutti questi punti non sono completati.

- [ ] **[LEGGE]** attività ricettiva correttamente autorizzata/classificata.
- [ ] **[LEGGE]** attività alimentare registrata presso l'autorità competente.
- [ ] **[LEGGE]** CIN ottenuto e utilizzato correttamente.
- [ ] **[LEGGE]** Alloggiati Web operativo.
- [ ] **[LEGGE]** procedura statistica turistica operativa.
- [ ] **[LEGGE]** imposta di soggiorno configurata, se applicabile.
- [ ] **[LEGGE]** HACCP operativo.
- [ ] **[LEGGE]** informazioni allergeni operative.
- [ ] **[LEGGE]** RT/corrispettivi telematici configurati.
- [ ] **[LEGGE]** fatturazione elettronica/SdI configurata.
- [ ] **[LEGGE]** conservazione digitale fiscale configurata.
- [ ] **[LEGGE/BP]** trattamento dati personali/GDPR definito.
- [ ] **[BP]** backup automatici funzionanti.
- [ ] **[BP/LEGGE]** restore realmente testato.
- [ ] **[BP]** monitoraggio e procedure di emergenza operative.
- [ ] **[VALIDARE]** commercialista, SUAP/ente territoriale, HACCP e sicurezza hanno controllato le rispettive parti.

Il GDPR non prescrive una specifica tecnologia di backup, ma richiede misure adeguate al rischio, disponibilità/resilienza, capacità di ripristinare tempestivamente i dati e verifica periodica dell'efficacia delle misure.

---

# 1. Inquadramento legale dell'attività

- [ ] **[LOCALE]** identificare esattamente la categoria amministrativa della struttura:
  - rifugio alpino;
  - rifugio escursionistico;
  - albergo;
  - ostello;
  - altra struttura ricettiva prevista dalla normativa territoriale.
- [ ] **[LOCALE]** verificare la legge regionale/provinciale applicabile.
- [ ] **[LOCALE]** verificare SCIA/autorizzazioni/comunicazioni tramite SUAP.
- [ ] **[LOCALE]** verificare eventuale codice identificativo regionale/provinciale.
- [ ] **[LEGGE]** registrare la struttura nella BDSR quando rientra nelle strutture soggette.
- [ ] **[LEGGE]** ottenere il CIN.
- [ ] **[LEGGE]** esporre il CIN secondo le regole applicabili.
- [ ] **[LEGGE]** riportare il CIN negli annunci/pubblicità della struttura.
- [ ] **[LEGGE]** mantenere anche il codice regionale/provinciale se previsto: il CIN non lo sostituisce.

Le FAQ BDSR aggiornate all'11 maggio 2026 confermano l'obbligo di CIN per le strutture turistico-ricettive definite dalle normative regionali/provinciali e chiariscono che il CIN non sostituisce eventuali codici locali.

---

# 2. Registrazione dell'attività alimentare

- [ ] **[LEGGE]** identificare il soggetto OSA — Operatore del Settore Alimentare.
- [ ] **[LEGGE/LOCALE]** notificare all'autorità competente lo stabilimento nel modo richiesto localmente.
- [ ] **[LEGGE]** notificare eventuali modifiche significative dell'attività.
- [ ] **[LEGGE]** notificare eventuale cessazione.
- [ ] **[LOCALE]** verificare ulteriori requisiti ASL/APSS e SUAP.

L'art. 6 del Regolamento CE 852/2004 impone all'operatore alimentare di notificare all'autorità competente gli stabilimenti sotto il proprio controllo ai fini della registrazione e di mantenere aggiornate tali informazioni.

---

# 3. Installazione Odoo di produzione

- [ ] **[BP]** utilizzare un utente Linux dedicato `odoo`.
- [ ] **[BP]** non eseguire Odoo come `root`.
- [ ] **[BP]** installare Odoo in un percorso controllato, ad esempio `/opt/odoo`.
- [ ] **[BP]** separare:
  - sorgenti Odoo;
  - custom addon;
  - configurazione;
  - filestore;
  - log.
- [ ] **[BP]** gestire i custom addon con Git.
- [ ] **[BP]** eseguire Odoo tramite `systemd` o equivalente.
- [ ] **[BP]** avvio automatico dopo reboot.
- [ ] **[BP]** restart controllato in caso di crash.
- [ ] **[BP]** ambiente staging distinto dalla produzione.
- [ ] **[BP]** provare aggiornamenti Odoo/addon sullo staging prima della produzione.
- [ ] **[ODOO]** mantenere Odoo 19 aggiornato con le build/correzioni supportate.

La guida di deployment Odoo raccomanda di mantenere aggiornata l'installazione e di configurare correttamente il server per un utilizzo di produzione.

---

# 4. PostgreSQL

- [ ] **[ODOO]** utilizzare PostgreSQL 13 o superiore per Odoo 19.
- [ ] **[ODOO]** creare un utente PostgreSQL dedicato a Odoo.
- [ ] **[ODOO]** non usare l'utente `postgres` come utente dell'applicazione.
- [ ] **[ODOO/SECURITY]** l'utente PostgreSQL usato da Odoo non deve essere superuser.
- [ ] **[BP]** usare una password DB lunga e casuale.
- [ ] **[BP]** PostgreSQL raggiungibile soltanto da localhost o dalla rete applicativa privata.
- [ ] **[BP]** non esporre `5432` su Internet.
- [ ] **[BP]** monitorare spazio disco e crescita DB.

Odoo 19 richiede PostgreSQL 13+; la documentazione di deployment raccomanda inoltre espressamente che il `db_user` Odoo non sia PostgreSQL superuser.

---

# 5. Reverse proxy e HTTPS

- [ ] **[ODOO/SECURITY]** mettere Odoo dietro Nginx/Caddy/altro reverse proxy.
- [ ] **[ODOO/SECURITY]** utilizzare HTTPS con certificato valido.
- [ ] **[BP]** redirigere HTTP verso HTTPS.
- [ ] **[ODOO]** impostare `proxy_mode = True` quando Odoo è dietro reverse proxy.
- [ ] **[BP]** non esporre direttamente la porta Odoo `8069` a Internet.
- [ ] **[ODOO]** gestire correttamente il canale websocket/gevent quando si usa la modalità multiprocess.
- [ ] **[BP]** automatizzare il rinnovo del certificato TLS.
- [ ] **[BP]** applicare firewall con politica restrittiva.

Odoo dichiara che un deployment sicuro deve utilizzare HTTPS e documenta `proxy_mode` e reverse proxy.

---

# 6. Sicurezza del Database Manager Odoo

- [ ] **[ODOO/SECURITY]** configurare `db_name`/`dbfilter`.
- [ ] **[ODOO/SECURITY]** in produzione con un database noto impostare `list_db = False`.
- [ ] **[ODOO/SECURITY]** bloccare quindi l'accesso pubblico al Database Manager.
- [ ] **[ODOO/SECURITY]** utilizzare un `admin_passwd` forte anche se il manager viene disabilitato.
- [ ] **[SECURITY]** non usare credenziali predefinite.
- [ ] **[SECURITY]** non installare dati demo su un'istanza Internet-facing.

Odoo raccomanda esplicitamente di disabilitare il Database Manager sui sistemi esposti a Internet e di usare `list_db = False` quando il database è identificato univocamente.

---

# 7. Utenti e permessi Odoo

- [ ] **[BP/GDPR]** account personale per ogni dipendente.
- [ ] **[BP/GDPR]** evitare account condivisi del tipo `reception` o `cassa`.
- [ ] **[ODOO]** configurare ruoli e gruppi di accesso.
- [ ] **[BP/GDPR]** applicare il principio del minimo privilegio.
- [ ] **[BP]** separare almeno:
  - amministratore;
  - reception;
  - amministrazione;
  - responsabile;
  - cameriere;
  - cucina;
  - magazzino.
- [ ] **[BP]** un cameriere non deve poter vedere dati identificativi degli ospiti se non necessari.
- [ ] **[BP]** disabilitare immediatamente gli utenti che lasciano l'attività.
- [ ] **[BP]** abilitare 2FA almeno per amministratori e utenti critici.
- [ ] **[BP]** valutare 2FA obbligatoria per tutti gli utenti back-office.

Odoo permette di gestire diritti per utenti/gruppi e supporta l'autenticazione a due fattori.

---

# 8. Localizzazione fiscale italiana

Verificare che nella specifica edizione/build utilizzata siano disponibili e installati i moduli necessari.

- [ ] **[ODOO]** `l10n_it` — contabilità italiana.
- [ ] **[ODOO]** `l10n_it_edi` — fatturazione elettronica.
- [ ] **[ODOO]** `l10n_it_edi_sale` — fatturazione elettronica vendite.
- [ ] **[ODOO]** `l10n_it_pos` — integrazione POS/stampante fiscale italiana.
- [ ] **[ODOO]** `l10n_it_reports` — report italiani.
- [ ] **[ODOO]** `l10n_it_stock_ddt` se vengono utilizzati DDT.
- [ ] **[VALIDARE]** piano dei conti controllato dal commercialista.
- [ ] **[VALIDARE]** aliquote IVA controllate dal commercialista.
- [ ] **[VALIDARE]** trattamento fiscale di:
  - pernottamento;
  - ristorante;
  - bar;
  - extra;
  - eventuale mezza pensione/pensione;
  - caparre;
  - cancellazioni/no-show.
- [ ] **[ODOO]** inserire correttamente:
  - ragione sociale;
  - indirizzo;
  - Partita IVA;
  - Codice Fiscale;
  - regime fiscale.

La documentazione ufficiale Odoo 19 elenca questi moduli e identifica nome, indirizzo, P.IVA, C.F. e regime fiscale come dati necessari alla localizzazione italiana.

---

# 9. Fatturazione elettronica / SdI

- [ ] **[LEGGE]** configurare emissione FatturaPA XML.
- [ ] **[LEGGE]** trasmettere tramite Sistema di Interscambio.
- [ ] **[ODOO]** configurare l'invio all'Agenzia delle Entrate/SdI.
- [ ] **[ODOO]** monitorare lo stato SdI.
- [ ] **[ODOO]** gestire scarti.
- [ ] **[ODOO]** gestire note di credito.
- [ ] **[ODOO]** gestire fatture fornitori.
- [ ] **[VALIDARE]** verificare con commercialista tipi documento realmente necessari.
- [ ] **[TEST]** emettere una fattura test completa prima del go-live.
- [ ] **[TEST]** simulare uno scarto e verificarne la correzione.

Una fattura elettronica inviata con modalità diverse da XML/SdI nei casi soggetti all'obbligo è considerata non emessa; l'Agenzia delle Entrate descrive SdI come canale obbligatorio.

Odoo 19 supporta creazione XML, invio a SdI, stato di elaborazione, accettazione e gestione del rifiuto.

---

# 10. Conservazione digitale delle fatture

- [ ] **[LEGGE]** attivare una soluzione di conservazione digitale conforme.
- [ ] **[LEGGE]** scegliere tra servizio Agenzia delle Entrate o conservatore adeguato.
- [ ] **[LEGGE]** assicurarsi che fatture e note di variazione entrino effettivamente nel processo.
- [ ] **[LEGGE]** definire i periodi di conservazione applicabili con il commercialista.
- [ ] **[LEGGE]** mantenere le scritture/documenti contabili per i termini civilistici e fiscali applicabili.
- [ ] **[TEST]** verificare periodicamente la reperibilità di documenti conservati.

Odoo precisa esplicitamente che **non fornisce i requisiti di Conservazione sostitutiva**; l'Agenzia delle Entrate offre un servizio dedicato attivabile dal portale Fatture e Corrispettivi.

AgID specifica che un sistema di conservazione deve garantire autenticità, integrità, affidabilità, leggibilità e reperibilità.

L'art. 2220 c.c. prevede, in generale, dieci anni per scritture contabili e fatture; il commercialista deve comunque definire la retention effettiva applicabile all'impresa.

---

# 11. POS ristorante e Registratore Telematico

- [ ] **[ODOO]** configurare il POS come Bar/Ristorante.
- [ ] **[ODOO]** creare planimetrie/sale.
- [ ] **[ODOO]** configurare tavoli e posti.
- [ ] **[ODOO]** configurare ordini e divisione conto.
- [ ] **[ODOO]** configurare cucina/bar.
- [ ] **[ODOO]** configurare Kitchen/Preparation Display o stampanti di preparazione.
- [ ] **[LEGGE]** utilizzare il corretto strumento di certificazione dei corrispettivi.
- [ ] **[ODOO/LEGGE]** utilizzare dispositivo RT certificato compatibile con il flusso previsto.
- [ ] **[LEGGE]** far registrare/fiscalizzare il dispositivo come previsto.
- [ ] **[ODOO]** configurare la stampante fiscale in Odoo.
- [ ] **[ODOO]** POS e stampante fiscale devono poter comunicare sulla LAN.
- [ ] **[TEST]** documento commerciale normale.
- [ ] **[TEST]** pagamento contanti.
- [ ] **[TEST]** pagamento elettronico.
- [ ] **[TEST]** annullo/resi.
- [ ] **[TEST]** chiusura giornaliera.
- [ ] **[TEST]** dispositivo RT temporaneamente non raggiungibile.

Odoo POS supporta sale, tavoli, ordini, preparazione cucina/bar e divisione del conto.

Per l'Italia Odoo distingue le normali stampanti ePOS dai dispositivi fiscali RT e afferma che gli RT certificati sono utilizzati per la conformità dei documenti e la trasmissione fiscale giornaliera. La documentazione specifica inoltre che le stampanti fiscali sono progettate per operare sulla rete locale.

---

# 12. Collegamento POS ↔ RT introdotto per il 2026

- [ ] **[LEGGE]** censire ogni strumento di pagamento elettronico.
- [ ] **[LEGGE]** censire ogni strumento di certificazione dei corrispettivi.
- [ ] **[LEGGE]** effettuare l'associazione tramite le funzionalità dell'Agenzia delle Entrate.
- [ ] **[LEGGE]** aggiornare l'associazione quando viene attivato/sostituito/modificato un POS.
- [ ] **[PROCEDURA]** nominare chi è responsabile dell'aggiornamento.
- [ ] **[ODOO]** assicurarsi che il metodo di pagamento registrato corrisponda al pagamento realmente effettuato.

Per i POS già in uso a gennaio 2026 la scadenza iniziale è stata il **20 aprile 2026**; per POS attivati successivamente o variazioni, l'Agenzia indica il periodo tra il sesto e l'ultimo giorno del secondo mese successivo.

---

# 13. Magazzino alimentare e rintracciabilità

- [ ] **[LEGGE]** poter identificare da chi sono stati acquistati gli alimenti/ingredienti.
- [ ] **[LEGGE]** mantenere procedure per mettere queste informazioni a disposizione dell'autorità.
- [ ] **[LEGGE]** per vendite B2B, poter identificare le imprese destinatarie quando richiesto.
- [ ] **[ODOO]** creare correttamente i fornitori.
- [ ] **[ODOO]** utilizzare ordini di acquisto/ricezioni.
- [ ] **[ODOO/BP]** usare lotti quando utili/necessari.
- [ ] **[ODOO/BP]** tracciare date di scadenza.
- [ ] **[ODOO/BP]** utilizzare FEFO per prodotti deperibili quando appropriato.
- [ ] **[BP]** distinguere ubicazioni:
  - dispensa;
  - frigorifero;
  - freezer;
  - bar;
  - cantina.
- [ ] **[BP]** registrare scarti/spoilage.
- [ ] **[BP]** inventari periodici.
- [ ] **[PROCEDURA]** definire procedura di richiamo/ritiro.

L'art. 18 del Regolamento CE 178/2002 richiede la rintracciabilità degli alimenti e la capacità di identificare fornitori e, nelle relazioni tra imprese, destinatari.

Odoo supporta lotti, scadenze, alert e strategia FEFO.

---

# 14. HACCP

- [ ] **[LEGGE]** predisporre un piano di autocontrollo/HACCP appropriato all'attività.
- [ ] **[LEGGE]** identificare pericoli.
- [ ] **[LEGGE]** identificare eventuali CCP.
- [ ] **[LEGGE]** definire limiti/criteri.
- [ ] **[LEGGE]** definire monitoraggio.
- [ ] **[LEGGE]** definire azioni correttive.
- [ ] **[LEGGE]** definire procedure di verifica.
- [ ] **[LEGGE]** mantenere documentazione/registrazioni adeguate.
- [ ] **[OPERATIVO]** gestione temperature.
- [ ] **[OPERATIVO]** controlli merci in ingresso.
- [ ] **[OPERATIVO]** pulizia e sanificazione.
- [ ] **[OPERATIVO]** pest control.
- [ ] **[OPERATIVO]** non conformità.
- [ ] **[OPERATIVO]** manutenzione attrezzature.
- [ ] **[OPERATIVO]** formazione addetti.
- [ ] **[ODOO/BP]** usare Odoo per attività, scadenze, controlli e storico dove utile.
- [ ] **[VALIDARE]** piano validato sulla cucina e sui processi effettivi.

Il Ministero della Salute conferma che l'autocontrollo è obbligatorio per gli operatori della filiera e che HACCP è obbligatorio per gli operatori post-primari; elenca inoltre i sette principi del piano HACCP.

---

# 15. Allergeni

- [ ] **[LEGGE]** identificare gli allergeni presenti nei piatti.
- [ ] **[OPERATIVO]** associare allergeni agli ingredienti.
- [ ] **[OPERATIVO]** associare ingredienti alle ricette/piatti.
- [ ] **[OPERATIVO]** rendere l'informazione facilmente accessibile al personale.
- [ ] **[OPERATIVO]** mantenere coerente menu cartaceo/digitale e ricette.
- [ ] **[PROCEDURA]** ogni modifica ricetta deve includere una verifica allergeni.
- [ ] **[PROCEDURA]** documentare il rischio di contaminazione crociata.
- [ ] **[BP]** evitare di affidarsi soltanto alla memoria del personale.

Il Regolamento UE 1169/2011 rende obbligatoria l'indicazione degli ingredienti/sostanze dell'Allegato II che causano allergie o intolleranze e disciplina anche gli alimenti non preimballati.

---

# 16. PMS / gestione camere e posti letto

Creare o installare un modulo PMS che rappresenti almeno:

- [ ] **[OPERATIVO]** camere.
- [ ] **[OPERATIVO]** posti letto.
- [ ] **[OPERATIVO]** tipologia camera/posto.
- [ ] **[OPERATIVO]** disponibilità.
- [ ] **[OPERATIVO]** prenotazioni.
- [ ] **[OPERATIVO]** data arrivo/partenza.
- [ ] **[OPERATIVO]** ospiti.
- [ ] **[OPERATIVO]** check-in/check-out.
- [ ] **[OPERATIVO]** prezzi/stagionalità.
- [ ] **[OPERATIVO]** caparre.
- [ ] **[OPERATIVO]** cancellazioni/no-show.
- [ ] **[OPERATIVO]** extra.
- [ ] **[OPERATIVO]** mezza pensione/pensione.
- [ ] **[OPERATIVO]** stato pulizia camera.
- [ ] **[OPERATIVO]** manutenzione.
- [ ] **[OPERATIVO]** folio/conto ospite.
- [ ] **[OPERATIVO]** addebito consumazioni ristorante alla camera.
- [ ] **[INTEGRAZIONE]** collegamento Alloggiati Web.
- [ ] **[INTEGRAZIONE]** statistiche turistiche.
- [ ] **[INTEGRAZIONE]** imposta di soggiorno.

Il Garante riconosce esplicitamente che i PMS delle strutture ricettive possono essere collegati ai sistemi del Ministero dell'Interno per automatizzare la comunicazione Alloggiati.

---

# 17. Alloggiati Web

- [ ] **[LEGGE]** ottenere le credenziali dalla Questura competente.
- [ ] **[LEGGE]** verificare l'identità dell'ospite sulla base di documento idoneo.
- [ ] **[LEGGE]** seguire le attuali indicazioni sulla verifica de visu dell'ospite.
- [ ] **[LEGGE]** inviare i dati tramite Alloggiati Web.
- [ ] **[LEGGE]** soggiorni ordinari: comunicazione entro 24 ore dall'arrivo.
- [ ] **[LEGGE]** soggiorni inferiori alle 24 ore: entro 6 ore.
- [ ] **[OPERATIVO]** stato in PMS:
  - da inviare;
  - inviato;
  - errore.
- [ ] **[OPERATIVO]** dashboard delle comunicazioni mancanti.
- [ ] **[LEGGE]** scaricare le ricevute prima che non siano più disponibili sul portale.
- [ ] **[LEGGE]** conservare le ricevute in formato digitale per 5 anni.
- [ ] **[PROCEDURA]** definire la procedura da seguire in caso di indisponibilità del servizio.

Le Questure confermano 24 ore/6 ore e la verifica dell'identità; le FAQ ufficiali Alloggiati Web prevedono la conservazione digitale delle ricevute per cinque anni.

---

# 18. Documenti d'identità degli ospiti

- [ ] **[LEGGE/GDPR]** non creare un archivio permanente di scansioni di documenti.
- [ ] **[LEGGE/GDPR]** se una copia viene acquisita per facilitare la comunicazione, cancellarla/distruggerla dopo il completamento dell'adempimento.
- [ ] **[LEGGE/GDPR]** dopo la ricevuta, cancellare i dati digitali trattati esclusivamente per tale trasmissione quando non esiste altra base giuridica per conservarli.
- [ ] **[LEGGE/GDPR]** distruggere eventuali copie cartacee non più necessarie.
- [ ] **[LEGGE]** conservare invece la ricevuta Alloggiati per 5 anni.
- [ ] **[GDPR]** impedire a utenti non autorizzati l'accesso ai dati identificativi.
- [ ] **[ODOO/CUSTOM]** se viene utilizzato OCR/scanner, implementare cancellazione automatica dell'immagine sorgente.
- [ ] **[PROCEDURA]** evitare uso incontrollato di WhatsApp/email per ricevere copie dei documenti.

Il chiarimento del Garante del **29 aprile 2026** stabilisce che la normativa di pubblica sicurezza non richiede l'archiviazione delle copie dei documenti e che eventuali copie devono essere eliminate/distrutte dopo la comunicazione; la ricevuta deve invece essere conservata cinque anni.

---

# 19. Statistiche del movimento turistico

- [ ] **[LEGGE]** identificare il sistema regionale/provinciale attraverso cui effettuare la rilevazione.
- [ ] **[LEGGE]** registrare correttamente arrivi.
- [ ] **[LEGGE]** registrare presenze/pernottamenti.
- [ ] **[LEGGE]** registrare provenienza/residenza secondo il tracciato richiesto.
- [ ] **[LEGGE]** produrre i dati con periodicità richiesta.
- [ ] **[ODOO/CUSTOM]** creare export compatibile con il sistema territoriale, quando possibile.
- [ ] **[OPERATIVO]** riconciliare PMS e comunicazioni statistiche.
- [ ] **[OPERATIVO]** conservare esiti/ricevute delle trasmissioni.

La rilevazione ISTAT 2026 “Movimento dei clienti negli esercizi ricettivi” raccoglie **mensilmente** arrivi e presenze di residenti e non residenti; l'organizzazione concreta della raccolta passa attraverso il sistema territoriale competente.

---

# 20. Imposta di soggiorno

- [ ] **[LOCALE]** verificare se il Comune l'ha istituita.
- [ ] **[LOCALE]** utilizzare il regolamento comunale vigente nel 2026.
- [ ] **[LOCALE]** configurare importo/tariffa.
- [ ] **[LOCALE]** configurare eventuali categorie differenti.
- [ ] **[LOCALE]** configurare numero massimo di notti tassabili.
- [ ] **[LOCALE]** configurare esenzioni.
- [ ] **[LOCALE]** configurare eventuale stagionalità.
- [ ] **[ODOO/CUSTOM]** calcolare automaticamente l'imposta.
- [ ] **[OPERATIVO]** registrare motivazione delle esenzioni quando richiesta.
- [ ] **[LEGGE/LOCALE]** generare dichiarazioni/report richiesti.
- [ ] **[OPERATIVO]** riconciliare:
  `pernottamenti → imposta dovuta → imposta riscossa → importo versato`.
- [ ] **[VALIDARE]** far verificare la configurazione al commercialista.

L'imposta di soggiorno è prevista dall'art. 4 D.Lgs. 23/2011 ed è istituita/regolata dai Comuni competenti; il Dipartimento delle Finanze pubblica anche per il 2026 regolamenti e delibere locali.

---

# 21. GDPR e privacy

- [ ] **[LEGGE]** determinare le finalità e basi giuridiche dei trattamenti.
- [ ] **[LEGGE]** fornire le informative privacy appropriate.
- [ ] **[LEGGE]** applicare minimizzazione dei dati.
- [ ] **[LEGGE]** definire tempi di conservazione.
- [ ] **[LEGGE]** limitare l'accesso ai dati.
- [ ] **[LEGGE]** adottare misure tecniche e organizzative adeguate al rischio.
- [ ] **[LEGGE]** garantire riservatezza, integrità, disponibilità e resilienza.
- [ ] **[LEGGE]** garantire capacità di ripristino dopo incidenti.
- [ ] **[LEGGE]** verificare periodicamente l'efficacia delle misure.
- [ ] **[PROCEDURA]** predisporre gestione data breach.
- [ ] **[LEGGE]** in caso di violazione valutare se sussiste l'obbligo di notifica al Garante.
- [ ] **[LEGGE]** quando dovuta, notifica senza ingiustificato ritardo e ove possibile entro 72 ore.
- [ ] **[PROCEDURA]** identificare chi decide e gestisce una violazione.
- [ ] **[VALIDARE]** definire responsabili del trattamento/contratti con hosting e fornitori quando applicabile.

Gli artt. 32 e 33 GDPR disciplinano sicurezza, restore e notifiche dei data breach.

---

# 22. Backup Odoo

Un backup utilizzabile deve comprendere almeno **database e filestore**.

- [ ] **[BP/GDPR]** backup PostgreSQL.
- [ ] **[BP/GDPR]** backup del filestore associato.
- [ ] **[BP]** backup di `odoo.conf`.
- [ ] **[BP]** backup configurazione reverse proxy.
- [ ] **[BP]** backup configurazioni `systemd`.
- [ ] **[BP]** custom addon conservati in repository remoto.
- [ ] **[BP]** cifrare i backup contenenti dati personali.
- [ ] **[BP]** avere almeno una copia indipendente dal server Odoo.
- [ ] **[BP]** avere almeno una copia off-site.
- [ ] **[BP]** definire formalmente:
  - RPO massimo accettabile;
  - RTO massimo accettabile;
  - retention.
- [ ] **[BP]** schedulare backup coerenti con l'RPO.
- [ ] **[BP]** monitorare il successo/fallimento dei job.
- [ ] **[BP]** valutare PostgreSQL WAL/PITR se la perdita di diverse ore di prenotazioni/vendite è inaccettabile.

Odoo specifica che i backup completi comprendono il dump database e il **filestore**, che contiene allegati e campi binari.

Il GDPR richiede inoltre capacità di ripristinare tempestivamente disponibilità e accesso ai dati in caso di incidente.

---

# 23. Disaster recovery

- [ ] **[BP/GDPR]** documentare la procedura di restore.
- [ ] **[BP/GDPR]** creare periodicamente una VM/server vuoto.
- [ ] **[TEST]** ripristinare PostgreSQL.
- [ ] **[TEST]** ripristinare filestore.
- [ ] **[TEST]** ripristinare custom addon.
- [ ] **[TEST]** ripristinare configurazione.
- [ ] **[TEST]** avviare Odoo.
- [ ] **[TEST]** aprire vecchi allegati.
- [ ] **[TEST]** controllare ordini POS.
- [ ] **[TEST]** controllare prenotazioni.
- [ ] **[TEST]** controllare fatture.
- [ ] **[TEST]** controllare magazzino.
- [ ] **[TEST]** registrare il tempo necessario al ripristino.
- [ ] **[BP]** ripetere il test dopo modifiche infrastrutturali significative.

Il requisito di testare regolarmente l'efficacia delle misure e di poter ripristinare i dati deriva direttamente dall'art. 32 GDPR.

---

# 24. Continuità operativa del rifugio

Questi sono requisiti tecnici raccomandati, non obblighi nazionali specifici nella forma indicata.

- [ ] **[BP]** UPS per router/switch.
- [ ] **[BP]** UPS per server locale, se presente.
- [ ] **[BP]** protezione adeguata dell'RT e dei terminali.
- [ ] **[BP]** connessione WAN principale.
- [ ] **[BP]** seconda connessione 4G/5G/satellite quando ragionevole.
- [ ] **[BP]** failover WAN.
- [ ] **[BP]** LAN funzionante anche durante perdita Internet.
- [ ] **[BP]** test periodico del failover.
- [ ] **[PROCEDURA]** comportamento in caso di:
  - Odoo offline;
  - Internet offline;
  - RT offline;
  - POS bancario offline;
  - Alloggiati Web offline;
  - blackout.

Odoo POS è progettato per mantenere funzionalità durante interruzioni temporanee di rete, ma sistemi esterni come SdI, Alloggiati e trasmissioni fiscali richiedono comunque connettività per completare i relativi adempimenti.

---

# 25. Monitoring

- [ ] **[BP]** monitorare disponibilità HTTPS Odoo.
- [ ] **[BP]** processo Odoo.
- [ ] **[BP]** PostgreSQL.
- [ ] **[BP]** spazio disco.
- [ ] **[BP]** RAM.
- [ ] **[BP]** CPU.
- [ ] **[BP]** certificato TLS.
- [ ] **[BP]** successo backup.
- [ ] **[BP]** errori applicativi.
- [ ] **[BP]** fatture SdI in errore.
- [ ] **[BP]** Alloggiati ancora da inviare.
- [ ] **[BP]** creare alert che raggiungano una persona responsabile.

Questo non è un obbligo normativo autonomo, ma supporta disponibilità, resilienza e controllo periodico richiesti dall'art. 32 GDPR.

---

# 26. Manutenzione delle attrezzature

- [ ] **[OPERATIVO]** censire frigoriferi.
- [ ] **[OPERATIVO]** freezer/celle.
- [ ] **[OPERATIVO]** forni.
- [ ] **[OPERATIVO]** lavastoviglie.
- [ ] **[OPERATIVO]** impianti.
- [ ] **[OPERATIVO]** attrezzature cucina.
- [ ] **[OPERATIVO]** registrare manutenzioni preventive.
- [ ] **[OPERATIVO]** registrare manutenzioni correttive.
- [ ] **[OPERATIVO]** registrare fornitori/manutentori.
- [ ] **[OPERATIVO]** conservare evidenze/certificazioni applicabili.
- [ ] **[ODOO]** valutare app Manutenzione per tracciare attrezzature e interventi.

Odoo Manutenzione permette di censire attrezzature e gestire manutenzione preventiva/correttiva.

---

# 27. Sicurezza sul lavoro

- [ ] **[LEGGE]** effettuare la valutazione dei rischi.
- [ ] **[LEGGE]** predisporre il DVR quando previsto.
- [ ] **[LEGGE]** includere rischi effettivi dell'attività.
- [ ] **[LEGGE/VALIDARE]** organizzare RSPP secondo il caso.
- [ ] **[LEGGE/VALIDARE]** gestione primo soccorso.
- [ ] **[LEGGE/VALIDARE]** gestione emergenze/antincendio.
- [ ] **[LEGGE/VALIDARE]** formazione lavoratori.
- [ ] **[LEGGE/VALIDARE]** DPI quando necessari.
- [ ] **[ODOO/BP]** registrare scadenze formative e manutenzioni senza considerare Odoo sostitutivo della documentazione legale.

INAIL ricorda che la valutazione di tutti i rischi e la conseguente elaborazione del documento costituiscono un obbligo non delegabile del datore di lavoro ai sensi dell'art. 17 D.Lgs. 81/2008.

---

# 28. Prevenzione incendi

- [ ] **[LEGGE/VALIDARE]** determinare il numero massimo autorizzato di posti letto.
- [ ] **[LEGGE/VALIDARE]** far verificare la struttura da professionista antincendio.
- [ ] **[LEGGE]** se il rifugio supera **25 posti letto**, verificare l'assoggettamento all'Attività 66 DPR 151/2011.
- [ ] **[VALIDARE]** verificare comunque gli obblighi applicabili anche se ≤25 posti.
- [ ] **[OPERATIVO]** registrare manutenzione presidi antincendio.
- [ ] **[OPERATIVO]** registrare scadenze controlli.
- [ ] **[OPERATIVO]** registrare formazione addetti.

I Vigili del Fuoco classificano nell'Attività 66, tra le altre strutture, i **rifugi alpini con oltre 25 posti letto**.

---

# 29. Riconciliazione giornaliera del ristorante

- [ ] **[OPERATIVO]** chiudere sessione POS.
- [ ] **[OPERATIVO]** controllare vendite Odoo.
- [ ] **[OPERATIVO]** controllare documenti RT.
- [ ] **[OPERATIVO]** confrontare pagamenti elettronici.
- [ ] **[OPERATIVO]** confrontare contanti.
- [ ] **[OPERATIVO]** registrare differenze.
- [ ] **[OPERATIVO]** autorizzare formalmente correzioni significative.
- [ ] **[OPERATIVO]** controllare fatture eventualmente emesse.
- [ ] **[OPERATIVO]** controllare errori fiscali/trasmissioni.

La necessità di riconciliare correttamente modalità di pagamento e corrispettivi è particolarmente rilevante dal 2026 con il collegamento logico POS-RT previsto dall'Agenzia.

---

# 30. Riconciliazione giornaliera struttura ricettiva

- [ ] **[OPERATIVO]** arrivi previsti vs arrivi effettivi.
- [ ] **[OPERATIVO]** partenze previste vs effettive.
- [ ] **[OPERATIVO]** camere/posti occupati.
- [ ] **[LEGGE/OPERATIVO]** Alloggiati da inviare = `0` a fine verifica.
- [ ] **[OPERATIVO]** ricevute Alloggiati presenti.
- [ ] **[OPERATIVO]** extra/addebiti camera.
- [ ] **[OPERATIVO]** caparre e saldi.
- [ ] **[OPERATIVO]** imposta di soggiorno.
- [ ] **[OPERATIVO]** dati necessari alle statistiche turistiche.

---

# 31. Test end-to-end prima del go-live

## Scenario ospite

- [ ] creare prenotazione;
- [ ] registrare caparra;
- [ ] effettuare check-in;
- [ ] verificare documento;
- [ ] preparare comunicazione Alloggiati;
- [ ] inviarla;
- [ ] scaricare ricevuta;
- [ ] cancellare dati/copie da eliminare;
- [ ] assegnare camera/posto letto;
- [ ] aggiungere consumazione ristorante alla camera;
- [ ] aggiungere extra;
- [ ] effettuare checkout;
- [ ] calcolare eventuale imposta di soggiorno;
- [ ] emettere documento fiscale/fattura;
- [ ] verificare SdI se viene emessa fattura;
- [ ] verificare inclusione corretta nelle statistiche.

## Scenario ristorante

- [ ] assegnare tavolo;
- [ ] inserire ordine;
- [ ] inviare piatti in cucina;
- [ ] inviare bevande al bar;
- [ ] modificare ordine;
- [ ] dividere conto;
- [ ] pagamento contanti;
- [ ] pagamento carta;
- [ ] documento RT;
- [ ] annullo/resi;
- [ ] chiusura POS;
- [ ] riconciliazione.

## Scenario errore

- [ ] Internet assente;
- [ ] Odoo riavviato;
- [ ] PostgreSQL riavviato;
- [ ] RT non raggiungibile;
- [ ] payment terminal non raggiungibile;
- [ ] SdI restituisce errore;
- [ ] Alloggiati non raggiungibile;
- [ ] disco quasi pieno;
- [ ] backup fallisce;
- [ ] restore del backup.

---

# 32. Sign-off finale

Prima dell'utilizzo reale:

- [ ] **Commercialista**
  - piano dei conti;
  - IVA;
  - fatturazione;
  - SdI;
  - corrispettivi;
  - RT/POS;
  - imposta di soggiorno;
  - conservazione;
  - retention documentale.

- [ ] **SUAP / Comune / Regione / Provincia autonoma**
  - classificazione rifugio;
  - autorizzazioni;
  - SCIA;
  - codice territoriale;
  - CIN;
  - statistica;
  - imposta soggiorno;
  - altri obblighi locali.

- [ ] **Consulente HACCP / autorità sanitaria competente**
  - piano HACCP;
  - cucina;
  - tracciabilità;
  - allergeni;
  - controlli;
  - registrazioni.

- [ ] **Consulente sicurezza / antincendio**
  - DVR;
  - formazione;
  - impianti;
  - presidi;
  - posti letto;
  - prevenzione incendi.

---

# 33. Definition of Done finale

Il sistema è considerabile pronto solo quando è possibile seguire senza sistemi paralleli improvvisati il flusso:

```text
Prenotazione
    ↓
Check-in
    ↓
Identificazione
    ↓
Alloggiati Web
    ↓
Pernottamento
    ↓
Ristorante / servizi / extra
    ↓
Magazzino e tracciabilità
    ↓
Pagamento
    ├── RT / corrispettivo
    └── Fattura → SdI
    ↓
Imposta soggiorno
    ↓
Statistiche turistiche
    ↓
Contabilità
    ↓
Conservazione legale
```

e contemporaneamente:

```text
Odoo
 ├── PostgreSQL
 ├── Filestore
 ├── Config
 └── Custom addons
        ↓
     Backup
        ↓
   copia indipendente
        ↓
  restore verificato
```

---

# Fonti principali verificate

## Odoo 19

- Documentazione ufficiale Odoo 19 — localizzazione fiscale italiana e moduli `l10n_it*`.
- Documentazione Odoo 19 — deployment on-premise e sicurezza Database Manager.
- Documentazione Odoo 19 — POS ristorante, sale/tavoli e ordini.
- Documentazione Odoo 19 — Preparation/Kitchen Display.
- Documentazione Odoo 19 — RT/stampanti fiscali italiane.
- Documentazione Odoo 19 — backup comprensivo di database e filestore.
- Documentazione Odoo 19 — lotti, scadenze e FEFO.
- Documentazione Odoo 19 — utenti, access rights e 2FA.

## Fiscalità italiana

- Agenzia delle Entrate — fatturazione elettronica e Sistema di Interscambio.
- Agenzia delle Entrate — servizio di conservazione elettronica.
- Agenzia delle Entrate — scadenze/collegamento POS-RT 2026.
- AgID — requisiti del sistema di conservazione.

## Strutture ricettive

- Ministero del Turismo — FAQ BDSR/CIN aggiornate all'11 maggio 2026.
- Polizia di Stato — Alloggiati Web e termini 24h/6h.
- Polizia di Stato — FAQ ricevute Alloggiati, conservazione cinque anni.
- Garante Privacy — nota del 29 aprile 2026 sui documenti di identità degli ospiti.
- ISTAT — Movimento dei clienti negli esercizi ricettivi, anno 2026.
- Dipartimento delle Finanze — regolamenti e delibere locali 2026.

## Alimentare

- Ministero della Salute — autocontrollo e HACCP.
- Regolamento CE 852/2004 — registrazione degli stabilimenti alimentari.
- Regolamento CE 178/2002 — rintracciabilità alimentare.
- Regolamento UE 1169/2011 — allergeni e informazioni alimentari.

## Privacy e sicurezza

- GDPR, art. 32 — sicurezza, disponibilità, resilienza, restore e test.
- Garante Privacy — data breach e regola delle 72 ore quando la notifica è dovuta.
- INAIL — valutazione dei rischi e DVR.
- Vigili del Fuoco — Attività 66, rifugi alpini oltre 25 posti letto.

---

# Dati ancora necessari per chiudere completamente la checklist

- [ ] Regione / Provincia autonoma.
- [ ] Comune.
- [ ] classificazione amministrativa esatta della struttura.
- [ ] numero massimo di posti letto.
- [ ] tipo di servizio ristorante/bar.
- [ ] presenza di dipendenti.
- [ ] eventuale vendita di alcolici.
- [ ] eventuale sito di prenotazione diretta.
- [ ] eventuali Booking.com/Airbnb/channel manager.
- [ ] eventuali pagamenti online.
- [ ] modello di Registratore Telematico scelto.

Finché **Regione/Provincia autonoma e Comune** non sono noti, le sezioni contrassegnate `[LOCALE]` non possono essere considerate chiuse.