---
title: "Cryptography: From Fundamentals to Interview Scenarios"
subtitle: "Expanded Section 5 | EY Senior Analyst Interview Preparation"
author: "Personal study guide"
date: "21 October 2026"
lang: en-IN
---

# How to use this guide

This expands section 5 of your original cybersecurity notes. It assumes ordinary computer engineering knowledge and some exposure to cryptography. Read the explanations first, then close the document and explain the examples aloud. You need to connect algorithms to a security problem, not reproduce difficult proofs.

**Learning target:** Choose between encryption, hashing, message authentication, signatures, and password hashing; explain certificates and TLS; recognize implementation mistakes; discuss business impact and remediation.

**Interview context:** These are broadly useful cybersecurity and consulting topics, not a verified EY question bank or an exact role syllabus. Senior Analyst roles vary by team. For a beginner, accurate reasoning and clear limits are more credible than claiming production experience.

**Priority:** Sections 1–9 are essential. Sections 10–12 add practical depth. Finish with the scenarios and recall sheet.

# 1. Start with the problem, then choose the tool

Imagine an employee sending a confidential invoice to a client. Several different things could go wrong: someone reads it, changes its amount, pretends to be the sender, or replays an old payment instruction. Each needs a different control.

| Requirement | Meaning | Suitable building block | Important limit |
|---|---|---|---|
| Confidentiality | Unauthorized parties cannot read the content | Encryption | An authorized or compromised endpoint can still read it |
| Integrity | Unauthorized changes can be detected | Authenticated encryption, HMAC, digital signatures | A bare checksum does not prove trusted origin |
| Authenticity | Evidence of the expected sender or keyholder | HMAC or digital signature, with trusted keys | Key theft undermines the assurance |
| Public verification | Other parties can verify a signed message | Digital signature | A valid signature does not prove the message is truthful |
| Password verification | Check a login without storing its password | Password hashing | Weak passwords can still be guessed |
| Freshness | Reject stale or repeated instructions | Nonce, timestamp/sequence and replay tracking | A valid signature alone does not reject an old message |

**Plain-language analogy:** Encryption is a locked box; a hash is a content fingerprint; an HMAC is a seal made using a shared secret; a signature is a seal that anyone with the trusted public key can verify. These analogies explain purpose, not the mathematics.

**Encoding** changes representation so systems can transport data. Base64 and URL encoding are reversible without a secret. **Obfuscation** makes content harder to understand but is not a cryptographic guarantee. **Compression** reduces size and is not encryption.

**Example:** A JWT payload written in Base64URL can usually be decoded by anyone holding the token. A signature can prevent undetected modification while the contents remain readable.

**Interview answer:** “I first identify the property needed. Encryption addresses confidentiality, while authenticated encryption also detects tampering. Hashes create digests, HMAC uses a shared secret for message authentication, and signatures enable verification using a public key. Passwords require a dedicated password-hashing scheme.”

# 2. Essential vocabulary and key strength

**Plaintext** is the original data. **Ciphertext** is encrypted data. An **algorithm** defines the transformation; a **key** selects the secret cryptographic behavior. Security should depend on protecting keys rather than hiding the algorithm.

A **bit** has two values. A uniformly random 128-bit key has 2^128 possible values. This is different from a 128-character human sentence: predictable language has less uncertainty, or **entropy**, than uniformly random bytes. Key strength also depends on the algorithm and attack model; equal key lengths across unrelated algorithm families do not imply equal security.

**Brute force** tries candidate keys or passwords. A **dictionary attack** tries likely words and patterns. Attackers often bypass strong mathematics through leaked keys, weak random generation, phishing, or broken authorization.

**Cryptographically secure randomness** is designed to remain unpredictable. A normal pseudo-random generator used for simulations may be predictable after observing its state. Use operating-system or vetted-library cryptographic generators for keys and security tokens.

**Key vs password:** A cryptographic key should have suitable random structure and length. A human password is usually weaker and must pass through an appropriate password-based derivation scheme when used to protect encryption keys. Adding characters does not automatically make a weak construction safe.

# 3. Symmetric encryption

## 3.1 What it does

Symmetric encryption uses a shared secret key for encryption and decryption. It is efficient enough for files, disks, databases, and large network transfers. **AES** is a block cipher with a 128-bit block size and supported key lengths of 128, 192, or 256 bits. Block size and key size are different concepts.

