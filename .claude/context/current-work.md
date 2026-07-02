---
generated-from-commit: c8935c50829cb56d0185124d6caf800abd11af6e
generated-from-branch: main
generated-date: 2026-07-02
covers-paths:
  - telegram-drive-secure-fork.md
last-verified-commit: c8935c50829cb56d0185124d6caf800abd11af6e
stato: in pianificazione
---

# Lavoro in corso

> La fonte di verità su cosa è fatto resta `memory/index.md` e il work-log, non le spunte di
> questo file.

## Feature: Fase 0 — Fork e setup

Cosa fa: esegue il fork effettivo di `caamer20/Telegram-Drive` sul GitHub personale
(`alesop95`), lo rinomina per non implicare un endorsement dell'autore originale, mantiene la
licenza MIT con un file `NOTICE` di attribuzione e changelog delle modifiche, e configura una CI
propria (build firmate, nessun AdMob, scansione delle dipendenze). Descritta nella sezione 4 del
documento di progetto `telegram-drive-secure-fork.md`.

File da creare:

```
NOTICE                     attribuzione MIT all'autore originale e changelog delle modifiche
.github/workflows/ci.yml   pipeline di build, audit dipendenze, firma
```

File da modificare:

```
(nessuno: il codice dell'originale non è stato ancora importato in questo repository)
```

Definition of done:

- [ ] repository forkato e collegato come remoto (o importato) sotto `alesop95`
- [ ] `NOTICE` con attribuzione e changelog presente
- [ ] CI minima funzionante (build, audit dipendenze)
- [ ] AdMob e SDK di tracking rimossi dal target Android

Domande aperte:

Il documento di progetto non specifica se il fork andrà eseguito tramite la funzione "Fork" di
GitHub, mantenendo lo storico dell'originale, oppure tramite import pulito in questo repository
già inizializzato (`alesop95/telegram-drive-secure`), che ha una storia propria indipendente. La
scelta va presa prima di eseguire la Fase 0, perché determina se questo repository resta quello
definitivo o se il codice va invece portato dentro un repository forkato a parte.

## Riconciliazione

Ultima verifica: 2026-07-02 al commit c8935c50829cb56d0185124d6caf800abd11af6e.
