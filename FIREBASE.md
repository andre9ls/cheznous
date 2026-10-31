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
