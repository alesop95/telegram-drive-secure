---
generated-from-commit: c8935c50829cb56d0185124d6caf800abd11af6e
generated-from-branch: main
generated-date: 2026-07-02
covers-paths:
  - app/**
  - NOTICE.md
  - README.md
last-verified-commit: 313fcc81be48567cc0f7a3d66f51d54e1d8088a6
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

File da modificare:

```
app/src-tauri/tauri.conf.json    identifier, productName e titolo finestra aggiornati a questa
                                  sessione (com.alesop95.telegramdrivesecure, "Telegram Drive
                                  Secure"); CSP permissiva e updater dell'autore originale restano
                                  da correggere (richiede una coppia di chiavi di firma proprie)
app/src-tauri/Cargo.toml         mancano ancora argon2, chacha20poly1305/aes-gcm, hkdf, zeroize
.github/workflows/release.yml    letto per intero: nessun riferimento hardcoded all'originale,
                                  ma richiede secret di firma non ancora configurati (vedi
                                  deployment.md); non abilitato
```

Definition of done:

- [x] codice importato in questo repository (import pulito, non fork GitHub)
- [x] `NOTICE.md` con attribuzione e changelog presente
- [x] `identifier`, `productName` e titolo finestra del bundle non implicano più l'autore
      originale (modifica di sola stringa, non verificata con una build reale: nessun toolchain
      Rust installato su questa macchina)
- [ ] stato della licenza upstream chiarito (badge MIT nel README, nessun file `LICENSE` trovato
      nel repository sorgente al commit importato — vedi `NOTICE.md`)
- [ ] CI propria funzionante: `release.yml` è stato revisionato ma non è utilizzabile finché non
      esiste una coppia di chiavi di firma del fork (vedi sotto) e i relativi secret GitHub
- [ ] conferma se AdMob/SDK di tracking sono presenti: nessun riferimento trovato nel codice
      importato, ma il sorgente Android (`app/src-tauri/src-android/`) è escluso dal repository
      upstream stesso tramite il suo `.gitignore`, quindi non è verificabile da un clone normale

Domande aperte:

Nessuna domanda bloccante residua per la sola Fase 0. Resta da decidere, prima di poter fare una
build reale del fork, chi genera la coppia di chiavi di firma dell'updater (`tauri signer
generate`, possibile via `npx @tauri-apps/cli` senza compilare il backend) e come gestisce la
chiave privata risultante; l'agente non la genera autonomamente perché va custodita con cura
dall'utente. Resta aperto anche se e come ottenere un chiarimento sulla licenza dall'autore
originale.

## Riconciliazione

Ultima verifica: 2026-07-02 al commit 313fcc81be48567cc0f7a3d66f51d54e1d8088a6, più modifiche di
sola stringa al bundle (`tauri.conf.json`) non ancora committate.
