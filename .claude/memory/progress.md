# Work-log

> Append-only, in ordine cronologico inverso (la voce più recente in alto). Ogni passo
> significativo di codice e ogni intervento manuale rilevante lascia una voce con data, file
> toccati, motivo e commit di riferimento. Qui confluisce anche il log di riconciliazione dei
> documenti `.docx`, con il nome del documento sorgente e l'esito, così la data di allineamento
> sopravvive a un clone.

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
