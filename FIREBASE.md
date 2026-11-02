# Finanze condivise ChezNous ↔ MoneyManager 0.7.0

## Versione 0.7.0
La sincronizzazione bidirezionale usa queste regole e questa pagina. I telefoni già collegati in modalità solo invio richiedono un nuovo abbinamento del codice per ottenere la lettura e la modifica delle finanze condivise.

## Accesso
Gli account Google verificati già autorizzati mantengono l’accesso al documento domestico `varie/contatori`.

I telefoni anonimi sono registrati in `widgetDevices/{uid}`. `enabled: true` consente il precedente invio dei propri movimenti; **solo** `bidirectional: true`, impostato esplicitamente dalla pagina abbinando nuovamente il codice, consente anche la lettura e la modifica dell’intero archivio finanziario condiviso. Il telefono non accede a inventario, note domestiche o documenti di altri dispositivi.

Revocare un telefono imposta `enabled: false` e blocca sia letture sia scritture successive sul server. Le copie già ricevute restano sul telefono.

## Archivio e migrazione
La pagina copia una sola volta l’array storico `finanze_movimenti` in documenti con ID stabili `widgetMovements/web_{idOriginale}`. L’operazione è atomica e idempotente; dopo la copia imposta `varie/contatori.finanze_migrate: true`. L’array originale viene conservato immutato come archivio; le regole impediscono ai vecchi client di modificarlo dopo la migrazione. I contatori e le note domestiche continuano a funzionare.

La pagina e il telefono condividono da quel momento la stessa raccolta `widgetMovements`. Non si applica più una somma fra un array e una copia dello stesso storico. I documenti già provenienti da MoneyManager mantengono i loro ID e vengono aggiornati direttamente dal sito.

Il telefono importa tutto lo storico condiviso nel conto Novelli-Racca. Il vecchio storico locale precedente all’abbinamento è conservato in un archivio separato, consultabile nell’app ed escluso dai totali condivisi. Il conto Novelli personale resta separato.

## Revisioni, cancellazioni e conflitti
Ogni scrittura usa una transazione Firestore e incrementa anche `widgetState/finanze.version`. La verifica del saldo confronta questa versione per rifiutare una conferma obsoleta.

Un elemento eliminato diventa un marcatore inattivo con importo, data e nota oscurati. Non può essere ripristinato da una copia obsoleta. Uno spostamento sul conto personale ritira la copia condivisa senza inviare i nuovi dati personali; un successivo nuovo inserimento condiviso usa un altro documento.

In caso di modifiche concorrenti prevale la prima versione confermata dal server. Sul sito una modifica basata su una revisione superata viene rifiutata mantenendo i campi aperti. Nell’app la modifica locale viene conservata nel riquadro «Modifiche da rivedere»; è possibile mantenere la versione condivisa oppure creare esplicitamente un nuovo movimento dalla copia locale. Le eliminazioni prevalgono sulle modifiche obsolete.

## Android
Room passa alla versione 3 mantenendo i movimenti e aggiungendo tabelle per collegamenti, conflitti e archivio locale. Non installare APK più vecchi dopo l’aggiornamento e non disinstallare l’app per aggiornarla.

La pagina riceve gli aggiornamenti in tempo reale. Con l’app aperta il telefono ascolta la versione delle finanze e richiede una sincronizzazione; in background usa WorkManager, con controlli periodici di circa 15 minuti soggetti alle restrizioni Android. «Sincronizza» forza un tentativo appena la rete è disponibile. Le modifiche locali sono salvate offline; il sito richiede rete per confermare le transazioni e conserva i campi in caso di errore.

## Pubblicazione e abbinamento
1. Pubblicare le nuove regole del progetto `cheznous-2ce52`.
2. Pubblicare il nuovo `index.html` sul ramo main di ChezNous.
3. Ricaricare ChezNous: lo storico viene migrato automaticamente una sola volta.
4. Installare `MoneyManager-0.7.0-cheznous-bidirectional-debug.apk` come aggiornamento.
5. Copiare il codice mostrato dall’app e riautorizzarlo in Finanze → Collega widget MoneyManager, anche se era già collegato in modalità solo invio.
6. Premere Sincronizza nell’app. Verificare il conto Novelli-Racca, lo storico e i totali.

La pubblicazione di GitHub Pages non pubblica le regole Firebase. La conferma delle autorizzazioni cloud è richiesta separatamente dal controllo di sicurezza del browser.