An algorithm normally needs a complete secure construction or **mode of operation**. Saying “we use AES” is incomplete: the mode, nonce handling, authentication, and key storage matter.

| Construction | What to remember | Typical mistake |
|---|---|---|
| AES-GCM | Authenticated encryption; protects content and checks tampering | Reusing a nonce with the same key |
| ChaCha20-Poly1305 | Another authenticated encryption construction | Incorrect nonce generation or tag handling |
| AES-CBC | Encryption mode that does not inherently authenticate data | Treating encryption alone as tamper protection |
| ECB | Identical plaintext blocks produce identical ciphertext blocks under the key | Exposes patterns; unsuitable for general sensitive structured data |

**AEAD** means Authenticated Encryption with Associated Data. It protects plaintext confidentiality and authenticates both ciphertext and optional associated data. Associated data is authenticated but remains visible: for example, a record identifier or protocol header.

Conceptually:

```text
encrypt(key, nonce, plaintext, associated_data)
    -> ciphertext and authentication_tag

decrypt_and_verify(key, nonce, ciphertext, associated_data, tag)
    -> plaintext OR authentication failure
```

The receiver must reject failed authentication and must not use unauthenticated plaintext. A nonce and tag normally accompany the ciphertext; neither needs to be hidden. The key must be protected.

## 3.2 IV, nonce, salt, and key are different

| Term | Purpose | Secret? | Reuse rule |
|---|---|---|---|
| Key | Controls encryption or authentication | Yes for symmetric/private keys | Lifecycle and usage limits apply |
| Nonce | Distinguishes operations under a key | Usually no | Follow the construction; GCM requires uniqueness under the key |
| IV | Initialization input for an encryption mode | Usually no | Requirements depend on the mode; CBC requires an unpredictable IV |
| Salt | Separates password-hash computations | No | Generate a fresh random salt for each password hash |
| Pepper | Optional extra secret in password protection | Yes | Keep separate from the password database |

**Why nonce reuse is serious:** GCM uses a counter-based stream internally. Reusing its nonce and key can reveal relationships between messages and undermine authentication. “The nonce is public” does not mean it is optional. Random nonces still need a design that respects collision probability and per-key limits; coordinated counters need reliable state.

