# Work-log

> Append-only, in ordine cronologico inverso (la voce più recente in alto). Ogni passo
> significativo di codice e ogni intervento manuale rilevante lascia una voce con data, file
> toccati, motivo e commit di riferimento. Qui confluisce anche il log di riconciliazione dei
> documenti `.docx`, con il nome del documento sorgente e l'esito, così la data di allineamento
> sopravvive a un clone.

## 2026-07-02 — Fase 0: hardening del bundle e revisione della CI (Rust non disponibile)

Commit: nessuno ancora, working tree modificato
File toccati: `app/src-tauri/tauri.conf.json` (`identifier`, `productName`, titolo finestra),
`.claude/context/deployment.md`, `.claude/context/current-work.md`, `.claude/memory/decisions.md`
(ADR-005).
Motivo: chiesto di iniziare la Fase 1 (modulo crypto Rust). Verificato che questa macchina non ha
`cargo`/`rustc` installati; per non scrivere codice crittografico non compilabile né testabile, si
è scelto di rimandare la Fase 1 (ADR-005) e lavorare su due item di Fase 0 verificabili senza
Rust: letto per intero `.github/workflows/release.yml` (nessun riferimento hardcoded
all'originale, ma richiede secret `TAURI_SIGNING_PRIVATE_KEY`/`_PASSWORD` non configurati), e
aggiornati `identifier` (`com.alesop95.telegramdrivesecure`), `productName` e titolo finestra in
`tauri.conf.json` da quelli dell'autore originale. Non generata una coppia di chiavi di firma
dell'updater: richiede una decisione dell'utente su come custodire la chiave privata.

## 2026-07-02 — Ancoraggio delle schede al commit di import di Fase 0

Commit: 313fcc81be48567cc0f7a3d66f51d54e1d8088a6
File toccati: `last-verified-commit` in tutte le schede di `.claude/context/`, snapshot in
`.claude/memory/index.md`.
Motivo: l'utente ha committato e pushato l'import di Fase 0. Sostituito il segnaposto
`PENDING-IMPORT-COMMIT` con l'hash reale in ogni scheda; il punto di ripresa in
`memory/index.md` è stato spostato sulla Fase 1 (modulo crittografico Rust), il passo di sviluppo
successivo secondo la roadmap.

## 2026-07-02 — Fase 0: import pulito del codice upstream

Commit: PENDING-IMPORT-COMMIT (working tree modificato, non ancora committato dall'utente)
File toccati: `README.md`, `CHANGELOG.md`, `REST_API_Documentation.md`, `NOTICE.md`, `app/**`
(nuovo), `.github/workflows/release.yml` (nuovo), `.gitignore` (unione con i pattern upstream),
schede di `context/` (`STACK.md`, `design-and-security.md`, `deployment.md`, `dev-testing.md`,
`current-work.md`, `roadmap.md`), `memory/decisions.md` (ADR-004).
Motivo: risolta la domanda aperta di Fase 0 (fork GitHub vs import pulito) con la scelta esplicita
dell'utente per l'import pulito. Clonato `caamer20/Telegram-Drive` al commit upstream `8715927`
(release v1.9.7) in una cartella scratch, scansionato per segreti (nessuno trovato), poi copiati i
file rilevanti nel repository escludendo `screenshots/` (46 MB, non essenziale) e
`.github/FUNDING.yml` (redirigerebbe sponsor verso l'autore originale). Creato `NOTICE.md` con
attribuzione MIT e nota esplicita che il repository sorgente, pur dichiarando MIT nel README, non
contiene un file `LICENSE` verificabile. Lette le dipendenze reali (`Cargo.toml`, `package.json`) e
la configurazione Tauri (`tauri.conf.json`), da cui sono emersi due gap di sicurezza concreti,
non più solo pianificati: CSP con `unsafe-inline`/`unsafe-eval`, e updater Tauri che punta ancora
all'endpoint e alla chiave di firma dell'autore originale. Le schede di `context/` sono state
riscritte per riflettere il codice reale importato, con `last-verified-commit` impostato a
`PENDING-IMPORT-COMMIT` in attesa che l'utente esegua il commit di questo import.

## 2026-07-02 — Verifica drift e chiusura del ciclo di riconciliazione

Commit: da0bb0dff99bf03cdd04c704b4f3fb6a348bf74c
File toccati: `last-verified-commit` in tutte le schede di `.claude/context/`, snapshot in
`.claude/memory/index.md`.
Motivo: dopo il push dell'utente, `HEAD` (`da0bb0d`, il commit di ancoraggio stesso) risultava un
passo avanti rispetto a `last-verified-commit` (`c8935c5`). Confrontati i due commit con `git diff
--name-only c8935c5..da0bb0d`: tocca solo `.claude/context/` e `.claude/memory/`, nessun file nei
`covers-paths` (`telegram-drive-secure-fork.md`), quindi nessun drift reale. `last-verified-commit`
portato a `da0bb0d` in ogni scheda per chiudere il ciclo; da qui in poi il drift si misura da questo
commit.

## 2026-07-02 — Ancoraggio delle schede al primo commit reale

Commit: c8935c50829cb56d0185124d6caf800abd11af6e
File toccati: frontmatter di tutte le schede sotto `.claude/context/` e snapshot in
`.claude/memory/index.md`.
Motivo: l'utente ha eseguito il primo commit (root commit, identità confermata `alesop95
<alessio.sopranzi.95@gmail.com>`) e il primo push su `origin/main` (remoto vuoto, nessun rebase
necessario). Il segnaposto `PENDING-FIRST-COMMIT` è stato sostituito con l'hash reale in
`generated-from-commit` e `last-verified-commit` di ogni scheda, e lo snapshot di
`memory/index.md` è stato aggiornato di conseguenza, come previsto dal Passo 7 del runbook di
inizializzazione.

## 2026-07-02 — Inizializzazione del sistema di progetto e bootstrap del repository

Commit: PENDING-FIRST-COMMIT
File toccati: `.git/` (init locale), anatomia di `.claude` (`PROJECT-SYSTEM.md`, `rules/`,
`skills/`, `templates/`, `agents/`, `settings.json`, `memory/`, `context/`), `.gitignore`,
`CLAUDE.md`, `CLAUDE.local.md`, `_notes/`.
Motivo: allineamento del progetto, che aveva solo il documento di design
`telegram-drive-secure-fork.md` e nessuna storia git, allo standard portabile di contesto,
documentazione e version control descritto in `.claude/PROJECT-SYSTEM.md`, con il bundle di
riferimento in `E:\template-claude-developing\.claude`. Trattandosi di un repository nuovo senza
codice né commit pregressi, l'adozione ha seguito il percorso greenfield della sezione 10 e non
quello di allineamento della sezione 11, che presuppone una storia git già esistente. Contestualmente
è stata impostata l'identità git locale personale (`alesop95` / `alessio.sopranzi.95@gmail.com`,
alias SSH `github-personal`, remoto `git@github-personal:alesop95/telegram-drive-secure.git`),
secondo `.claude/rules/git-identity-and-repo.md`. Le schede di `context/` sono state popolate
leggendo il documento di progetto esistente, l'unico contenuto disponibile in assenza di codice.
Configurazione MCP e `README.md` pubblico non sono stati creati: restano promemoria espliciti, da
attivare solo su richiesta esplicita dell'utente (mai per default). Auto-memory nativa lasciata
disattivata (`autoMemoryEnabled: false`), in linea col default raccomandato dal sistema; l'igiene
dell'account attivo (`account2`) è stata verificata con `check-account-hygiene.ps1`, esito PASS su
entrambi i controlli.
