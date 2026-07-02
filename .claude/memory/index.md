# Snapshot di sincronizzazione

> Da leggere per primo a inizio sessione. Fotografa lo stato del progetto al commit di
> riferimento e mappa ogni scheda al suo stato di verifica. È la fonte di verità su cosa è fatto,
> non le spunte del diario.

## Stato

```
Branch attivo:        main
Commit di riferimento: 313fcc81be48567cc0f7a3d66f51d54e1d8088a6
Data snapshot:        2026-07-02
```

Fase 0 committata e pushata su `origin/main`. Il codice di `caamer20/Telegram-Drive` (commit
upstream `8715927`, release v1.9.7) è nel repository con import pulito (storia dell'originale non
preservata, decisione in `ADR-004`): `app/**` (frontend React/TS, backend Rust/Tauri),
`README.md`, `CHANGELOG.md`, `REST_API_Documentation.md`, `NOTICE.md`,
`.github/workflows/release.yml` (riferimento non verificato). Esclusi `screenshots/` e
`.github/FUNDING.yml`, per motivi documentati in `NOTICE.md`. Due gap di sicurezza concreti
restano aperti, individuati leggendo `tauri.conf.json`: CSP permissiva (`unsafe-inline`/
`unsafe-eval`) e updater che punta ancora all'endpoint e alla chiave di firma dell'autore
originale (dettagli in `context/design-and-security.md` e `context/deployment.md`). Fase 0 non
ancora chiusa: mancano CI propria verificata, chiarimento sulla licenza upstream (nessun file
`LICENSE` nel sorgente, solo un badge MIT nel README) e decisione sull'identifier del bundle.

## Stato di verifica delle schede

| Scheda | last-verified | Stato |
|---|---|---|
| STACK.md | 313fcc8 | aggiornata sul codice reale importato |
| design-and-security.md | 313fcc8 | aggiornata, due gap concreti annotati |
| deployment.md | 313fcc8 | aggiornata sul codice reale importato |
| dev-testing.md | 313fcc8 | aggiornata (nessun test trovato nel codice importato) |
| current-work.md | 313fcc8 | aggiornata, Fase 0 in corso |
| roadmap.md | 313fcc8 | aggiornata, Fase 0 marcata in corso |

## Punto di ripresa

Fase 1 (modulo crypto Rust) rimandata: questa macchina non ha un toolchain Rust installato
(`cargo`/`rustc` assenti, verificato da bash e PowerShell), e scrivere codice crittografico non
compilabile né testabile andrebbe contro il criterio di qualità della Fase 1 stessa (`ADR-005`).
Nel frattempo chiusi due item di Fase 0 verificabili senza Rust: `identifier`/`productName`/titolo
finestra del bundle non implicano più l'autore originale, e `.github/workflows/release.yml` è
stato letto per intero (nessun riferimento hardcoded all'originale, ma servono i secret di firma).
Working tree modificato, non ancora committato.

Prossima azione concreta: installare un toolchain Rust su questa macchina (o accettare
esplicitamente codice non verificato) per poter iniziare davvero la Fase 1; oppure, se si preferisce
restare senza Rust per ora, decidere chi genera la coppia di chiavi di firma dell'updater
(`npx @tauri-apps/cli signer generate`, non richiede compilazione del backend) per chiudere anche
l'ultimo item aperto di Fase 0.