**Practical recommendation:** Use a maintained high-level library that manages the construction correctly. Avoid designing your own mode or combining primitives casually. See [OWASP Cryptographic Storage guidance](https://cheatsheetseries.owasp.org/cheatsheets/Cryptographic_Storage_Cheat_Sheet.html).

## 3.3 Worked business example

A client stores identity documents in object storage. Encrypt each document with a random data key using AEAD. Bind relevant context, such as a document identifier, as associated data where the design supports it. Restrict who may request decryption, protect keys in a key-management service, and audit access. Encrypting documents does not stop an overprivileged application from decrypting every document.

**Interview follow-up: “Is AES-256 always safer than AES-128?”** Both can be strong with correct use. A longer key does not repair nonce reuse, exposed secrets, or an application that returns decrypted data to the wrong user. Discuss the organization's requirements and implementation before choosing.

# 4. Asymmetric cryptography and key agreement

Asymmetric cryptography uses a related public/private key pair. The public key may be shared, but you must verify whose key it is. The private key must remain controlled by its owner.

| Algorithm/family | Main role in this guide | What not to say |
|---|---|---|
| RSA | Encryption or signatures using the appropriate scheme | “Raw RSA is a safe complete encryption design” |
| ECDH | Establish shared secret material | “ECDH signs a document” |
| ECDSA / EdDSA | Digital signatures | “They encrypt files” |
| AES | Symmetric encryption primitive | “AES uses a public/private pair” |

**Public-key encryption:** To send confidential material to Bob, use Bob's authenticated public encryption key; Bob uses his private key to recover it. For RSA, schemes such as OAEP provide needed structure; do not use textbook RSA. Large files usually use a hybrid scheme rather than direct public-key encryption of all bytes.

**Key agreement:** Alice and Bob exchange public contributions and independently compute matching shared secret material using their private contributions. They then derive usable keys using a key derivation function. The secret itself is not simply transmitted across the network.

**Small Diffie–Hellman illustration:** With public prime p=23 and generator g=5, Alice chooses private a=6 and sends A=8; Bob chooses private b=15 and sends B=19. Alice computes B^a mod p=2; Bob computes A^b mod p=2. These tiny numbers are intentionally insecure and only demonstrate matching results. Real systems use approved groups or elliptic curves and vetted implementations.

**The missing piece is authentication:** An attacker who can replace the exchanged public values can establish separate secrets with both parties. Authenticated protocols use certificates/signatures or another trusted method to bind the exchange to the intended peer. Bare Diffie–Hellman does not stop a man-in-the-middle attack.

**Hybrid encryption:** Establish or wrap a random symmetric key using an appropriate asymmetric mechanism, then encrypt the bulk content symmetrically. This combines manageable trust establishment with efficient data encryption.

# 5. Hash functions and HMAC

## 5.1 Cryptographic hashes

A hash maps input of arbitrary length to a fixed-length digest. SHA-256 produces 256 bits, commonly displayed as 64 hexadecimal characters because each hex character represents four bits.

- **Preimage resistance:** Given a digest, finding an input that produces it should be infeasible.
- **Second-preimage resistance:** Given one message, finding a different message with the same digest should be infeasible.
- **Collision resistance:** Finding any two different messages with the same digest should be infeasible.

Collisions mathematically exist because unlimited inputs map to a finite output space. Security is about computational difficulty. For an ideal n-bit hash, generic collision search has roughly 2^(n/2) complexity; generic preimage search is roughly 2^n. You do not need to derive this in a basic interview, but it explains why “256-bit hash” is not “256-bit collision strength.”

**File-download example:** Comparing a downloaded file's digest to a trusted published digest can reveal corruption or substitution. If an attacker can replace both the file and the displayed digest, matching hashes prove little about origin. A signed release and trusted signer verification address a different trust problem.

**CRC/checksum vs cryptographic hash:** CRCs are useful for accidental-error detection. They are not designed to resist deliberate manipulation. MD5 and SHA-1 should not be chosen for new collision-sensitive security designs.

## 5.2 HMAC

HMAC combines a cryptographic hash and a shared secret to authenticate messages. It uses a specific construction; it is not simply “concatenate a key and hash.” Use the standard library implementation.

**Webhook example:** A payment service and merchant share a secret. The payment service authenticates a message containing the event ID, timestamp, and payment data. The merchant recomputes and compares the HMAC using an appropriate constant-time verification function. It also checks freshness and whether the event was already processed.

An HMAC alone does not stop replay: the same valid message can be delivered twice. The receiver needs replay controls and idempotent business processing. HMAC also does not hide the contents. Since either secret holder can generate the code, it does not establish which one created a message for an independent third party.

# 6. Password storage and login security

When a user logs in, the application generally needs to verify the supplied password, not recover the original password. Reversible encryption creates unnecessary exposure if the decryption key leaks.

**Why fast hashing fails:** If attackers steal a password database, they can guess passwords offline. SHA-256 is built to process data quickly, which also helps attackers test guesses. Password-hashing schemes deliberately make each guess cost more time and, for memory-hard designs, memory.

**Typical design:** Use Argon2id with a unique random salt and parameters benchmarked for the environment. Suitable alternatives depend on platform and compliance requirements. OWASP discusses Argon2id, scrypt, bcrypt for legacy contexts, and PBKDF2 where applicable. Store the algorithm, parameters, salt, and verifier in a standard library-supported format; keep an optional pepper separately. See [OWASP Password Storage guidance](https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html).

```text
Registration:
  password -> library password_hash() -> encoded verifier
  store verifier; do not log password

Login:
  submitted password + stored verifier -> library verify()
  if correct, create a fresh authenticated session
  rehash after successful login when parameters need upgrading
```

**Salt example:** Two users choose the same password. Different salts cause different stored verifier values. Attackers cannot reuse one precomputed table across all users, though they can still guess each user's weak password.

**Online vs offline attacks:** Rate limiting, MFA, and bot detection help against requests to the live service. They do not slow guesses against a stolen verifier file. Password hashing addresses the offline scenario. A complete design needs both sets of controls.

**MFA** uses different factor categories: knowledge, possession, or inherence. Two passwords are still one category. MFA does not repair broken authorization and some methods remain vulnerable to phishing or approval fatigue. Recovery flows must not become an easier bypass.

**Password reset:** Generate an unpredictable short-lived single-use token, protect its storage, avoid account enumeration, and invalidate it after use. Offer an appropriate session-revocation policy. Sending users their existing password indicates recoverable storage and is a serious design concern.

# 7. Digital signatures and trust

A sender signs data using a private signing key. A verifier checks it using the corresponding trusted public key. In practice, signature schemes include hashing and scheme-specific processing; “encrypting a hash with the private key” is an inaccurate general explanation.

**Example:** A software publisher signs a release artifact. A verifier checks the signature and whether the signer is approved before installation. The signature detects modification after signing and ties the artifact to a key. It does not prove that the original code is free from vulnerabilities or that the build system was uncompromised.

**Non-repudiation:** Signatures can support evidence that a key authorized an action. Real accountability also depends on identity verification, key custody, trusted timestamps, revocation, process controls, and applicable law. Do not promise absolute legal non-repudiation from mathematics alone.

| Question | HMAC | Digital signature |
|---|---|---|
| Who can generate it? | Parties with shared secret | Holder of private signing key |
| Who can verify it? | Parties with shared secret | Anyone with trusted public key |
| Conceals message? | No | No |
| Main management concern | Distributing the shared secret | Protecting private key and trusting public-key binding |

# 8. Certificates and PKI

A public key is not automatically associated with the person or server you intended. A **digital certificate** provides a signed binding between an identity and a public key, subject to its stated scope and validation rules.

**Public Key Infrastructure (PKI)** includes certificate authorities, registration/validation processes, trust stores, issuance, renewal, revocation, and policies. A typical chain is leaf server certificate → intermediate CA → trusted root. The root is trusted because it is already an approved trust anchor, not merely because it signs itself.

For an HTTPS server, a client checks the certificate chain, validity period, requested hostname against the certificate's subject alternative names, permitted usages, and relevant policy constraints. Revocation handling through mechanisms such as CRLs or OCSP depends on the client and deployment.

**Self-signed certificate:** It can participate in encrypted communication, but a client needs an independent way to trust the correct key or configure a trust anchor. Ignoring warnings removes a central identity check.

**Expiry vs revocation:** Expiry is the planned end of validity. Revocation indicates the certificate should no longer be trusted before expiry, for example after key compromise. Renewing a certificate is not the same action as replacing a compromised private key.

**Consulting scenario:** A client has a valid certificate and concludes its website is safe. Explain that the certificate helps authenticate the endpoint and secure transport; it does not assess SQL injection, object access checks, fraud logic, or the business's honesty.

# 9. TLS and HTTPS step by step

**TLS** protects communication against network eavesdropping and tampering and normally authenticates the server. **HTTPS** is HTTP carried over TLS. Ordinary HTTPS does not automatically authenticate the human user to the application.

The following is an intentionally simplified full TLS 1.3 handshake using ephemeral key agreement:

1. The client sends supported settings and a public key share in a ClientHello.
2. The server selects settings and responds with its key share. Both sides derive handshake secrets from the shared key material.
3. The server sends its certificate and a signature proving control of the authentication private key. Much of the remaining handshake is encrypted.
4. The client validates the server's identity and proof. Finished messages authenticate the handshake transcript.
5. Both sides derive directional symmetric traffic keys and use authenticated encryption for application records.

**Three jobs, three tools:** Key agreement establishes shared material; a certificate and signature authenticate the server; symmetric AEAD efficiently protects traffic. Session resumption and pre-shared-key modes have different details. Use [the IETF TLS 1.3 specification](https://www.rfc-editor.org/info/rfc9846/) for protocol detail.

## 9.1 Forward secrecy

If long-term server authentication keys are stolen later, previously recorded sessions established using fresh ephemeral secrets should remain protected, assuming those ephemeral secrets were erased and the protocol was correctly used. Forward secrecy does not protect a session if its live endpoint or session secrets were already compromised. It also does not guarantee that every resumption mode has identical properties.

## 9.2 What HTTPS does and does not cover

HTTPS protects the particular transport connection. The application still sees decrypted data. A TLS-terminating reverse proxy can see traffic; the proxy-to-backend link needs its own protection and trust assessment. Metadata such as destination IP and traffic sizes can remain visible. HTTPS alone does not make phishing content trustworthy or prevent malicious application actions.

**Mutual TLS (mTLS)** adds client-certificate authentication. It is useful for some service-to-service designs, but permissions still require authorization. An authenticated service certificate should not grant unrestricted data access.

**Replay nuance:** TLS 1.3 early data, called 0-RTT, has replay considerations. High-impact state-changing actions should not assume it provides the same replay guarantees as an ordinary completed handshake.

# 10. Key management: where practical failures happen

The key lifecycle includes generation, protected storage, controlled distribution/use, rotation, backup/recovery where appropriate, revocation, and destruction. Who may decrypt is as important as which algorithm encrypts.

**KMS:** A key-management service centralizes key lifecycle and access policy. **HSM:** A hardware security module protects cryptographic keys and operations within a controlled hardware boundary. They overlap in some deployments but are not interchangeable terms.

**Envelope encryption example:** Generate a data-encryption key (DEK) to protect a document. A key-encryption key (KEK) in KMS protects the DEK. Store ciphertext plus wrapped DEK. An authorized application asks KMS to unwrap the DEK when needed. Rotating the KEK may allow rewrapping DEKs rather than decrypting and rewriting every document, depending on the design.

**Controls to discuss:** Separate production and test keys, restrict decrypt permissions, audit unusual decrypt volume, protect backups, avoid source-code secrets, use workload identity where available, and maintain a response plan for compromise. A leaked key must be revoked/rotated; deleting it from the latest Git commit does not remove old exposure.

**Trade-off:** Destroying a decryption key may make data unrecoverable. Rotation and retention must account for old backups, legal retention, business recovery, and migration needs. Do not casually delete keys as an incident response shortcut.

# 11. Data protection and common mistakes

**At rest** means stored data, **in transit** means moving across a connection, and **in use** means data being processed. Full-disk encryption primarily helps with certain lost/stolen-storage threats. It does not stop a logged-in attacker or application with legitimate read access. Database encryption likewise does not replace access checks.

**Tokenization** replaces sensitive values with substitute references; depending on the design, a protected vault or service maps tokens back to originals. **Masking** hides part of a value for display. Neither term automatically specifies encryption or eliminates all privacy obligations.

| Mistake | Why it fails | Better reasoning |
|---|---|---|
| Put decryption key next to encrypted database backup | One compromise obtains both | Separate protection, access policy, and recovery procedure |
| Use a hardcoded IV for every message | Violates mode assumptions | Follow exact mode requirements through vetted APIs |
| Compare a file to an attacker-supplied digest | Both can be replaced | Establish a trusted digest or signed artifact |
| Assume encryption stops SQL injection | Application can already decrypt or query data | Fix query construction and permissions |
| Log passwords or private keys for troubleshooting | Logs become another secret store | Redact secrets and retain useful non-secret context |
| Accept a valid HMAC repeatedly | Authentic old messages still validate | Check timestamps/sequence, deduplicate, process idempotently |

# 12. Post-quantum awareness: an optional extension

A sufficiently capable quantum computer would threaten widely used RSA and elliptic-curve public-key assumptions. This is different from saying present-day attackers can break all encryption. Organizations also consider “harvest now, decrypt later” when intercepted information must remain confidential for many years.

NIST finalized initial post-quantum standards in 2024: ML-KEM for key establishment and ML-DSA and SLH-DSA for signatures. A KEM establishes secret key material; it is not a replacement for bulk symmetric encryption. For an analyst, the practical work is cryptographic inventory, dependency/provider readiness, data-lifetime assessment, and planned migration using supported protocols. See [NIST's standards announcement](https://www.nist.gov/news-events/news/2024/08/nist-releases-first-3-finalized-post-quantum-encryption-standards).

# 13. Worked interview scenarios

## Scenario A: A password database has leaked

**Ask:** What was stored, which users are affected, when did exposure occur, and are the application or signing keys compromised too?

**Reason:** A salted Argon2id verifier is less convenient to attack than plaintext or fast hashes, but weak passwords remain at risk. Offline guessing bypasses the website's rate limits.

**Respond:** Activate the incident process, preserve evidence, contain the source, assess reset and session-revocation requirements, rotate exposed secrets, coordinate communications with responsible teams, and improve storage and monitoring. Do not claim that salts make resetting unnecessary.

**Say aloud:** “I would assess the verifier scheme and scope before judging impact. I would address both the leaked data and the initial access path, and distinguish confirmed compromise from possible password recovery.”

## Scenario B: Encrypted records can be edited without detection

Confidentiality alone is insufficient. Review the complete construction, nonce management, tag verification, and how decrypted results are used. Migrate to supported authenticated encryption or an approved protocol. Check whether tampering has already affected business decisions. A blind replacement of the algorithm name does not validate the design.

## Scenario C: A server private key is exposed

Revoke/replace affected credentials, generate new key material securely, deploy correctly, and investigate exposure duration and use. Assess other copied secrets and affected services. For past traffic, ask whether sessions used forward-secret key agreement or non-forward-secret modes; avoid claiming all historical traffic is either safe or decrypted. Coordinate certificate revocation and client behavior with the relevant owners.

## Scenario D: “SHA-256 is unbreakable, so I used it for passwords”

The problem is efficient guessing, not necessarily breaking SHA-256. An attacker hashes likely passwords and compares results. Explain salts and adaptive password hashing; benchmark parameters and plan upgrades. Add online protections separately.

# 14. Short interview answers to rehearse

**Why combine symmetric and asymmetric cryptography?** Asymmetric mechanisms help authenticate peers and establish key material; symmetric authenticated encryption efficiently protects large amounts of data. Each performs the task it suits.

**Can hashing be reversed?** There is no decryption operation. Attackers may still guess likely inputs and compare digests. That is why ordinary fast hashing is unsuitable for human passwords.

**Does a signature encrypt a message?** No. It supports integrity and origin verification relative to a trusted public key. Use encryption separately when confidentiality is needed.

**Does a certificate stop a man-in-the-middle attack?** Correct certificate and protocol validation helps prevent impersonation. Accepting invalid certificates, trusting an attacker-controlled CA, or compromising endpoints can defeat that assurance.

**What is the difference between a salt and a nonce?** A salt separates password-hashing computations. A nonce distinguishes cryptographic operations under a key. Both are normally public, but their requirements and roles differ.

**What would you audit first in an encryption design?** Data sensitivity and threat model, complete cryptographic construction, key generation/storage/permissions, nonce handling, authentication failure behavior, rotation/recovery, and logging.

# 15. Self-check with answers

1. **A backup and its AES key are exposed together. Is the backup protected?** Assume confidentiality is compromised for data recoverable with that key; strong AES does not help against key theft.
2. **Two users have the same password but different salts. Should the verifier values match?** Normally no, because the salt changes each computation.
3. **A webhook has a valid HMAC but was already processed. What is missing?** Replay tracking and idempotent business handling.
4. **An application uses AES-GCM with a fresh key but ignores an invalid tag. Is it safe?** No. Authentication failures must cause rejection.
5. **A signed document contains false information. Does the signature fix that?** No. It verifies the signed bytes relative to the signing key, not factual truth.
6. **An attacker changes an invoice ID over HTTPS. What blocks access to another user's invoice?** Server-side object authorization; TLS protects transport.
7. **Why not compare RSA and AES only by key length?** Different mathematical problems and attack costs determine security.
8. **If a certificate expires, must the website's data encryption algorithm be broken?** No. Certificate validity and algorithm compromise are separate issues.

# 16. Five-minute recall sheet

| Prompt | Recall |
|---|---|
| Confidentiality + tamper detection | Properly used authenticated encryption |
| Bulk data | AES-GCM or another supported AEAD construction |
| GCM nonce | Unique per key; public does not mean reusable |
| Hash | One-way digest; trusted comparison needed |
| HMAC | Shared-secret integrity/authentication; no confidentiality or automatic freshness |
| Passwords | Adaptive password hashing, fresh salts, suitable parameters |
| Signature | Private key signs; trusted public key verifies |
| Certificate | Identity-to-key binding; validate chain and hostname |
| TLS | Key establishment + authentication + symmetric record protection |
| Forward secrecy | Later long-term-key theft need not expose earlier ephemeral sessions |
| Practical risk | Key theft, bad permissions, nonce misuse, unsafe verification |

**Answer structure:** Define → explain mechanism → give one example → identify a limitation → recommend a control.
