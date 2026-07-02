---
generated-from-commit: c8935c50829cb56d0185124d6caf800abd11af6e
generated-from-branch: main
generated-date: 2026-07-02
covers-paths:
  - app/**
  - NOTICE.md
  - README.md
last-verified-commit: PENDING-IMPORT-COMMIT
stato: in corso
---

# Lavoro in corso

> La fonte di verità su cosa è fatto resta `memory/index.md` e il work-log, non le spunte di
> questo file.

## Feature: Fase 0 — Fork e setup

Cosa fa: porta il codice di `caamer20/Telegram-Drive` in questo repository, con attribuzione MIT
e changelog, e prepara una CI propria. Descritta nella sezione 4 del documento di progetto
`telegram-drive-secure-fork.md`. La domanda aperta sul come (fork GitHub reale vs import pulito) è
stata risolta: import pulito, storia dell'originale non preservata (vedi `ADR-004` in
`memory/decisions.md`).

File creati:

```
NOTICE.md                        attribuzione, stato della licenza, changelog del fork
README.md                        importato invariato dall'upstream, da riscrivere in Fase 5
CHANGELOG.md                     importato invariato dall'upstream
REST_API_Documentation.md        importato invariato dall'upstream
app/**                           codice sorgente completo (frontend React/TS, backend Rust/Tauri)
.github/workflows/release.yml    importato come riferimento, non verificato né adattato
```

File da modificare (non ancora toccati):

```
app/src-tauri/tauri.conf.json    CSP permissiva (unsafe-inline/unsafe-eval) e updater che punta
                                  all'endpoint e alla chiave di firma dell'autore originale
app/src-tauri/Cargo.toml         mancano ancora argon2, chacha20poly1305/aes-gcm, hkdf, zeroize
.github/workflows/release.yml    da rivedere prima di abilitarlo (segreti e firma dell'originale)
```

Definition of done:

- [x] codice importato in questo repository (import pulito, non fork GitHub)
- [x] `NOTICE.md` con attribuzione e changelog presente
- [ ] stato della licenza upstream chiarito (badge MIT nel README, nessun file `LICENSE` trovato
      nel repository sorgente al commit importato — vedi `NOTICE.md`)
- [ ] CI propria funzionante (il workflow importato non è ancora verificato né adattato)
- [ ] conferma se AdMob/SDK di tracking sono presenti: nessun riferimento trovato nel codice
      importato, ma il sorgente Android (`app/src-tauri/src-android/`) è escluso dal repository
      upstream stesso tramite il suo `.gitignore`, quindi non è verificabile da un clone normale
- [ ] identifier del bundle (`com.cameronamer.telegramdrive` in `tauri.conf.json`) da valutare se
      cambiare per un fork distribuito separatamente

Domande aperte:

Nessuna domanda bloccante residua per la sola Fase 0. Restano da decidere, prima della Fase 4,
l'endpoint e la chiave di firma dell'updater proprio del fork, e se e come ottenere un chiarimento
sulla licenza dall'autore originale.

## Riconciliazione

Ultima verifica: 2026-07-02, import di Fase 0 non ancora committato (working tree modificato,
commit di riferimento da assegnare al prossimo commit dell'utente).
