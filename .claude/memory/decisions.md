# Registro delle decisioni architetturali

> Convenzione ADR-lite, append-only. Ogni decisione architetturale non ovvia entra come voce
> numerata con data, stato, contesto, decisione, motivazione e conseguenze. Una decisione non si
> cancella e non si riscrive: quando viene superata, si aggiunge una nuova voce che dichiara di
> superare la precedente e ne cita il numero. Le inferenze non confermate si marcano come da
> verificare e si promuovono a decisione solo quando una fonte le conferma.

## ADR-001 — Adozione del sistema di progetto portabile

Data: 2026-07-02
Stato: accettata
Contesto: il progetto necessita di uno stato interamente recuperabile da un clone e di
documentazione che resti allineata al codice senza rilettura integrale a ogni sessione.
Decisione: adottare il sistema descritto in `.claude/PROJECT-SYSTEM.md`, con motore di
riconciliazione ancorato ai commit e doppio livello documentale tracciato/ignorato.
Motivazione: persistenza strutturale su disco indipendente dalla sessione di chat, e controllo
umano sul versionamento.
Conseguenze: ogni passo significativo aggiorna schede, `last-verified-commit`, snapshot e
work-log; commit e push restano manuali.

## ADR-002 — Identità git personale per questo repository

Data: 2026-07-02
Stato: accettata
Contesto: la macchina ospita più identità git (una di lavoro via alias SSH `github-corp`, una
personale via `github-personal`); il rischio è firmare o pushare un progetto personale con
l'identità sbagliata.
Decisione: impostare a livello locale di questo repository `user.name = alesop95`,
`user.email = alessio.sopranzi.95@gmail.com`, `core.sshCommand` verso l'OpenSSH di sistema, e il
remoto `origin` su `git@github-personal:alesop95/telegram-drive-secure.git`.
Motivazione: `telegram-drive-secure-fork` è un progetto personale, non legato all'organizzazione
di lavoro; la protezione globale `user.useConfigOnly` è già attiva su questa macchina, quindi git
rifiuta comunque un commit privo di identità locale.
Conseguenze: ogni commit futuro su questo repository sarà firmato con l'identità personale;
push, creazione del repository remoto su GitHub e primo commit restano operazioni manuali
dell'utente.

## ADR-003 — Adozione in modalità greenfield, non allineamento retroattivo

Data: 2026-07-02
Stato: accettata
Contesto: il prompt riusabile di riferimento (`PROMPT-allinea-progetto-esistente.md`) presuppone
un progetto con codice e storia git già esistenti; questo repository, al momento
dell'inizializzazione, non aveva `.git` e conteneva solo un documento di design, nessun codice.
Decisione: seguire il percorso greenfield della sezione 10 di `PROJECT-SYSTEM.md` (scaffold
completo dell'anatomia da `templates/`) invece del percorso di allineamento della sezione 11
(inventario dei divari, ricostruzione della memoria dalla storia dei commit), che qui non ha
materiale su cui operare.
Motivazione: applicare il criterio di scelta della modalità dichiarato nella skill
`init-project-system` stessa, che distingue greenfield da allineamento in base allo stato reale
del repository, non alla formulazione della richiesta.
Conseguenze: le schede di `context/` sono popolate leggendo il documento di progetto esistente
invece che il codice, che non esiste ancora; andranno riconciliate con `sync-context` non appena
inizierà l'implementazione (Fase 0 della roadmap, il fork effettivo del repository originale).

## ADR-004 — Fase 0 eseguita come import pulito, non come fork GitHub

Data: 2026-07-02
Stato: accettata
Contesto: `context/current-work.md` segnalava come domanda bloccante se portare il codice di
`caamer20/Telegram-Drive` in questo progetto tramite il bottone "Fork" di GitHub (che preserva lo
storico dei commit originali ma crea un repository separato) oppure tramite import pulito nel
repository già inizializzato con identità, anatomia `.claude` e primo commit propri. L'utente ha
scelto esplicitamente l'import pulito.
Decisione: scaricato il codice upstream con `git clone --depth 1` al commit
`8715927b4651930125d6478b7d69f74281d9010c` (release v1.9.7, 2026-07-01) in una cartella scratch,
poi copiati nel repository `README.md`, `CHANGELOG.md`, `REST_API_Documentation.md`, l'intera
cartella `app/` (frontend React/TS e backend Rust/Tauri) e `.github/workflows/release.yml` come
riferimento non verificato. Esclusi deliberatamente `screenshots/` (circa 46 MB di immagini non
essenziali allo sviluppo) e `.github/FUNDING.yml` (redirigerebbe eventuali sponsor di questo fork
verso l'autore originale). Creato `NOTICE.md` con attribuzione, changelog del fork, ed
esplicitamente uno stato "da verificare" sulla licenza: il `README.md` upstream dichiara MIT ma il
repository sorgente, al commit importato, non contiene alcun file `LICENSE`.
Motivazione: questo repository ha già l'identità git personale, l'anatomia `.claude` e un primo
commit pushato su `origin/main`; un fork GitHub reale avrebbe creato un secondo repository separato
da riconciliare con questo, senza un beneficio proporzionato per un progetto personale a un solo
sviluppatore.
Conseguenze: la storia dei commit dell'originale non è preservata in questo repository, solo il
riferimento esplicito al commit upstream in `NOTICE.md`; l'attribuzione MIT resta garantita dal
`NOTICE.md` stesso, non da un fork Git formale. La scheda `STACK.md` e le altre schede di
`context/` sono state aggiornate leggendo il codice reale importato, non più il solo documento di
progetto.
