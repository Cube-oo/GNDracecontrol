# GND Racecontrol

Console web per gestire una gara FPV da un’unica interfaccia: video, cronometraggio, piloti, risultati e replay. L’app è composta da pagine statiche HTML/CSS/JavaScript e non richiede una fase di build.

## Moduli

- **Live** — acquisisce il segnale video composito, lo divide in quattro feed, mostra piloti e tempi, e consente la registrazione WebM. La finestra **Apri feed separati** mostra i feed con lo stato di avanzamento sotto.
- **Timer** — controlla la connessione a RotorHazard, i tempi ricevuti e gli eventi diagnostici.
- **Judge** — seleziona heat e pilota e registra l’esito della valutazione: OK, DNF, DNS, Penalty o Reflight. Le decisioni sono salvate nel browser.
- **Risultati** — mostra qualifiche e tabelloni PRO e OPEN a partire dai dati configurati.
- **Replay** — riproduce registrazioni WebM locali con ricerca, velocità variabile e modalità fullscreen.
- **Manuale** — contiene la procedura operativa e le indicazioni per collegare sorgenti e servizi.

### Avanzamento gara

La Live mostra una corsia per ciascun pilota. Il valore iniziale è **3 giri**; nelle impostazioni si può scegliere un obiettivo da **1 a 100 giri**. Percentuale e icona avanzano quando Racecontrol riceve un giro valido da RotorHazard: la posizione non viene interpolata durante il giro.

## Requisiti

- Chrome o Microsoft Edge aggiornato; per la cattura video apri la pagina in una scheda del browser, non in una preview integrata che possa negare l’accesso alla camera.
- Un ingresso video USB o una capture card che fornisca la matrice FPV **2 × 2** attesa dalla Live. È possibile configurare anche una sorgente HD opzionale per la schermata senza segnale.
- Un’istanza RotorHazard raggiungibile dal computer sulla rete locale.
- Un foglio Google accessibile tramite link, con heat e piloti nel formato descritto nel Manuale.
- Connessione Internet per leggere Google Sheets e caricare la libreria Socket.IO usata per RotorHazard.

Il browser deve autorizzare la fotocamera. Se il permesso è stato negato, riattivalo dalle autorizzazioni del browser. `http://localhost` è il metodo consigliato per l’avvio locale; anche il protocollo HTTPS è adatto alla cattura video.

## Avvio locale

Con Python installato, apri un terminale nella cartella del progetto ed esegui:

```powershell
py -m http.server 8000 --bind 127.0.0.1
```

Poi apri [http://localhost:8000](http://localhost:8000) in Chrome o Edge. Se vuoi aprire direttamente la Live, usa [http://localhost:8000/#live](http://localhost:8000/#live). Lascia aperto il terminale mentre utilizzi l’app.

In alternativa, puoi aprire `index.html` direttamente, ma se il browser blocca la videocamera usa l’avvio tramite localhost.

## Pubblicazione con GitHub Pages

Racecontrol è un’app statica: per pubblicarla, carica nella stessa directory del repository `index.html`, `racecontrol-theme.css`, `gnd_live.html`, `gnd_timer.html`, `gnd_judge.html`, `gnd_results.html`, `gnd_replay.html` e `gnd_manual.html`. Attiva GitHub Pages dalle impostazioni del repository e seleziona la branch e la cartella che contengono questi file.

GitHub Pages serve il sito in HTTPS, requisito utile per la webcam. La pagina pubblicata deve comunque ricevere l’autorizzazione del browser. Inoltre, una pagina HTTPS che si collega a RotorHazard su HTTP o WebSocket locale può essere limitata dal browser o dalla rete: verifica la connessione con l’indirizzo e la porta reali dell’installazione prima di usarla durante una gara. L’hosting statico non crea un proxy verso RotorHazard.

Prima di pubblicare un repository, controlla che i dati e l’ID del foglio Google presenti nella configurazione possano essere esposti pubblicamente. Non inserire password, token o credenziali private nel codice del client.

## Dati e limiti

- Live e Timer leggono i tempi direttamente da RotorHazard; l’associazione dei nodi ai piloti va verificata prima della gara.
- La Live legge il foglio delle heat; Judge conserva le proprie decisioni nel `localStorage` del browser.
- La registrazione salva il segnale video FPV originale in WebM, senza sovrimpressioni e senza composizione HD.
- L’avanzamento rappresenta i giri validi ricevuti dal timer, non una posizione GPS o una misura continua sul tracciato.
- Le impostazioni locali sono memorizzate nel browser. Cambiare browser o profilo può richiedere di reinserire indirizzo del timer e ID del foglio.

## Struttura del progetto

```text
index.html              Console e navigazione
racecontrol-theme.css   Tema grafico condiviso
gnd_live.html           Video e diretta
gnd_timer.html          Connessione e diagnostica timer
gnd_judge.html          Valutazione piloti
gnd_results.html        Qualifiche e tabelloni
gnd_replay.html         Riproduzione WebM
gnd_manual.html         Manuale operativo
```
