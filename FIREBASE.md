# Accesso Firebase di ChezNous

Il sito usa Firebase Authentication con Google. Le regole di `firestore.rules`
consentono lettura del documento e creazione/aggiornamento di `varie/contatori`
solo ai due account Google verificati autorizzati. Tutti gli altri percorsi,
le query di raccolta e la cancellazione del documento sono negati.

## Configurazione console

- Progetto: `cheznous-2ce52`.
- Authentication → Metodo di accesso: Google abilitato.
- Authentication → Impostazioni → Domini autorizzati: `andre9ls.github.io`.
- Firestore → Regole: pubblicare il contenuto esatto di `firestore.rules`.

Le regole non hanno scadenza. Non ripristinare la modalità test.
La verifica nel codice del sito serve all’interfaccia: la protezione dei dati
è applicata dalle regole pubblicate su Firebase.

Per modificare gli account autorizzati, aggiornare sia `firestore.rules` sia
`accountAutorizzati` in `index.html`, verificare e pubblicare entrambi.
Con Firebase CLI autenticata, le regole si pubblicano con:

```sh
firebase deploy --only firestore:rules --project cheznous-2ce52
```

Il sito rimane pubblicato su GitHub Pages; il push del sito non pubblica
le regole Firestore. Non è stato aggiunto supporto offline con questa modifica.


## Widget MoneyManager (0.6.0)

Abilitare anche Authentication → Metodo di accesso → Anonimo, senza pulizia
automatica. L’accesso anonimo serve esclusivamente per identificare ogni
installazione del widget. Non concede accesso al documento domestico.

Un utente Google già autorizzato abbina il telefono tramite il codice mostrato
nell’app, da Finanze → Collega widget MoneyManager. L’abbinamento crea un
documento `widgetDevices/{uid}` con `enabled: true`. La revoca imposta false;
i movimenti già ricevuti rimangono visibili.

Il telefono invia solo nuovi movimenti del conto Novelli-Racca, escludendo
lo storico presente al primo collegamento e i saldi iniziali. Scrive documenti
con ID stabile `widgetMovements/{uid}_{idMovimento}` e revisioni crescenti.
Modifiche, eliminazioni e trasferimenti al conto personale si gestiscono
in MoneyManager. Le rimozioni sono marcatori inattivi privi di importo,
data e nota; non si cancellano fisicamente.

Ogni scrittura aggiorna nella stessa transazione `widgetState/finanze.version`.
La versione impedisce che una modifica contemporanea al flusso del widget
produca una verifica del saldo obsoleta. Le regole non consentono al telefono
di leggere movimenti, elencare dispositivi o accedere a `varie/contatori`.
Solo i due account Google autorizzati leggono il flusso e gestiscono gli abbinamenti.

Pubblicare le regole aggiornate prima della pagina. I movimenti del widget
vengono combinati in lettura con `finanze_movimenti`; non vengono copiati
nell’array storico del documento domestico. La pagina riceve gli aggiornamenti
anche se era chiusa al momento dell’inserimento.
