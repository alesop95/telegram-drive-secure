# Snapshot di sincronizzazione

> Da leggere per primo a inizio sessione. Fotografa lo stato del progetto al commit di
> riferimento e mappa ogni scheda al suo stato di verifica. È la fonte di verità su cosa è fatto,
> non le spunte del diario.

## Stato

```
Branch attivo:        main
Commit di riferimento: da0bb0dff99bf03cdd04c704b4f3fb6a348bf74c (HEAD; import di Fase 0 non ancora committato)
Data snapshot:        2026-07-02
```

Fase 0 in corso: il codice di `caamer20/Telegram-Drive` (commit upstream `8715927`, release
v1.9.7) è stato importato nel working tree con import pulito (storia dell'originale non
preservata, decisione in `ADR-004`), non ancora committato dall'utente. Aggiunti `app/**`
(frontend React/TS, backend Rust/Tauri), `README.md`, `CHANGELOG.md`,
`REST_API_Documentation.md`, `NOTICE.md`, `.github/workflows/release.yml` (riferimento non
verificato). Esclusi `screenshots/` e `.github/FUNDING.yml`, per motivi documentati in
`NOTICE.md`. Due gap di sicurezza concreti emersi leggendo `tauri.conf.json`: CSP permissiva e
updater che punta ancora all'endpoint e alla chiave di firma dell'autore originale, dettagliati in
`context/design-and-security.md` e `context/deployment.md`.

## Stato di verifica delle schede

| Scheda | last-verified | Stato |
|---|---|---|
| STACK.md | PENDING-IMPORT-COMMIT | aggiornata sul codice reale importato |
| design-and-security.md | PENDING-IMPORT-COMMIT | aggiornata, due gap concreti annotati |
| deployment.md | PENDING-IMPORT-COMMIT | aggiornata sul codice reale importato |
| dev-testing.md | PENDING-IMPORT-COMMIT | aggiornata (nessun test trovato nel codice importato) |
| current-work.md | PENDING-IMPORT-COMMIT | aggiornata, Fase 0 in corso |
| roadmap.md | PENDING-IMPORT-COMMIT | aggiornata, Fase 0 marcata in corso |

## Punto di ripresa

L'utente esegue il commit e il push dell'import di Fase 0 (comandi in fondo alla risposta della
sessione corrente); poi si ancora `last-verified-commit` all'hash reale come già fatto dopo il
primo commit. Lavoro sostanziale ancora aperto per chiudere la Fase 0: chiarire lo stato della
licenza upstream, rivedere `.github/workflows/release.yml` prima di abilitarlo, e decidere
identifier del bundle. La Fase 1 (modulo crittografico Rust) resta il passo successivo naturale
una volta chiusa la Fase 0.
