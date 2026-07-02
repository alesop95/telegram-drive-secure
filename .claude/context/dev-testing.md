---
generated-from-commit: c8935c50829cb56d0185124d6caf800abd11af6e
generated-from-branch: main
generated-date: 2026-07-02
covers-paths:
  - app/src-tauri/src/**
source-doc: telegram-drive-secure-fork.md
last-verified-commit: 313fcc81be48567cc0f7a3d66f51d54e1d8088a6
---

# Test di sviluppo

> Popolare leggendo la configurazione reale dei test. Verificato dopo l'import di Fase 0.

## Test runner e comandi

Nessun test automatico esiste nel codice importato: una ricerca di `#[test]` sotto
`app/src-tauri/src/` non ha trovato risultati, e non esiste una cartella `tests/`. Il documento di
progetto prevede, per il modulo crittografico Rust pianificato nella Fase 1, test vettoriali e
round-trip (cifra poi decifra) più *property testing*<sup>1</sup> con la dipendenza di sviluppo
`proptest`, non ancora aggiunta a `Cargo.toml`, oltre a un benchmark del throughput per tarare la
dimensione dei chunk.

## Rotte e dati mockati

Nessuna rotta o dato mockato è stata identificata; la REST API reale (`server.rs`,
`api_routes.rs`, `share_routes.rs`) non è stata letta in dettaglio in questo passaggio.

## Hook e controlli di qualità

Nessun hook di lint, type-check o build è ancora configurato in questo repository (il workflow
`.github/workflows/release.yml` importato non è stato verificato né adattato, vedi
`deployment.md`). Il documento di progetto anticipa, come checklist di sicurezza pre-release,
controlli quali `cargo audit` e `npm audit` puliti in CI, verifica che nessun segreto sia
persistito in chiaro, verifica che le chiavi e le passphrase siano azzerate dalla memoria al lock
o all'uscita, e verifica che i nonce non vengano mai riusati. Nessuno di questi controlli è ancora
automatizzato.

---

[^1]: *Property testing* — tecnica di test che verifica proprietà generali del codice su input
generati automaticamente, invece di soli casi singoli scritti a mano.
