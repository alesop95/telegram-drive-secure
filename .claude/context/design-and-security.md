---
generated-from-commit: PENDING-FIRST-COMMIT
generated-from-branch: main
generated-date: 2026-07-02
covers-paths:
  - telegram-drive-secure-fork.md
source-doc: telegram-drive-secure-fork.md
last-verified-commit: PENDING-FIRST-COMMIT
---

# Design e sicurezza applicativa

> Popolare leggendo il codice attuale. In assenza di codice, questa scheda riporta il modello di
> minaccia e l'architettura crittografica come pianificati nel documento di progetto
> `telegram-drive-secure-fork.md`, da riconciliare non appena l'implementazione comincia.

## Modello di minaccia

Il documento di progetto distingue l'esposizione dell'originale da quella pianificata per il
fork. L'intercettazione di rete passiva è già mitigata da MTProto/TLS in entrambi i casi. Telegram
stesso, o chi ne compromette i server, oggi legge i file in chiaro; il fork mira a mitigare questo
con blob cifrati e chiavi mai trasmesse. La compromissione dell'account Telegram è oggi
un'esposizione piena; il fork la mitiga per la sola confidenzialità dei contenuti. L'accesso
fisico al dispositivo a riposo è mitigato se il vault è bloccato e i segreti sono cifrati. Il
malware locale con l'app sbloccata resta un'esposizione non coperta, un limite intrinseco di
qualunque client end-to-end dichiarato esplicitamente dal documento: se il dispositivo è
compromesso mentre il vault è sbloccato, le chiavi in memoria restano raggiungibili.

## Paradigmi di software design

Nessun paradigma è ancora osservabile nel codice, che non esiste. Il documento di progetto
pianifica un'architettura a *envelope encryption*<sup>1</sup>: una passphrase utente deriva, via
*Argon2id* con salt per-vault, una *Master Key* mai persistita in chiaro; da questa, via *HKDF*<sup>2</sup>,
si derivano una *Vault Key* che cifra il keystore locale (session string Grammers, `api_hash`, e
simili) e la chiave usata per wrappare le *File Key*. Ogni file ha una *File Key* casuale
indipendente che ne cifra il contenuto in chunk *AEAD*, e viene wrappata con la *Master Key* nei
metadati cifrati del file. Il vantaggio dichiarato è che cambiare passphrase richiede solo
ri-wrappare le *File Key*, non ri-cifrare i file.

Il formato del contenitore cifrato pianificato ha un header in chiaro, autenticato come dato
associato: magic `TGDX`, versione, identificatore dell'algoritmo, parametri della KDF<sup>3</sup>,
salt, *File Key* wrappata, dimensione del chunk, base del nonce. Seguono i chunk, ciascuno con
nonce o contatore e testo cifrato con tag di autenticazione. Nomi file e metadati sensibili non
vanno esposti in chiaro nei nomi dei messaggi o dei canali Telegram: il documento propone un
indice cifrato caricato come blob a parte, oppure la cifratura di nome e tipo nell'header stesso.

## Sicurezza applicativa

Il documento elenca, come hardening pianificato oltre la crittografia dei file, la cifratura a
riposo dei segreti (session string, `api_hash`, chiave della REST API) con la Vault Key invece che
in JSON in chiaro; una REST API locale disabilitata di default, con bind solo su `127.0.0.1`,
chiave forte confrontata in tempo costante, difesa dal DNS rebinding tramite validazione degli
header `Host` e `Origin`, e rate limiting; una CSP<sup>4</sup> restrittiva nella configurazione
Tauri, con allowlist minima e verifica che il webview non carichi contenuto remoto arbitrario, più
un updater firmato con chiavi proprie; controlli di supply chain con `cargo audit` e `npm audit` in
CI, `cargo deny` per licenze e duplicati, lockfile committati; azzeramento (`zeroize`) di chiavi e
passphrase in memoria, e nessun logging di segreti, passphrase, token o nomi file in chiaro. Nessuna
di queste misure è ancora implementata: sono impegni dichiarati nel documento di progetto, da
verificare voce per voce contro il codice reale quando esisterà.

## Diagrammi

Nessun diagramma esiste ancora. Il documento di progetto include uno schema testuale della
gerarchia delle chiavi (sezione 3.3) che, se promosso a diagramma versionato, andrebbe registrato
qui con corrispondenza uno a uno tra sorgente `.mmd` e componenti descritti.

---

[^1]: *Envelope encryption* — schema in cui una chiave dati è cifrata (wrappata) da una chiave
maestra, cosa che permette di ruotare la chiave maestra senza ricifrare i dati.
[^2]: *HKDF*, HMAC-based Key Derivation Function — funzione di derivazione di chiavi basata su HMAC.
[^3]: *KDF*, Key Derivation Function — funzione che deriva una o più chiavi da un segreto, qui una
passphrase.
[^4]: *CSP*, Content Security Policy — meccanismo che limita le origini da cui una webview può
caricare contenuto, per ridurre la superficie di attacco.
