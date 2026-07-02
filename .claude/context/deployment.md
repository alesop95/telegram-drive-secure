---
generated-from-commit: c8935c50829cb56d0185124d6caf800abd11af6e
generated-from-branch: main
generated-date: 2026-07-02
covers-paths:
  - app/src-tauri/tauri.conf.json
  - .github/workflows/**
source-doc: telegram-drive-secure-fork.md
last-verified-commit: PENDING-IMPORT-COMMIT
---

# Deployment

> Popolare leggendo la configurazione reale di infrastruttura e CI. Aggiornata dopo l'import
> pulito del codice upstream: la configurazione ora esiste, ma è quella dell'autore originale, non
> ancora adattata a questo fork.

## Livelli

Nessun livello di test o staging separato: l'applicazione è desktop (Tauri, target `all` in
`app/src-tauri/tauri.conf.json`, con bundle Android abilitato via `minSdkVersion 24`) distribuita
come build installabile, non come servizio ospitato. Non esiste un ambiente di produzione gestito
da questo fork.

## Comandi

Ereditati dall'originale, non ancora verificati end-to-end in questo repository: `npm run dev` e
`npm run build` in `app/` (rispettivamente `beforeDevCommand` e `beforeBuildCommand` in
`tauri.conf.json`), e il comando `tauri` esposto dallo script `"tauri": "tauri"` di
`app/package.json` per build e bundle nativi. Un workflow CI, `.github/workflows/release.yml`, è
stato importato come riferimento ma non è stato letto in dettaglio né adattato: cita segreti e
passaggi di firma specifici dell'autore originale (vedi `NOTICE.md`) e non va abilitato su questo
repository senza revisione.

## Variabili d'ambiente e segreti

Nessun `.env.example` esiste ancora in questo repository. Il codice importato gestisce a runtime
`api_id`, `api_hash` e la session string di Grammers (meccanismo esatto di storage non ancora
letto in `db.rs`/`models.rs`), oltre alla chiave della REST API locale esposta da `server.rs`.
Un gap concreto già verificabile: `tauri.conf.json` importato punta l'updater Tauri a
`https://github.com/caamer20/Telegram-Drive/releases/latest/download/latest.json` con la chiave
pubblica dell'autore originale, non di questo fork; va sostituito con endpoint e chiavi proprie
prima di qualunque build distribuita, dettaglio ripreso anche in `design-and-security.md`.
