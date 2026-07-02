---
generated-from-commit: PENDING-FIRST-COMMIT
generated-from-branch: main
generated-date: 2026-07-02
covers-paths:
  - telegram-drive-secure-fork.md
source-doc: telegram-drive-secure-fork.md
last-verified-commit: PENDING-FIRST-COMMIT
---

# Test di sviluppo

> Popolare leggendo la configurazione reale dei test. Nessuna esiste ancora in questo repository.

## Test runner e comandi

Nessun test runner è ancora configurato. Il documento di progetto prevede, per il modulo
crittografico Rust pianificato nella Fase 1 della roadmap, test vettoriali e round-trip
(cifra poi decifra) più *property testing*<sup>1</sup> con la dipendenza di sviluppo `proptest`,
oltre a un benchmark del throughput per tarare la dimensione dei chunk.

## Rotte e dati mockati

Nessuna rotta o dato mockato esiste ancora, in assenza di codice.

## Hook e controlli di qualità

Nessun hook di lint, type-check o build è ancora configurato. Il documento di progetto anticipa,
come checklist di sicurezza pre-release, controlli quali `cargo audit` e `npm audit` puliti in CI,
verifica che nessun segreto sia persistito in chiaro, verifica che le chiavi e le passphrase siano
azzerate dalla memoria al lock o all'uscita, e verifica che i nonce non vengano mai riusati.
Nessuno di questi controlli è ancora automatizzato.

---

[^1]: *Property testing* — tecnica di test che verifica proprietà generali del codice su input
generati automaticamente, invece di soli casi singoli scritti a mano.
