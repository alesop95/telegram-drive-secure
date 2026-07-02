---
generated-from-commit: c8935c50829cb56d0185124d6caf800abd11af6e
generated-from-branch: main
generated-date: 2026-07-02
covers-paths:
  - app/**
  - telegram-drive-secure-fork.md
source-doc: telegram-drive-secure-fork.md
last-verified-commit: 313fcc81be48567cc0f7a3d66f51d54e1d8088a6
---

# Stack applicativo

> Documento di recupero più importante: tracciato, perché un collega che clona deve vederlo.
> Aggiornata dopo l'import pulito del codice upstream (Fase 0): il codice ora esiste sotto `app/`,
> importato da `caamer20/Telegram-Drive` al commit upstream `8715927` (release v1.9.7), senza
> preservarne la storia git. Il modulo crittografico del fork non è ancora scritto.

## Stack e runtime

Confermato leggendo `app/package.json` e `app/src-tauri/Cargo.toml`: frontend *React* 19 con
*TypeScript*, build tramite *Vite* 7 e *Tailwind* 4; backend desktop *Rust* (edition 2021) con il
framework *Tauri* 2; client *MTProto*<sup>1</sup> verso Telegram tramite le crate *Grammers*
(`grammers-client`, `grammers-session`, `grammers-mtsender`, `grammers-tl-types`), fissate a un
fork Git pinnato al commit `d07f96f` di `github.com/Lonami/grammers`, non a una versione
pubblicata. Il backend espone anche una REST API locale via `actix-web` (`actix-web`,
`actix-cors`, `actix-files`, `actix-multipart`), usa `tokio` come runtime asincrono, `sqlite`
(bundled) per lo storage locale, e `reqwest` con `rustls-tls` per le chiamate HTTP in uscita. Sono
già presenti, ereditate dall'originale e non ancora impiegate per la crittografia end-to-end
pianificata dal fork, le crate `sha2`, `rand` 0.9, `bcrypt` e `constant_time_eq`: utili come base ma
non equivalenti al modulo `crypto` pianificato, che manca ancora di `argon2`, `chacha20poly1305` o
`aes-gcm`, `hkdf` e `zeroize` — nessuna delle quali risulta oggi in `Cargo.toml`. Il fork stesso
elenca queste ultime come dipendenze candidate, con la nota esplicita di verificarne le versioni al
momento dell'implementazione (Fase 1 della roadmap, non ancora iniziata).

## Alternative deliberatamente escluse

Nessuna alternativa allo stack ereditato è stata rivalutata durante l'import: si tratta dello
stack scelto dall'autore originale, non di una decisione di questo fork. La sola scelta esplicita
del fork resta quella tra `XChaCha8Poly1305`/`chacha20poly1305` e `AES-256-GCM` per l'AEAD<sup>2</sup>
dei chunk, con la prima indicata come opzione principale nel documento di progetto; la decisione
definitiva è da prendere in Fase 1.

## Flussi di codice e ruolo architetturale dei file

Inventario di prima lettura per directory, non ancora una lettura approfondita di ogni file. Il
frontend sotto `app/src/` si organizza in `components/desktop`, `components/mobile`,
`components/shared`, `context`, `hooks`, `i18n` e `theme`, a indicare una UI condivisa con varianti
per desktop e mobile. Il backend sotto `app/src-tauri/src/` espone, tra gli altri,
`server.rs` e `api_routes.rs` come probabile punto d'ingresso della REST API locale,
`share_routes.rs` per i link condivisibili, `upload_service.rs` come probabile pipeline di upload
verso Telegram, `db.rs` per lo storage locale via sqlite, `transcode.rs`, `fmp4_remux.rs` e
`mp4_utils.rs` per lo streaming media, `bandwidth.rs` e `vpn_optimizer.rs` per la gestione di rete,
e `socks5_bridge.rs` per il supporto proxy dichiarato nel documento di progetto. Questi ruoli sono
inferiti dal nome del file e dalle dipendenze dichiarate, non da una lettura riga per riga: vanno
confermati quando si interviene su ciascun modulo. `upload_service.rs` e la pipeline di download
corrispondente sono i punti in cui la Fase 2 dovrà inserire cifratura e decifratura, secondo il
piano descritto in `design-and-security.md`.

## Riferimenti a snippet

`app/src-tauri/Cargo.toml` per le dipendenze Rust, `app/package.json` per quelle Node, e
`app/src-tauri/tauri.conf.json` per la configurazione Tauri (CSP, bundle, updater), da riverificare
in dettaglio in `deployment.md` e `design-and-security.md` quando si affronterà l'hardening di
Fase 4.

---

[^1]: *MTProto* — il protocollo di trasporto proprietario di Telegram.
[^2]: *AEAD*, Authenticated Encryption with Associated Data — cifratura autenticata che protegge
sia la confidenzialità sia l'integrità del testo cifrato.
