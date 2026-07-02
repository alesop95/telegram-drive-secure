---
generated-from-commit: PENDING-FIRST-COMMIT
generated-from-branch: main
generated-date: 2026-07-02
covers-paths:
  - telegram-drive-secure-fork.md
source-doc: telegram-drive-secure-fork.md
last-verified-commit: PENDING-FIRST-COMMIT
---

# Roadmap

> Direzione e priorità del progetto. Tracciata. Trascritta dalla sezione 4 del documento di
> progetto `telegram-drive-secure-fork.md`, l'unica fonte disponibile in assenza di codice.

## Direzione

Trasformare `caamer20/Telegram-Drive`, un client che usa Telegram come storage illimitato ma
carica i file in chiaro, in un fork con crittografia end-to-end client-side, gestione robusta delle
chiavi, e hardening generale della superficie d'attacco, documentando in modo esplicito il modello
di minaccia e i suoi limiti.

## Priorità

Le fasi sono numerate nel documento di progetto e vanno eseguite in ordine, ciascuna con le sue
dipendenze dalla precedente.

```
Fase 0   Fork del repository originale e setup (CI propria, NOTICE, licenza)
Fase 1   Modulo crittografico Rust: Argon2id, gerarchia MK/FK, AEAD a chunk, formato contenitore
Fase 2   Integrazione della cifratura nella pipeline di upload/download, thumbnail cifrate
Fase 3   Vault e gestione chiavi: setup, sblocco/blocco, recovery key
Fase 4   Hardening generale: segreti a riposo, REST API, configurazione Tauri, supply chain
Fase 5   Documentazione, audit esterno o cargo audit, release
```

La Fase 0 non è ancora iniziata: il repository è stato inizializzato con il sistema di
documentazione e version control, ma il codice dell'originale non è stato ancora importato.

## Idee e ipotesi da verificare

Il documento di progetto lascia esplicitamente aperte, come compromessi da confermare in fase di
implementazione, la scelta tra `XChaCha8Poly1305` e `AES-256-GCM` come AEAD di default, e il
disegno dei link condivisibili in un modello end-to-end (la password attuale dell'originale non
basta, e il destinatario dovrebbe ricevere la File Key fuori banda). Da verificare anche se il
fork andrà eseguito preservando lo storico Git dell'originale o come import pulito in questo
repository, domanda già annotata in `context/current-work.md`.
