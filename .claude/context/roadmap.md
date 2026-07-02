---
generated-from-commit: c8935c50829cb56d0185124d6caf800abd11af6e
generated-from-branch: main
generated-date: 2026-07-02
covers-paths:
  - telegram-drive-secure-fork.md
source-doc: telegram-drive-secure-fork.md
last-verified-commit: 313fcc81be48567cc0f7a3d66f51d54e1d8088a6
---

# Roadmap

> Direzione e priorità del progetto. Tracciata. Trascritta dalla sezione 4 del documento di
> progetto `telegram-drive-secure-fork.md`.

## Direzione

Trasformare `caamer20/Telegram-Drive`, un client che usa Telegram come storage illimitato ma
carica i file in chiaro, in un fork con crittografia end-to-end client-side, gestione robusta delle
chiavi, e hardening generale della superficie d'attacco, documentando in modo esplicito il modello
di minaccia e i suoi limiti.

## Priorità

Le fasi sono numerate nel documento di progetto e vanno eseguite in ordine, ciascuna con le sue
dipendenze dalla precedente.

```
Fase 0   Fork del repository originale e setup (CI propria, NOTICE, licenza)      IN CORSO
Fase 1   Modulo crittografico Rust: Argon2id, gerarchia MK/FK, AEAD a chunk        non iniziata
Fase 2   Integrazione della cifratura nella pipeline di upload/download            non iniziata
Fase 3   Vault e gestione chiavi: setup, sblocco/blocco, recovery key              non iniziata
Fase 4   Hardening generale: segreti a riposo, REST API, Tauri, supply chain       non iniziata
Fase 5   Documentazione, audit esterno o cargo audit, release                      non iniziata
```

La Fase 0 è in corso: il codice upstream è stato importato (import pulito, commit
`8715927` di `caamer20/Telegram-Drive`, release v1.9.7), `NOTICE.md` è stato creato, ma CI propria
e chiarimento sulla licenza restano da chiudere (dettaglio in `context/current-work.md`).

## Idee e ipotesi da verificare

Il documento di progetto lascia esplicitamente aperte, come compromessi da confermare in fase di
implementazione, la scelta tra `XChaCha8Poly1305` e `AES-256-GCM` come AEAD di default, e il
disegno dei link condivisibili in un modello end-to-end (la password attuale dell'originale non
basta, e il destinatario dovrebbe ricevere la File Key fuori banda). Emerse durante l'import: lo
stato della licenza upstream (badge MIT nel README, nessun file `LICENSE` nel repository sorgente),
e la necessità di un endpoint e di una chiave di firma dell'updater propri del fork prima di
qualunque distribuzione, al posto di quelli dell'autore originale ancora presenti in
`tauri.conf.json`.
