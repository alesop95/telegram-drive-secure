---
generated-from-commit: c8935c50829cb56d0185124d6caf800abd11af6e
generated-from-branch: main
generated-date: 2026-07-02
covers-paths:
  - telegram-drive-secure-fork.md
source-doc: telegram-drive-secure-fork.md
last-verified-commit: c8935c50829cb56d0185124d6caf800abd11af6e
---

# Stack applicativo

> Documento di recupero più importante: tracciato, perché un collega che clona deve vederlo.
> Nessun codice esiste ancora in questo repository: quanto segue è pianificato nel documento di
> progetto `telegram-drive-secure-fork.md` e va riverificato non appena il fork del repository
> originale viene eseguito e il codice comincia a esistere davvero.

## Stack e runtime

Il progetto è un fork hardenizzato di `caamer20/Telegram-Drive`, non ancora eseguito. Lo stack
ereditato dall'originale, secondo il documento di progetto, è *React* con *TypeScript* per il
frontend, *Rust* con il framework *Tauri* per il backend desktop, e la libreria *Grammers* come
client *MTProto*<sup>1</sup> verso Telegram. Il fork aggiunge un modulo crittografico in Rust, non
ancora scritto, con le dipendenze candidate elencate nel documento di progetto: `chacha20poly1305`
per l'AEAD<sup>2</sup>, `argon2` per la derivazione della chiave da passphrase, `hkdf` e `sha2` per
la gerarchia delle chiavi, `zeroize` per l'azzeramento delle chiavi in memoria, `rand` e
`getrandom` come CSPRNG<sup>3</sup>, e `proptest` come dipendenza di sviluppo per il property
testing. Le versioni esatte non sono fissate: il documento stesso segnala di verificarle al
momento dell'implementazione.

## Alternative deliberatamente escluse

Nessuna alternativa allo stack ereditato risulta ancora valutata e scartata nel documento di
progetto. La sola scelta esplicita è tra `XChaCha8Poly1305`/`chacha20poly1305` e `AES-256-GCM` per
l'AEAD dei chunk, con la prima indicata come opzione principale; la decisione definitiva è da
prendere in fase di implementazione (Fase 1 della roadmap).

## Flussi di codice e ruolo architetturale dei file

Nessun file di codice esiste in questo repository al momento della scrittura. Il documento di
progetto descrive un flusso previsto: cifratura *client-side* dei file prima dell'upload verso
Telegram e decifratura dopo il download, con un nuovo modulo `crypto` nel backend Tauri che
implementa la derivazione *Argon2id*, la gestione della *Master Key* e delle *File Key*, la
cifratura *AEAD* in streaming a chunk, e il formato di contenitore descritto nella scheda
`design-and-security.md`. Questa sezione va riscritta leggendo il codice reale non appena il fork
viene eseguito e i primi moduli vengono scritti.

## Riferimenti a snippet

Nessuno, in assenza di codice. Il solo riferimento disponibile è il documento di progetto in
radice, `telegram-drive-secure-fork.md`.

---

[^1]: *MTProto* — il protocollo di trasporto proprietario di Telegram.
[^2]: *AEAD*, Authenticated Encryption with Associated Data — cifratura autenticata che protegge
sia la confidenzialità sia l'integrità del testo cifrato.
[^3]: *CSPRNG*, Cryptographically Secure Pseudo-Random Number Generator — generatore di numeri
casuali adatto a uso crittografico.
