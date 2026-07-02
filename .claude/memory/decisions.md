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
