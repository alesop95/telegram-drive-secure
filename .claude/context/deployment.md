---
generated-from-commit: c8935c50829cb56d0185124d6caf800abd11af6e
generated-from-branch: main
generated-date: 2026-07-02
covers-paths:
  - app/src-tauri/tauri.conf.json
  - .github/workflows/**
source-doc: telegram-drive-secure-fork.md
last-verified-commit: 313fcc81be48567cc0f7a3d66f51d54e1d8088a6
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
`app/package.json` per build e bundle nativi. Verifica non eseguibile in questa sessione: nessun
toolchain Rust (`cargo`/`rustc`) è installato sulla macchina, quindi build e test del backend
Tauri non sono stati compilati né eseguiti.

`.github/workflows/release.yml` è stato letto per intero. Si attiva su push di un tag `v*`,
crea una release GitHub in bozza, builda su Windows/Linux/macOS (Intel e Apple Silicon) tramite
`tauri-apps/tauri-action`, e infine pubblica la release. Non contiene riferimenti hardcoded
all'autore originale: usa `context.repo.owner`/`context.repo.repo` dinamici, quindi opera già sul
repository in cui gira. Richiede però due secret non configurati in questo repository,
`TAURI_SIGNING_PRIVATE_KEY` e `TAURI_SIGNING_PRIVATE_KEY_PASSWORD`: senza di essi, un push di un
tag `v*` su questo repository creerebbe comunque una release in bozza (job `create-release`, che
non richiede il secret) per poi fallire nello step di build. Il workflow non va quindi considerato
pericoloso di per sé, ma non è ancora utilizzabile: manca una coppia di chiavi di firma proprie del
fork, e la relativa chiave pubblica non è ancora stata sostituita in `tauri.conf.json` (vedi sotto
e `design-and-security.md`).

## Note sul bundle

`app/src-tauri/tauri.conf.json` aveva `identifier: com.cameronamer.telegramdrive`,
`productName: Telegram Drive` e il titolo finestra `Telegram Drive`, tutti riferiti al progetto
originale. Aggiornati in questa sessione a `com.alesop95.telegramdrivesecure` e
`Telegram Drive Secure` (identifier, productName, titolo finestra), modifica di sola stringa non
verificata con una build reale per l'assenza del toolchain Rust. L'endpoint e la chiave pubblica
dell'updater restano invece quelli dell'autore originale: cambiarli richiede prima generare una
coppia di chiavi di firma proprie del fork (`tauri signer generate`, eseguibile via
`npx @tauri-apps/cli` senza compilare il backend), passo non eseguito qui perché la chiave privata
risultante va gestita con cura dall'utente (password, storage, segreto GitHub), non generata e
lasciata di default dall'agente.

## Variabili d'ambiente e segreti

Nessun `.env.example` esiste ancora in questo repository. Il codice importato gestisce a runtime
`api_id`, `api_hash` e la session string di Grammers (meccanismo esatto di storage non ancora
letto in `db.rs`/`models.rs`), oltre alla chiave della REST API locale esposta da `server.rs`.
Un gap concreto già verificabile: `tauri.conf.json` importato punta l'updater Tauri a
`https://github.com/caamer20/Telegram-Drive/releases/latest/download/latest.json` con la chiave
pubblica dell'autore originale, non di questo fork; va sostituito con endpoint e chiavi proprie
prima di qualunque build distribuita, dettaglio ripreso anche in `design-and-security.md`.
