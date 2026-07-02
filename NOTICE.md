# NOTICE

> Attribuzione richiesta dal fork, secondo la sezione 9 di `telegram-drive-secure-fork.md` e la
> Fase 0 della roadmap. Non sostituisce una licenza: registra provenienza e modifiche.

## Provenienza

Questo progetto è un fork di [`caamer20/Telegram-Drive`](https://github.com/caamer20/Telegram-Drive),
importato il 2026-07-02 al commit upstream `8715927b4651930125d6478b7d69f74281d9010c` (release
v1.9.7, 2026-07-01), tramite import pulito del codice sorgente in questo repository (storia dei
commit dell'originale non preservata; l'alternativa era un fork GitHub reale con storico, scartata
in `memory/decisions.md`). Autore originale: Cameron Amer, secondo `app/src-tauri/Cargo.toml`.

## Stato della licenza — da verificare

Il `README.md` upstream dichiara la licenza MIT con un badge e una sezione dedicata ("Licensed
under the MIT License"), ma il repository sorgente, al commit importato, non contiene alcun file
`LICENSE`: il link del badge in `README.md` punta a un file inesistente. Questo non è verificabile
allo stato attuale e va trattato come tale, non come un fatto: prima di redistribuire o pubblicare
questo fork va chiarito con l'autore originale, o verificato in un commit upstream successivo, se
un `LICENSE` MIT formale esiste altrove o se va richiesto esplicitamente. Fino a chiarimento, il
codice importato va trattato come soggetto ai soli termini dichiarati nel `README.md` upstream.

## Cosa non è stato importato

La cartella `screenshots/` dell'upstream (circa 46 MB di immagini dell'interfaccia originale) e il
file `.github/FUNDING.yml` (link di sponsorizzazione dell'autore originale) sono stati
deliberatamente esclusi dall'import: il primo per peso non essenziale allo sviluppo, il secondo
perché redirigerebbe eventuali sponsor di questo fork verso l'autore originale. Il workflow
`.github/workflows/release.yml` è stato importato come riferimento ma non è stato verificato né
adattato: cita segreti e passaggi di firma specifici dell'autore originale e va rivisto prima di
essere abilitato su questo repository.

## Changelog delle modifiche del fork

Nessuna modifica al codice importato ancora eseguita: questo NOTICE nasce contestualmente
all'import stesso (Fase 0). Le modifiche successive (modulo crittografico, hardening) si
registrano qui in ordine cronologico inverso, oppure in `.claude/memory/decisions.md` se si
tratta di una decisione architetturale.

- 2026-07-02 — Import iniziale del codice upstream, nessuna modifica applicata.
