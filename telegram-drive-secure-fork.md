# Telegram-Drive Secure Fork — Documento di Progetto

> Fork hardenizzato di [`caamer20/Telegram-Drive`](https://github.com/caamer20/Telegram-Drive) con focus su sicurezza e crittografia client-side dei file (zero-knowledge / end-to-end).

---

## 1. Contesto e obiettivi

### 1.1 Progetto originale
Telegram-Drive è un'app desktop/Android open-source (MIT) che trasforma un account Telegram in uno storage cloud "illimitato". Stack: **React + TypeScript** (frontend), **Rust + Tauri** (backend desktop), libreria **Grammers** come client MTProto Telegram. Funzioni chiave: upload/download, organizzazione in canali privati usati come cartelle, streaming media, link condivisibili, REST API locale, supporto proxy SOCKS5/MTProto.

### 1.2 Limiti di sicurezza dell'originale
Dal punto di vista della sicurezza, l'app come distribuita presenta alcune criticità tipiche di questa categoria:

- **I file sono caricati in chiaro** sui server Telegram. "Secure" nel README si riferisce al trasporto MTProto, non a confidenzialità verso Telegram stesso. Telegram (e chiunque comprometta l'account) può leggere ogni file.
- **Credenziali sensibili a riposo**: `api_id`, `api_hash`, session string di Grammers e chiave della REST API vengono salvati localmente; vanno valutati storage e cifratura a riposo.
- **REST API locale**: anche se "off by default", introduce superficie d'attacco (binding, autenticazione, CSRF/DNS-rebinding verso `localhost`).
- **Link condivisibili con password**: dipende da come sono generati e validati i token.
- **Build Android non firmata** con ad-network di terze parti (AdMob): da rimuovere nel fork per ridurre tracking e superficie.

### 1.3 Obiettivi del fork
1. **Crittografia E2E client-side** dei file prima dell'upload: Telegram immagazzina solo blob cifrati (modello zero-knowledge).
2. **Gestione robusta delle chiavi** derivate da passphrase utente, con storage protetto.
3. **Hardening generale**: storage segreti a riposo, irrigidimento della REST API, dipendenze, supply chain, configurazione Tauri.
4. **Trasparenza**: documentare chiaramente il modello di minaccia e cosa il fork protegge (e cosa no).

---

## 2. Modello di minaccia

| Avversario | Originale | Fork (obiettivo) |
|---|---|---|
| Intercettazione di rete passiva | Mitigato (MTProto/TLS) | Mitigato + payload già cifrato |
| Telegram stesso / leak server-side | **Esposto** (file in chiaro) | **Mitigato** (blob cifrati, chiavi mai trasmesse) |
| Compromissione dell'account Telegram | **Esposto** | **Mitigato** per la confidenzialità dei contenuti |
| Accesso fisico al dispositivo (a riposo) | Parziale | Mitigato se vault bloccato; segreti cifrati |
| Malware locale con app sbloccata | Esposto | Esposto (limite intrinseco) |
| Attaccante sulla REST API locale | Dipende dalla config | Mitigato (token, binding, origin checks) |

**Assunzione esplicita non coperta:** se il dispositivo è compromesso *mentre* il vault è sbloccato, le chiavi in memoria sono raggiungibili. È un limite intrinseco di qualunque client E2E.

---

## 3. Architettura della crittografia

### 3.1 Principi
- **Cifratura prima dell'upload, decifratura dopo il download** — la chiave non lascia mai il dispositivo.
- **Algoritmi moderni e auditati**, niente crittografia custom.
- **Streaming**: cifratura a chunk per supportare file grandi senza caricare tutto in RAM (coerente con il "transfer chunking" già presente).

### 3.2 Primitive consigliate (lato Rust)
- **AEAD per chunk**: `XChaCha8Poly1305` o `AES-256-GCM` (crate `chacha20poly1305` / `aes-gcm`, entrambi RustCrypto, audited).
- **KDF da passphrase**: `Argon2id` (crate `argon2`) con parametri robusti (es. m=64MiB, t=3, p=1, tarati sull'hardware).
- **CSPRNG**: `rand::rngs::OsRng` / `getrandom`.
- **Nonce**: 24 byte casuali (XChaCha) per chunk, mai riutilizzati. Architettura a chiave-file unica + nonce per chunk derivato da contatore.

### 3.3 Gerarchia delle chiavi (envelope encryption)
```
Passphrase utente
   │  Argon2id (salt per-vault)
   ▼
Master Key (MK)  ── mai persistita in chiaro
   │  HKDF
   ├─► Vault Key  → cifra il keystore locale (session string, api_hash, ecc.)
   └─► usata per "wrappare" le File Keys

Per ogni file:
   File Key (FK) casuale ── cifra il contenuto (AEAD a chunk)
   FK viene wrappata con la MK e salvata nei metadati cifrati del file
```
Vantaggi: cambiare passphrase richiede solo ri-wrappare le FK, non ri-cifrare i file; ogni file ha chiave indipendente.

### 3.4 Formato del contenitore cifrato
Header (in chiaro, autenticato come AAD):
```
magic "TGDX" | versione (u8) | alg-id (u8) | kdf-params | salt | wrapped_FK | chunk_size | nonce_base
```
Seguono i chunk: `[nonce/contatore][ciphertext+tag]...`. Nome file e metadati sensibili vanno cifrati separatamente (vedi §3.5); su Telegram il file viene caricato come `nome_offuscato.tgdx`.

### 3.5 Metadati
I nomi reali dei file e la struttura cartelle non vanno esposti in chiaro nei nomi dei messaggi/canali. Mantenere un **indice cifrato** (manifest) caricato esso stesso come blob cifrato, oppure cifrare nome+tipo nell'header del contenitore.

---

## 4. Roadmap di implementazione

### Fase 0 — Fork e setup (giorno 1)
- Fork del repo sul proprio GitHub; rinominare in modo da non implicare endorsement dell'autore originale.
- Mantenere licenza MIT + `NOTICE` con attribuzione all'originale e changelog delle modifiche.
- Configurare CI propria (build firmate, niente AdMob, scansione dipendenze).

### Fase 1 — Modulo crittografico Rust (core)
1. Nuovo crate/modulo `crypto` nel backend Tauri.
2. Implementare: derivazione Argon2id, gestione MK/FK, AEAD streaming a chunk, formato contenitore §3.4.
3. **Test vettoriali** e round-trip (encrypt→decrypt) + property testing (`proptest`).
4. Benchmark throughput per tarare `chunk_size`.

### Fase 2 — Integrazione pipeline upload/download
1. Inserire encrypt nel percorso pre-upload e decrypt nel post-download, mantenendo lo streaming.
2. Adattare anteprime/thumbnail e streaming media: o si genera una thumbnail cifrata separata, o si disabilita l'anteprima server-side (le anteprime in chiaro vanificano la E2E). **Scelta consigliata:** thumbnail cifrate generate localmente.
3. Streaming video/audio: decifrare on-the-fly a chunk verso il player locale.

### Fase 3 — Vault e gestione chiavi
1. Schermata di setup vault (crea passphrase → genera salt → deriva MK).
2. Sblocco/blocco vault; auto-lock dopo inattività; MK solo in memoria (`zeroize` su drop, crate `zeroize`).
3. Recovery: generare una **recovery key** (24 parole / stringa ad alta entropia) mostrata una volta sola; la passphrase persa **non è recuperabile** senza di essa — documentarlo chiaramente.

### Fase 4 — Hardening generale (vedi §5)

### Fase 5 — Documentazione, audit, release
- README riscritto con modello di minaccia onesto.
- Eventuale audit esterno o almeno `cargo audit` + revisione del modulo crypto.

---

## 5. Hardening generale (oltre la crittografia)

### 5.1 Segreti a riposo
- Salvare session string Grammers, `api_hash` e chiave REST **cifrati** con la Vault Key, non in JSON in chiaro.
- Valutare il keychain di sistema (crate `keyring`) per la recovery key.

### 5.2 REST API locale
- Default **disabilitata** (mantenere).
- Bind solo su `127.0.0.1`, mai `0.0.0.0`.
- **API key forte** generata casualmente, confronto in tempo costante.
- Difesa **DNS rebinding**: validare header `Host`/`Origin`; rifiutare richieste cross-origin.
- Rate limiting e logging degli accessi.

### 5.3 Configurazione Tauri
- CSP restrittiva nella `tauri.conf.json`; disabilitare API non usate; allowlist minima.
- Verificare che il webview non carichi contenuto remoto arbitrario.
- Abilitare e configurare correttamente l'**updater firmato** (chiavi proprie).

### 5.4 Supply chain
- `cargo audit` e `npm audit` in CI; `cargo deny` per licenze/duplicati.
- Lockfile committati; build riproducibili dove possibile.
- Rimuovere AdMob e SDK pubblicitari dal target Android.

### 5.5 Memoria e logging
- `zeroize` su chiavi e passphrase.
- Nessun logging di segreti, passphrase, token o nomi file in chiaro.

### 5.6 Link condivisibili
- Se si mantiene la condivisione: il destinatario deve ricevere la FK fuori banda, oppure i link condividono solo blob cifrati inutili senza chiave. Riprogettare in coerenza con la E2E (la "password" attuale non basta).

---

## 6. Compromessi e impatti UX

| Aspetto | Impatto | Mitigazione |
|---|---|---|
| Anteprime/streaming server-side | Non più possibili in chiaro | Thumbnail cifrate locali; decifratura on-the-fly |
| Ricerca per nome file | I nomi non sono leggibili da Telegram | Indice cifrato locale ricercabile |
| Performance upload/download | Overhead AEAD + KDF | Streaming a chunk, parametri tarati |
| Recupero password persa | **Impossibile** senza recovery key | UX chiara + recovery key obbligatoria al setup |
| Compatibilità coi file già caricati in chiaro | Nessuna | Migrazione opzionale: scarica→cifra→ricarica |

---

## 7. Dipendenze Rust suggerite

```toml
[dependencies]
chacha20poly1305 = "0.10"   # AEAD (alternativa: aes-gcm)
argon2 = "0.5"              # KDF
hkdf = "0.12"
sha2 = "0.10"
zeroize = { version = "1", features = ["derive"] }
rand = "0.8"
getrandom = "0.2"

[dev-dependencies]
proptest = "1"
```
*(Verificare le versioni correnti al momento dell'implementazione e tenerle aggiornate via CI.)*

---

## 8. Checklist di sicurezza pre-release

- [ ] Nessun segreto persistito in chiaro su disco
- [ ] MK/passphrase azzerate dalla memoria al lock/exit (`zeroize`)
- [ ] Nonce mai riutilizzati; verificato a livello di test
- [ ] AEAD: tag verificato prima di usare il plaintext (fail closed)
- [ ] REST API: binding localhost, token forte, validazione Origin/Host
- [ ] CSP Tauri restrittiva e allowlist minima
- [ ] `cargo audit` / `npm audit` puliti in CI
- [ ] Updater firmato con chiavi proprie
- [ ] Recovery key mostrata una sola volta e testata in flusso reale
- [ ] README con modello di minaccia onesto (cosa non è protetto)
- [ ] Rimossi AdMob/SDK di tracking
- [ ] Attribuzione MIT all'autore originale + NOTICE delle modifiche

---

## 9. Note legali ed etiche
- La licenza MIT consente fork e modifica; mantenere il copyright originale e indicare chiaramente le modifiche.
- L'app non è affiliata a Telegram; l'uso massivo come "storage illimitato" può violare i ToS di Telegram. Documentarlo.
- La crittografia E2E è legale nella maggior parte delle giurisdizioni, ma alcune impongono restrizioni: verificare la propria.

---

## 10. Riferimenti
- Repo originale: https://github.com/caamer20/Telegram-Drive
- RustCrypto (AEAD, Argon2): https://github.com/RustCrypto
- Tauri security: https://v2.tauri.app/security/
- OWASP ASVS (criteri di verifica): https://owasp.org/www-project-application-security-verification-standard/

---

*Documento generato come base di progetto. Il modulo crittografico va sottoposto a revisione/audit prima di qualunque uso su dati reali.*
