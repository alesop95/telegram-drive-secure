---
generated-from-commit: PENDING-FIRST-COMMIT
generated-from-branch: main
generated-date: 2026-07-02
covers-paths:
  - telegram-drive-secure-fork.md
source-doc: telegram-drive-secure-fork.md
last-verified-commit: PENDING-FIRST-COMMIT
---

# Deployment

> Popolare leggendo la configurazione reale di infrastruttura e CI. Nessuna delle due esiste
> ancora in questo repository.

## Livelli

Nessun livello di test, staging o produzione è ancora definito. Il documento di progetto prevede,
nella Fase 0 della roadmap, di configurare una CI propria dopo il fork del repository originale,
con build firmate, senza AdMob, e con scansione delle dipendenze; nessun dettaglio di hosting o
dominio è specificato, trattandosi di un'applicazione desktop (Tauri) e Android distribuita come
build, non di un servizio ospitato.

## Comandi

Nessun comando di build, rilascio o rollback esiste ancora in questo repository.

## Variabili d'ambiente e segreti

Nessuna variabile d'ambiente è ancora definita nel codice, che non esiste. Il documento di
progetto anticipa la necessità di gestire in modo sicuro, a riposo, `api_id` e `api_hash` di
Telegram, la session string di Grammers, e la chiave della REST API locale, cifrati con la Vault
Key pianificata invece che in chiaro; nessun valore va mai committato, e un `.env.example` andrà
istanziato non appena queste variabili prendono forma concreta nel codice.
