# Cisco Introduction to Cybersecurity — Detailed Concept Guide

**Audience:** A cybersecurity beginner with normal computer engineering knowledge and some cryptography exposure.

**Purpose:** Understand the concepts, connect them to real situations, and explain them in an EY Senior Analyst interview.

**Companion:** [50 detailed questions and answers](Cisco_Introduction_to_Cybersecurity_Top_50_Questions.md).

**Scope:** An independent study companion mapped to the five modules and topic areas in [Cisco's public course outline](https://www.netacad.com/courses/introduction-to-cybersecurity). Explanations and examples below are original teaching material. Signed-in lessons, activities, and terminology can vary by course edition; this is not a transcript or an official Cisco exam-answer document. Supplementary interview topics are identified where relevant. References checked on 21 October 2026.

## Contents

1. [How to learn from this guide](#how-to-learn-from-this-guide)
2. [Module 1 — Introduction to Cybersecurity](#module-1--introduction-to-cybersecurity)
3. [Module 2 — Attacks, Concepts and Techniques](#module-2--attacks-concepts-and-techniques)
4. [Module 3 — Protecting Your Data and Privacy](#module-3--protecting-your-data-and-privacy)
5. [Module 4 — Protecting the Organization](#module-4--protecting-the-organization)
6. [Module 5 — Will Your Future Be in Cybersecurity?](#module-5--will-your-future-be-in-cybersecurity)
7. [One incident connecting all five modules](#one-incident-connecting-all-five-modules)
8. [Glossary and important distinctions](#glossary-and-important-distinctions)
9. [Practice activities and readiness check](#practice-activities-and-readiness-check)
10. [References and further reading](#references-and-further-reading)

## How to learn from this guide

For every concept, ask: **What needs protection? What could go wrong? How does the control help? What does it leave unresolved?** These questions produce better understanding than memorizing tool names.

For an interview, answer in four steps: define the concept, explain its mechanism, give a concrete example, and connect the impact to a control. Use your real coursework and labs when describing experience.

### Course coverage map

| Public topic area | What this guide explains |
|---|---|
| 1.1–1.2: Digital life and organizational information | Identity, personal data, business assets, CIA, data states, and safeguards |
| 1.3: Stolen information and consequences | Breach analysis, exposure versus confirmed theft, and business impact |
| 1.4–1.5: Adversaries and conflict | Motives, insiders, criminal groups, nation-state activity, and critical infrastructure |
| 2.1–2.2: Attacks and infiltration | Attack chains, malware, deception, credential abuse, interception, and disruption |
| 2.3–2.4: Weaknesses and the wider landscape | Exploits, implementation flaws, zero-days, IoT, cloud, and supply-chain risk |
| 3.1–3.2: Devices and data maintenance | Hardening, Wi-Fi, passwords, MFA, encryption, backups, and disposal |
| 3.3–3.5: Data rights, privacy, and personal behavior | Permissions, tracking, third-party access, safer sharing, and self-assessment |
| 4.1–4.3: Organizational defenses | Security technologies, behavioral detection, response, intelligence, and Cisco's ecosystem |
| 5.1–5.2: Professional practice | Authorization, ethics, disclosure, careers, skills, and honest interview preparation |

## Module 1 — Introduction to Cybersecurity

### 1.1 The world of cybersecurity: what are we protecting?

**Cybersecurity** is the protection of digital systems, information, and services against harmful events and misuse. It includes preventing attacks, detecting suspicious activity, responding, and restoring safe operation. It also includes mistakes and failures: an accidentally public database can expose information without an attacker defeating encryption.

An **asset** is something valuable: a laptop, customer database, employee account, source-code repository, business service, or reputation. Technology exists to support people and organizations, so security decisions should relate to a business outcome.

**Example:** A college portal contains marks, fees, and student information. Security should prevent unauthorized viewing, stop unauthorized mark changes, and keep registration available during enrollment.

**Information security** also includes non-digital information, such as printed records. **Cybersecurity** focuses on digital systems and their interactions. **Privacy** asks whether personal information is collected, shared, retained, and used appropriately. A database can be technically protected while its use remains intrusive or inconsistent with its stated purpose.

#### Online identity, offline identity, and digital footprints

Your offline identity includes your physical-world identity and relationships. Your online identity includes accounts, usernames, profiles, and the attributes services associate with you. Separate usernames can still be linked through reused photos, phone numbers, contact lists, or behavior.

A **digital footprint** is information left through digital activity:

- **Active:** A profile photo, comment, public résumé, or uploaded document.
- **Passive:** Location metadata, device identifiers, browsing records, and service logs generated while you use a system.

Separate professional and personal use where helpful, but do not assume separate accounts create anonymity. A public birthday, employer, and family relationship can help someone craft a convincing impersonation request.

#### Personal information is broader than passwords

Names, addresses, identifiers, health information, financial records, messages, biometrics, and location histories can all be sensitive. A device or advertising identifier can also become personal information when linked to a person.

Data can be combined to reveal more than one field alone. A fitness-route map might reveal a home address and daily routine even without a name. A photo may expose a badge, document, or location in its background.

**Why criminals want it:** Account takeover, identity misuse, payment fraud, targeted extortion, resale, and more convincing social engineering. A stolen email account is particularly valuable because it can control password-reset messages for other services.

### 1.2 Organizational data and security goals

Organizations hold customer records, employee information, intellectual property, credentials, operational information, and transaction histories. Security classification helps establish suitable controls; possible labels include public, internal, confidential, and restricted. The exact labels and requirements are organization-specific.

**Data minimization** means keeping only what is needed for a defined purpose. Information never collected or already properly deleted cannot be leaked from that system later. Minimization also reduces storage, access-review, and recovery complexity.

#### Connected devices and big data

Connected sensors and services generate large amounts of information about activity and environments. **Big data** describes datasets whose scale and processing needs motivate specialized approaches. The familiar three Vs are **volume** (amount), **velocity** (generation/processing rate), and **variety** (different formats/sources). Some models add veracity and value.

The security issue is not only storage size. Aggregating location, device, transaction, and behavior records can reveal sensitive patterns, amplify a breach, and complicate access and retention. Protect pipelines and analysis outputs as well as raw databases. De-identification can reduce risk but does not automatically prevent re-identification when datasets are combined.

#### The CIA triad

| Goal | Plain explanation | Example | Controls |
|---|---|---|---|
| Confidentiality | Only permitted parties can see information | One customer must not read another's invoice | Access checks, encryption, limited sharing |
| Integrity | Information and changes remain trustworthy and authorized | Supplier bank details cannot be silently altered | Approval rules, signatures, transaction constraints |
| Availability | Systems and information are usable when needed | Payment processing remains operational | Redundancy, capacity, protected backups, recovery |

A control can support several goals, but none is a universal solution. Encryption does not stop an authorized application from showing decrypted data to the wrong person. Restoring a ransomware backup does not undo data theft.

**Trade-off example:** Requiring excessive approvals for every low-impact task may make a service unusable. Good security chooses proportionate safeguards based on sensitivity and impact.

#### Data states

**At rest** means stored, such as a disk or cloud object. **In transit** means moving between systems. **In use** means being processed, often in application memory.

Encrypting a laptop disk helps against certain lost-device threats. TLS protects a network connection. Neither automatically prevents a running compromised application from reading plaintext. Assess the complete data path rather than checking a single encryption box.

#### The three-dimensional security model

As a useful foundational extension, the **McCumber Cube** combines security goals, data states, and categories of safeguards. The dimensions are CIA; storage/transmission/processing; and technology/policies and practices/education and awareness. Some teaching material uses slightly different wording.

For each combination, ask a concrete question. To protect confidentiality of transmitted customer data, use a protected channel, define approved sharing procedures, and teach staff to verify recipients. Buying a technical control without an operating process leaves gaps.

### 1.3 What was taken? Understand breaches carefully

A **breach** involves a compromise of information or security under the applicable context. An **incident** is broader and can include attempted compromise or service disruption. A **data leak** may result from accidental exposure. Specific legal definitions vary, so do not infer a statutory conclusion from a casual label.

When evaluating an incident, distinguish:

1. **What was accessible:** The attacker may have had a route to a database.
2. **What was accessed:** Evidence shows particular reads or actions.
3. **What was transferred:** Evidence supports data leaving the controlled environment.
4. **What is unknown:** Logging gaps prevent a complete answer.

**Fictional example:** A learning platform exposes student profile records through an API. Investigators confirm unauthorized downloads of names and email addresses. Payment data is held elsewhere, and there is no evidence it was reached. Do not report that every database was stolen, but do not describe missing logs as proof of safety either.

**Consequences:** Fraud, targeted phishing, operational disruption, response costs, reputational damage, contractual issues, and potentially regulatory obligations. Sensitive information and the circumstances matter more than record count alone.

**Interview habit:** State confirmed facts, plausible risk, evidence gaps, and the next validation step separately.

### 1.4 Cyber attackers: motives, access, and capability

| Actor/context | Common motive | What to understand |
|---|---|---|
| Opportunistic attacker | Easy access or profit | Automated attacks can find weak internet services |
| Organized criminal group | Financial gain | Fraud, extortion, credential theft, and resale |
| Hacktivist | Political or social objective | Defacement, disruption, or disclosure |
| Nation-state-linked activity | Intelligence or strategic advantage | Espionage, influence, or disruptive objectives |
| Insider | Malice, financial gain, coercion, or error | Existing access can increase impact |
| Authorized security researcher | Improve security | Permission, scope, and handling determine legitimate activity |

“Insider” does not automatically mean malicious. A well-intentioned employee can accidentally share a confidential folder. Conversely, a stolen employee account can make an external attacker appear internal.

The labels **white hat**, **black hat**, and **grey hat** are informal descriptions. The important professional distinction is explicit authorization and agreed boundaries. Good intentions do not grant permission to access someone else's system.

### 1.5 Cyberwarfare and critical infrastructure

Digital conflict can involve espionage, disruption, influence, and attacks against services. **Critical infrastructure** includes services whose failure can seriously affect society, such as energy, water, communications, health care, and transport.

**IT** generally supports information processing; **OT**, operational technology, monitors or controls physical processes. An industrial controller may operate equipment, making safety and availability especially important. A patch or scan acceptable on a test website may disrupt a sensitive operational system.

**Example:** Compromising a business email service affects communication. Compromising a control system could affect a physical process as well. Security changes require appropriate operational and safety coordination.

Attribution is difficult. A source IP, language string, or reused technique does not by itself establish which country or organization directed an operation.

**Module 1 checkpoint:** Explain one asset, all three CIA properties, three data states, a plausible threat actor, and the difference between confirmed theft and possible exposure.

## Module 2 — Attacks, Concepts and Techniques

### 2.1 Analyze an attack as a chain

Attacks often combine human, software, and configuration weaknesses. A useful illustrative chain is reconnaissance → initial access → execution → persistence/privilege misuse → movement → collection/exfiltration or disruption. Real attacks can skip, repeat, or reorder these activities.

**Example:** An attacker learns a company's supplier names, sends a convincing payment-themed email, steals a user's session, changes a forwarding rule, and exports records. No single alert describes the entire sequence.

**Attack surface** means the points through which a system can be interacted with or influenced: websites, APIs, remote access, accounts, dependencies, devices, physical interfaces, and people. Reducing unnecessary exposed services and privileges reduces reachable paths.

**Attack vector** means the route used, such as a phishing message or an exposed vulnerable service. **Payload** is the action or content delivered to achieve an effect. The payload can be a malicious file, but account abuse may require no malware file at all.

### 2.2 Methods of infiltration

#### Malware: classify behavior rather than every file as a virus

**Malware** is software intentionally used for harmful or unauthorized activity. The categories below overlap: a program can be both a Trojan and a credential stealer.

| Category | Defining idea | Teaching example |
|---|---|---|
| Virus | Replicates by infecting a host file/program | Malicious code spreads with infected executable files |
| Worm | Can propagate as a standalone program | Compromised hosts automatically exploit reachable vulnerable peers |
| Trojan | Appears useful or legitimate while hiding harmful behavior | Fake study software installs an information stealer |
| Ransomware | Extorts through denying access and/or threatening disclosure | Files are encrypted and stolen records used for pressure |
| Spyware | Secretly collects information | Software captures browsing or account information |
| Keylogger | Records keystrokes or related input | Entered credentials are captured |
| Rootkit | Conceals malicious presence or supports covert privileged control | System activity is hidden through compromised components |
| Backdoor | Bypasses intended access mechanisms | An unauthorized persistent remote-access path |
| Bot | Device/software controlled to perform tasks for an operator | A compromised device receives remote instructions |
| Adware | Displays advertising, sometimes with invasive behavior | Bundled software changes browser behavior without meaningful consent |
| Wiper/logic bomb | Destructive action, sometimes triggered by a condition | Data destruction occurs on a chosen date or event |

See [Cisco's comparison of viruses, worms, Trojans, and bots](https://sec.cloudapps.cisco.com/security/center/resources/virus_differences). The examples above illustrate categories rather than a particular Cisco incident.

**Botnet:** A set of remotely coordinated devices. A botnet can send spam, steal information, or generate distributed denial-of-service traffic. An infected webcam may still work normally while participating.

**Symptoms are clues:** Unexpected processes, changed settings, new accounts, heavy traffic, disabled protections, or encrypted files can prompt investigation. They are not proof by themselves; legitimate updates can also change behavior.

**Fileless/living-off-the-land activity:** Attackers may abuse installed scripting or administration tools instead of introducing an obvious new executable. Endpoint behavior, identity context, and logs matter beyond file scanning.

#### Social engineering

Social engineering manipulates people into taking unsafe actions or disclosing information. Common pressures include urgency, authority, fear, curiosity, reward, and helpfulness. Strong technical knowledge does not make someone immune.

- **Phishing:** Deceptive digital communication used to trigger disclosure or action.
- **Spear phishing:** Tailored to a particular person or organization.
- **Whaling:** Targets senior or high-value individuals.
- **Smishing/vishing:** Deception through text messages/voice calls.
- **Pretexting:** A fabricated situation, such as pretending to be IT support.
- **Baiting:** A lure, such as an attractive download or found storage device.
- **Tailgating:** Following an authorized person into a restricted space.
- **Business email compromise:** Impersonating or taking over business communication to cause fraud or disclosure.

**Example:** A message from “the finance head” urgently changes a supplier's bank details. The correct defense includes independent verification through a known channel and approval separation, not just checking spelling. See [Cisco's social engineering overview](https://www.cisco.com/site/us/en/learn/topics/security/what-is-social-engineering.html).

**Search poisoning and deceptive advertising:** An attacker can manipulate search visibility or advertising to lead users to a malicious imitation/download. A high ranking or sponsored position is not proof of authenticity. **Blended attacks** combine techniques, such as a search lure, fake software, credential theft, and lateral movement; classifications are not mutually exclusive.

#### Password and credential attacks

| Attack | What the attacker does | Important defenses |
|---|---|---|
| Brute force | Tries many candidate secrets | Strong secrets, throttling, MFA |
| Dictionary attack | Tries likely words/patterns | Unpredictable unique passwords, protected verifiers |
| Password spraying | Tries a few likely passwords across many accounts | Cross-account detection, MFA, blocked weak passwords |
| Credential stuffing | Reuses stolen account/password pairs | Unique passwords, MFA, abuse detection |
| Offline guessing | Guesses against stolen password verifiers | Slow salted password hashing and strong passwords |
| Session theft | Reuses authenticated session material | Protect sessions, reduce lifetime, revoke on compromise |

Rate limiting a website does not slow offline guessing after the verifier database has been stolen. MFA at login does not automatically invalidate a stolen active session.

#### Interception, spoofing, and replay

**Eavesdropping** observes communication. **Spoofing** falsifies an identity or source indicator. **Man-in-the-middle** activity interferes between communicating parties. Correctly validated authenticated encryption protocols such as TLS protect against many network interception and tampering scenarios.

**Replay** resends a previously valid message. A signed payment instruction can still be authentic when replayed. Add freshness and duplicate-processing checks, not just signatures. Never treat an IP address or email display name as a sufficient identity guarantee.

#### DoS and DDoS

**Denial of service** makes resources unavailable. **Distributed DoS** uses multiple sources. Attacks can exhaust network capacity, connection state, CPU, memory, application quotas, or expensive backend operations.

A local firewall cannot absorb unlimited upstream traffic. Defenses may include upstream mitigation, resilient capacity, caching, traffic filtering, timeouts, quotas, and application resource limits. Match the defense to the bottleneck.

#### APTs and long-running intrusions

An **advanced persistent threat** describes sustained, capable adversarial activity against objectives. It does not mean every technique is novel. Stolen credentials, familiar tools, and persistence can be more effective than an advanced exploit. Assess observed actions instead of assuming an actor from one indicator.

### 2.3 Vulnerabilities and exploits

**Threat:** Potential harm. **Vulnerability:** A weakness. **Exploit:** A way to take advantage of a weakness. **Risk:** Possibility and consequence of harm given the asset, exposure, and controls.

An exploit may abuse software, configuration, identity, or business rules. A vulnerability scanner produces leads; it does not prove every reported issue is exploitable in the specific environment.

#### Common causes of weaknesses

**Buffer overflow:** A program writes outside an intended memory boundary. Depending on language, platform, and protections, this can corrupt data, crash the process, or contribute to code execution. Bounds checking, memory-safe designs, testing, and platform mitigations help; not every crash is an exploit.

**Race condition:** Correctness depends on uncontrolled timing. Two simultaneous withdrawal requests may both pass a balance check before either changes state. Transactions, atomic operations, locking, and correct invariants address the root cause.

**Insufficient input handling:** The application accepts data outside intended rules or sends it to an interpreter unsafely. Validation checks business format/range; it does not replace parameterization or context-specific output encoding.

**Broken access control:** A user can read another user's records or invoke higher-privilege functions. The server must check the caller, action, resource, and relevant relationship. A hidden interface button is not enforcement.

**Misconfiguration:** Debug mode, public storage, default accounts, excessive permissions, unnecessary services, or exposed administration. Secure code can still be deployed unsafely.

**Hardware and firmware weaknesses:** Security issues can exist below ordinary applications, including device firmware and processor behavior. Remediation may require platform-specific updates and mitigations. A **side channel** leaks information through observable effects such as timing rather than directly returning the secret. Application patching alone does not establish that every platform layer is current.

**Cryptographic weakness:** Outdated or unsuitable algorithms, exposed keys, unsafe nonce reuse, weak password verification, or disabled certificate checks. Choosing “AES” alone does not define a safe system.

#### Web examples: supplementary interview depth

**SQL injection:** Input changes executable SQL structure. Bind values using parameterized queries; map any needed dynamic identifiers through fixed allowed choices. Restricted database privileges reduce impact. Sanitizing apostrophes is not the primary fix.

**Cross-site scripting (XSS):** Untrusted content becomes executable browser script in a trusted site's context. Use safe rendering, context-specific encoding, and appropriate sanitization for intended rich HTML. SQL parameterization does not fix XSS.

**Object authorization flaw:** Changing an invoice ID returns another customer's invoice. Check permissions server-side for every object/action. Random IDs are not access-control rules.

#### Zero-day and known vulnerabilities

A **zero-day** commonly refers to a vulnerability or exploit before defenders have an available effective vendor fix or adequate prior opportunity to address it; usage varies. A published known weakness with a patch is not automatically a zero-day just because one organization has not updated.

Patching addresses supported known weaknesses, but compensating controls and detection matter for unknown or temporarily unpatchable issues. Validate updates safely and verify deployment rather than assuming “update scheduled” means “fixed.”

### 2.4 The wider cybersecurity landscape

**IoT**, Internet of Things, includes connected cameras, sensors, appliances, and other devices. Weak credentials, unsupported firmware, exposed management, and limited visibility can create paths into a network. Keep an inventory, update supported devices, remove default credentials, restrict access, and separate them where useful.

**BYOD**, bring your own device, mixes personal ownership with organizational use. Consider device health, account/data separation, access policy, and privacy. A personal device should not receive unrestricted business access merely because the user is an employee.

**Cloud:** The provider and customer share responsibilities according to service type. A provider can secure its infrastructure while a customer exposes a storage container or grants excessive identity permissions.

**Supply chain:** Dependencies, build systems, updates, and service providers can introduce compromise. Inventory components, control build access, verify trusted artifacts, assess suppliers, and monitor updates. Trusted software can still contain vulnerabilities.

**AI-assisted deception:** Attackers can produce convincing messages, voice impersonation, and content at scale. Treat appearance and fluency as weak identity evidence; strengthen independent verification and approval processes. This is a current-context extension rather than an assertion about a specific lesson.

#### Cryptocurrency, blockchain, and cryptojacking

**Cryptocurrency** uses cryptographic mechanisms and a transaction system to represent/control digital value. A **blockchain** is a ledger structure in which blocks are linked through hashes and participants use a consensus process. This does not mean every cryptocurrency has identical design, every ledger is anonymous, or every blockchain application is secure. Signing keys authorize actions in many designs, so key theft can be devastating even when the underlying mathematics works.

Some networks use **proof of work**, where participants perform computational work as part of consensus; not every cryptocurrency uses this mechanism. **Cryptojacking** uses someone else's device or cloud resources for mining without authorization. It can raise costs, consume capacity, and indicate a broader intrusion. Investigate the process, identity, connections, persistence, and initial access rather than concluding that high CPU alone proves mining. This is unauthorized resource use, distinct from encrypting a victim's files for ransom.

For a real research example, see [Cisco Talos's investigation of an illicit mining campaign](https://blog.talosintelligence.com/cybercriminals-target-graphic-designers-with-gpu-miners/).

**Module 2 checkpoint:** Explain a plausible attack chain and identify where prevention, detection, and recovery controls would interrupt it.

## Module 3 — Protecting Your Data and Privacy

### 3.1 Protecting devices and networks

#### Device hardening

**Hardening** reduces unnecessary capabilities, exposure, and privilege while enabling appropriate protection. It is a maintained state, not a one-time setup.

Keep the operating system, browser, applications, and supported device firmware updated. Obtain software from trusted distribution channels. Remove unused applications and accounts, disable unnecessary services, and use a standard account for daily work where practical. Require a screen lock and protect physical access.

Enable the host firewall and supported anti-malware protections. Full-disk encryption helps if a device or storage is lost, but protect recovery keys and test the recovery procedure. A weakly protected recovery copy can defeat the intended benefit.

Review mobile permissions: a flashlight application should not need your contacts and microphone without a clear reason. Supported hardware/software, secure boot mechanisms, device-finding controls, and remote wipe can help in suitable environments. Wipe depends on connectivity and management design; it is not a guarantee that a thief never accessed data.

**Example:** A student downloads a “free premium” utility and runs it as administrator. The problem includes untrusted software and broad privilege; an excellent Wi-Fi password would not fix either.

#### Home router and Wi-Fi

The router's administrator password and the wireless network password have different roles. Protect both. Change default administration credentials, update firmware, restrict remote management, and remove unnecessary features. Use WPA3 where supported or appropriately configured WPA2 with strong encryption; avoid obsolete WEP or WPA/TKIP configurations.

A guest or IoT network can limit unnecessary access to personal devices. Verify that isolation is actually enforced. A different network name alone does not prove separation. Hiding the SSID or relying on MAC-address filtering is not a replacement for strong wireless authentication and encryption.

**Public Wi-Fi:** Verify the expected network, avoid blindly trusting captive-portal requests, keep sharing disabled, and heed certificate warnings. Correct HTTPS protects web traffic to the authenticated endpoint even on an untrusted network. It does not guarantee the destination site is honest.

**VPN:** Creates a protected tunnel to a VPN endpoint. This can reduce visibility to the local network and provide approved remote access, but shifts some trust to the VPN operator/endpoint. It does not stop phishing, malware, or an application sending information to the wrong party.

#### Passwords, passphrases, and password managers

Use long, unique, unpredictable passwords. Reusing one strong password across sites creates a shared point of failure. A passphrase can help memorability, but predictable quotations or common word patterns remain guessable.

A password manager can generate and store different random passwords, reducing reuse. Protect its main authentication and recovery process; consider MFA and keep recovery material appropriately secure. It is still important to verify the intended domain before approving a login.

Do not assume arbitrary character substitutions make a password strong. Nor should frequent forced changes be treated as a substitute for uniqueness, compromise detection, or secure authentication. Change exposed credentials promptly through a trusted route. [NIST's password discussion](https://pages.nist.gov/800-63-4/sp800-63b/passwords/) explains why usability and resistance to real attacks matter.

#### Authentication, authorization, and MFA

**Authentication** checks identity. **Authorization** checks permitted actions. **Accounting/auditing** records activity. Together they are often called AAA.

**MFA** combines categories such as knowledge, possession, and inherence. A password plus another password is still one category. SMS codes, app codes, push approvals, and cryptographic authenticators have different strengths; not every method resists phishing equally.

**Passkeys** use public-key authentication bound to a relying party, which helps resist common credential-phishing scenarios. The service does not need a shared reusable password for that authentication. Device protection, synchronization/recovery, and account lifecycle still matter. A biometric may unlock an authenticator locally rather than being sent as a password to each site.

**Example:** Strong login authenticates a customer, but the application must separately check which invoices the customer can view. MFA cannot repair a missing ownership check.

### 3.2 Data maintenance: protect the whole lifecycle

The data lifecycle includes collection, use, storage, sharing, retention, backup, and disposal. A sensible lifecycle asks why the data is needed, who can access it, how long it remains, and how recoverability or deletion will be verified.

#### Encryption, hashes, signatures, and certificates

| Mechanism | Purpose | Important limit |
|---|---|---|
| Encryption | Protect readable content from parties without the required key | Exposed keys or authorized application access can reveal plaintext |
| Hash | Produce a content digest | An untrusted digest does not establish origin |
| HMAC | Shared-secret message authentication | Does not encrypt or automatically prevent replay |
| Digital signature | Verify signed bytes relative to a trusted public key | Does not guarantee truthful content or safe software |
| Certificate | Signed identity-to-public-key binding | Does not certify an application is free of vulnerabilities |
| Encoding | Change representation for transport | Not secrecy; Base64 is readily reversible |

**Symmetric encryption** uses a shared secret and efficiently protects bulk data. **Asymmetric cryptography** uses public/private key pairs for operations such as signatures and key agreement. Algorithms are not interchangeable: ECDH establishes shared material; ECDSA signs.

**TLS/HTTPS** combines authenticated key establishment and efficient symmetric record protection. The browser must validate the intended server identity. Protected transport does not fix SQL injection or unsafe browser rendering after the server receives the request.

**Password storage:** A website should use an appropriate slow password-hashing scheme with fresh salts, such as suitably configured Argon2id, rather than plaintext, reversible encryption, or bare fast SHA-256. A salt is public and separates computations; an optional pepper is a separate secret. Live-service rate limits and offline password hashing address different attack scenarios. See [OWASP Password Storage](https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html).

**Key management:** Control who may decrypt, keep secrets out of source code and ordinary logs, plan rotation and compromise response, and protect recovery copies. Strong mathematics cannot rescue an exposed key stored beside the encrypted backup.

#### Backups and recovery

A common starting rule is **3-2-1**: three copies of important data, across two suitable storage types, with one offsite. Treat it as a design aid, not a guarantee. Ransomware resilience also needs an appropriately isolated/offline or immutable copy, restricted administration, suitable retention, and tested restoration.

**Synchronization is not automatically backup.** A sync system may copy malicious deletion or encryption to every device. Versioning helps only if needed versions remain available and attackers cannot delete them.

**RPO**, recovery point objective, is the acceptable data-loss window measured in time. **RTO**, recovery time objective, is the target time to restore service. A 15-minute RPO usually needs more than an isolated nightly copy unless another recovery mechanism exists.

Test a sample restore and, for organizations, the complete service with dependencies and credentials. A successful backup-job message is weaker evidence than an actual recovery exercise.

#### Secure deletion and disposal

Deleting a file usually removes references rather than proving all copies are unrecoverable. Recycle bins, snapshots, cloud retention, backups, and replicas complicate deletion.

Sanitization methods depend on device type and sensitivity. SSD wear-leveling means traditional overwrite assumptions may not hold. Use supported sanitization or cryptographic-erase procedures where appropriate, verify the conditions, and use approved disposal services when needed. Destroying one key only protects data if all usable copies of that key and all relevant plaintext copies are addressed.

**Personal incident response:** From a trusted device/channel, secure affected accounts, review active sessions and recovery settings, revoke suspicious access, preserve useful notifications, and report through the relevant provider or organizational process. Changing one password does not necessarily remove malicious forwarding rules or active tokens.

### 3.3 Who owns your data?

Ownership, control, licensing, and rights are different questions. Uploading content may grant a service permission to process or distribute it under its terms without simply transferring every conceivable ownership right. Actual rights depend on the agreement, the data, and applicable law.

Before sharing sensitive information, examine what the service collects, why, whether it shares data, who can see it, how long it keeps it, and how access/deletion requests work. A privacy notice is an explanation to evaluate, not proof of perfect behavior.

**Example:** A photo application requests access to every picture and a license to process uploads. Ask whether it needs the whole library for your chosen feature, whether location metadata is included, and whether limited access is possible.

**Organizational information:** Information you can view at work is not automatically yours to upload to a personal email account, public AI service, or file-sharing tool. Follow the actual data classification and approved handling process.

### 3.4 Safeguarding online privacy

#### Cookies, tracking, and browser storage

A cookie is data stored by a browser and sent with matching requests under browser rules. Cookies can maintain sessions and preferences, but some mechanisms also support tracking. Other identifiers and fingerprinting can link activity even if cookies are cleared.

**Incognito/private browsing** mainly limits persistent local browsing records in that browser session. It does not make you anonymous to the visited website, an employer-managed endpoint, or every network observer. It also does not remove data you intentionally submit.

Use privacy settings, limit unnecessary tracking/permissions, review extensions, and keep the browser updated. Extensions with broad page access can read or alter sensitive content. The fact that an extension is useful does not make every requested permission necessary.

#### Social sharing and metadata

Check audience settings and document-sharing links. “Anyone with the link” can become effectively public if forwarded. Remove unnecessary metadata when appropriate, but remember that the visible content itself may reveal sensitive details.

Avoid posting material that answers account-recovery questions or exposes family relationships, employee routines, badges, internal systems, or live travel plans. Review third-party integrations and connected applications periodically.

#### Single sign-on, OAuth, and permissions

**SSO** lets an identity system support access to multiple services. It reduces some password friction but makes protecting the identity account particularly important.

**OAuth** supports delegated authorization: a service can grant another application scoped access without giving it the user's password. **OpenID Connect** adds an identity layer. These are not synonyms for JWT, which is a token format.

**Example:** A scheduling application requests permission to read your calendar. Decide whether that scope is necessary and whether ongoing access is justified. A legitimate consent screen can authorize an untrustworthy application; OAuth does not automatically mean a request is safe. Revoke unneeded integrations and protect refresh tokens and sessions. For protocol details, use [IETF OAuth Security Best Current Practice](https://www.rfc-editor.org/rfc/rfc9700.html).

### 3.5 Discover your own risky online behavior

Review a few concrete questions rather than relying on “I am careful”:

- Are my important account passwords unique, and are recovery settings current?
- Is my email account protected with suitable MFA or passkeys?
- Can I restore important coursework or documents from a protected backup?
- Do I know which applications have ongoing access to my accounts?
- Are my router and devices supported and updated?
- Do I verify urgent payment/account requests independently?
- Have I accidentally shared public links, source-code secrets, or personal metadata?

**Module 3 checkpoint:** Describe how you would protect a lost laptop, a stolen password database, a public Wi-Fi connection, and an accidentally shared document. The controls should differ because the threats differ.

## Module 4 — Protecting the Organization

### 4.1 Cybersecurity devices and technologies

#### Defense in depth and access boundaries

**Defense in depth** combines complementary safeguards. For a customer portal: strong identity, object authorization, safe query construction, controlled network access, protected secrets, useful logging, and recovery each address different failure paths.

**Least privilege** limits rights to the task. **Segmentation** limits unnecessary communication between systems. **Network access control** can use identity and device posture to govern network admission or access. A device on an office network is not automatically trusted for every resource.

**Zero trust**, a supplementary architectural concept, avoids granting trust solely from network location. Decisions consider identity, resource, device/context, and policy. It is not a single appliance. See [NIST Zero Trust Architecture](https://csrc.nist.gov/pubs/sp/800/207/final).

#### Know each tool's purpose

| Technology | What it helps with | What it does not establish alone |
|---|---|---|
| Firewall | Enforces traffic policy | Correct application/business authorization |
| WAF | Examines HTTP patterns and applies web rules | Every exploit is blocked or the root cause is fixed |
| IDS | Signals suspicious activity | An alert is confirmed malicious |
| IPS | Can block according to inspection/policy | No business disruption or false positives |
| Anti-malware/EDR | Detects suspicious endpoint files or behavior and supports response | Every endpoint and technique is visible |
| Email/DNS/web filtering | Restricts malicious destinations/content using policy/intelligence | New or compromised trusted destinations are impossible |
| DLP | Applies defined sensitive-data handling controls | All business context or data sensitivity is automatically known |
| VPN | Protects a tunnel and supports approved remote access | The user/device is safe or broadly authorized |
| IAM/PAM | Manages identities and privileged access | Rights remain suitable forever without review |
| SIEM/SOAR | Correlates telemetry and coordinates workflows | Missing evidence or unsafe automation is solved automatically |

The distinction between IDS and IPS depends on deployment and configured behavior, not just the product label. Encrypted traffic also affects what a network sensor can inspect directly.

#### Vulnerability testing and deception

A **port/service scan** identifies reachable services; it does not automatically demonstrate a vulnerability. A **vulnerability assessment** identifies and prioritizes weaknesses. A **penetration test** safely validates defined exploit paths and impact under written authorization.

A **honeypot** is a deliberately monitored decoy intended to attract or reveal suspicious interaction. It must be isolated and managed appropriately; an insecure decoy should not become a route to production. Decoys complement other controls rather than protecting every real system.

### 4.2 A behavioral approach to cybersecurity

#### Signature, reputation, and behavior

**Signature-based detection** recognizes known patterns. **Reputation-based detection** uses information about known or suspicious files, domains, or addresses. **Behavioral detection** looks for actions or relationships that differ from expected or malicious-behavior models.

**Example:** A known malicious file hash is a signature/indicator. A trusted office application spawning an unexpected interpreter and connecting to a new destination is behavioral context. An allowed backup job can also look unusual, so context and validation remain necessary.

**Baselining** describes expected activity, such as normal destinations, hours, transfer volume, and identity use. A baseline must account for role changes, deployments, seasonal workloads, and shared infrastructure. “Different from yesterday” is not equivalent to “malicious.”

#### Telemetry: logs, flows, and packet capture

**Telemetry** is observed operational data. Useful sources include identity logs, operating-system events, endpoint process trees, application audit records, DNS, firewall decisions, and cloud management logs.

**Flow records**, such as NetFlow/IPFIX-style information, summarize who communicated with whom, when, on which ports/protocols, and with what volume. They usually do not contain the full application content. **Packet capture** can provide more detail, but encrypted payloads remain encrypted unless an appropriate lawful/authorized inspection design provides visibility.

**Example:** Flow data shows a server suddenly sending much more data externally. It supports a hypothesis; application and identity logs help determine whether this was an approved export or possible exfiltration. See [Cisco's network traffic analysis explanation](https://www.cisco.com/site/us/en/learn/topics/security/what-is-network-traffic-analysis.html).

Reliable time, source coverage, parser correctness, retention, and access protection determine whether telemetry is usable. Logs should avoid passwords, full tokens, and unnecessary personal information.

#### SOC, SIEM, EDR, and SOAR

A **Security Operations Center (SOC)** is an operational function involving people, processes, and tools. A **Security Information and Event Management (SIEM)** platform collects, searches, correlates, and alerts on events. **Endpoint Detection and Response (EDR)** supplies endpoint visibility and response. **Security Orchestration, Automation and Response (SOAR)** coordinates or automates workflows.

An **event** is observed activity. An **alert** is a detection signal. An **incident** is a situation assessed as requiring incident handling under policy. Do not skip the validation step between alert and conclusion.

#### False positives and false negatives

| Outcome | Meaning |
|---|---|
| True positive | Target malicious/policy-violating behavior correctly detected |
| False positive | Benign behavior incorrectly flagged |
| False negative | Target behavior missed |
| True negative | Benign behavior correctly left unflagged |

**Precision = TP / (TP + FP). Recall = TP / (TP + FN).** A system with very few alerts may have poor visibility, not perfect security. Real-world recall is difficult to measure because unknown attacks are not fully observable.

#### Worked triage example

The following records are fictional. All times use UTC, and the source address is from a documentation range.

```text
09:10 account=analyst1 source=198.51.100.44 result=login_failure
09:11 account=analyst1 source=198.51.100.44 result=login_success device=new
09:14 account=analyst1 action=create_external_forwarding_rule
09:17 account=analyst1 action=bulk_download records=700
```

**Validate:** Are the records correctly parsed and time-aligned? Which device and session performed the actions? What does the download count actually measure?

**Enrich:** Is the user privileged? Were forwarding and export approved? Is the device managed? Did recovery or MFA settings change? What do identity, endpoint, mail, and application records show?

**Assess:** Account compromise is a plausible hypothesis. Successful MFA is not conclusive proof of intent. A new IP or device alone is weaker evidence than the correlated sequence.

**Act:** Follow the response playbook for escalation, session revocation, account restrictions, and preservation of suspicious rule details. Coordinate impact with responsible owners. Document what is confirmed, what is suspected, and what remains unknown.

#### Incident response and improvement

Use a practical sequence of preparation, detection/analysis, containment, eradication, recovery, and improvement. Activities overlap, and evidence handling and communication continue throughout.

Containment limits ongoing harm; eradication removes persistence and the entry path; recovery restores trusted service. Restoring a server without fixing the exploited access path can allow immediate reinfection.

Preserve relevant evidence and an action record. **Chain of custody** records collection, handling, transfers, and access. A hash helps detect changes to collected bytes but does not prove the original system told the truth.

**Framework accuracy:** NIST SP 800-61 Rev. 3 integrates response with CSF 2.0 and supersedes Rev. 2. The practical sequence above should not be presented as an exact current NIST phase diagram. See [NIST's incident response publication](https://csrc.nist.gov/pubs/sp/800/61/r3/final).

### 4.3 Cisco's approach: connect visibility, intelligence, and response

For course revision, a useful organizing idea is protection **before, during, and after** an attack. Treat it as a teaching model, not a claim that incidents happen in clean separate stages.

- **Before:** Understand assets and exposure; harden, limit access, prepare staff, and test recovery.
- **During:** Observe activity, correlate detections, investigate, and interrupt harmful behavior.
- **After:** Determine scope and root cause, restore trusted operations, remove persistence, and improve defenses.

**Threat intelligence** adds context about adversary behaviors, vulnerabilities, infrastructure, and techniques. Indicators such as domains and file hashes help, but shared infrastructure, stale data, and false positives require validation.

**Cisco Talos** is Cisco's threat intelligence and security research organization. Its research and telemetry support security capabilities and response work. Learn the role of intelligence rather than memorizing a marketing catalog. See [Talos's overview](https://www.talosintelligence.com/about) and [its explanation of intelligence supporting Cisco defenses](https://blog.talosintelligence.com/how-cisco-talos-powers-the-solutions-protecting-your-organization/).

A **CSIRT** is a Computer Security Incident Response Team that coordinates incident handling. A **PSIRT** focuses on product security incident response, including vulnerability handling for products. These functions may interact but are not interchangeable with a SOC.

Older lesson editions may mention product names or packaging that change. Translate them into enduring capabilities: network controls, endpoint visibility, identity decisions, email/web/DNS protection, intelligence, and coordinated response.

**Supplementary prioritization:** CVE identifies a vulnerability record; CWE describes a weakness class; CVSS characterizes technical severity. Business priority also depends on exposure, exploitation activity, affected assets, and controls. A lower-scored exposed identity gateway can be more urgent than an isolated higher-scored test system—provided the isolation is verified.

**Module 4 checkpoint:** Given an alert, identify the telemetry you need, a benign explanation to test, a proportionate response, and how recovery will be verified.

## Module 5 — Will Your Future Be in Cybersecurity?

### 5.1 Legal and ethical issues

The professional starting point is **authorization, scope, confidentiality, and accountability**. Access to a network or a public website does not automatically authorize vulnerability testing. Rules of engagement should identify targets, permitted techniques, time windows, contacts, data handling, and stop conditions.

**Example:** A client authorizes a test of a training portal. Finding a link to a different production database does not extend the permission. Stop that path and raise it through the engagement lead.

**Ethical handling:** Collect the minimum necessary evidence; protect client information; do not use discovered credentials outside authorization; avoid unnecessary disruption; and state findings honestly. Curiosity and good intent do not replace permission.

**Responsible/coordinated disclosure:** Use the owner's published reporting process when available, provide enough safe evidence to reproduce the issue, avoid collecting unrelated data, and coordinate further work and publication through the appropriate process. Do not demand a reward or assume a bug-bounty policy covers every asset.

**Laws and agreements:** Relevant obligations may involve unauthorized access, personal information, monitoring, contracts, or sector requirements. Exact obligations and reporting deadlines depend on jurisdiction and facts; verify them through the organization's responsible specialists rather than inventing a universal rule.

**Evidence and privacy:** Security monitoring still needs an approved purpose, appropriate access, retention, and handling. Uploading confidential logs to a public analysis tool can create a second exposure.

### 5.2 Education and careers

| Role area | Main work | Beginner foundation |
|---|---|---|
| SOC/security analyst | Investigate signals, correlate evidence, escalate and respond | Networking, OS logs, identity, structured reasoning |
| Incident responder/forensic analyst | Determine scope, preserve evidence, contain, and recover | Systems, evidence handling, timelines, communication |
| Vulnerability analyst/tester | Identify and validate weaknesses within scope | Web/network fundamentals, safe testing, reporting |
| Security engineer | Build and maintain controls and integrations | Systems, networking, scripting, secure configuration |
| IAM analyst | Manage identity lifecycle, authentication, and access governance | Accounts, permissions, workflows, audit evidence |
| GRC/security consultant | Assess controls and risk and explain improvement options | Business context, evidence, frameworks, clear writing |

Job titles vary between organizations. A Senior Analyst interview may focus on a particular team, so use the job description to prioritize after learning the foundations.

**Your engineering bridge:** OS permissions help explain least privilege; networking helps interpret communication; databases help explain injection and transaction integrity; programming helps trace input-to-execution paths; cryptography helps explain trust and transport protection.

**A practical learning sequence:** Strengthen TCP/IP, DNS, HTTP, Linux/Windows basics, and identity. Then practice reading logs, secure application patterns, and reporting. Use isolated training labs and authorized targets. Keep a small portfolio describing what you observed, your reasoning, limitations, and verification.

Courses and certifications can structure learning, but completion is not production experience or a job guarantee. Check official providers for current prerequisites and exam details before making decisions.

**An honest interview statement:** “My computer engineering background helps me understand systems and networking, and I have studied some cryptography. I am building cybersecurity skills through structured study and practical labs. I can explain the reasoning and will be clear about tools I have not used in production.”

**Module 5 checkpoint:** Explain why permission matters, how you would report a weakness, and which real project demonstrates your ability to learn and communicate.

## One incident connecting all five modules

**Situation:** A fictional college placement portal stores student résumés. An attacker sends a convincing support message, obtains an employee session, exports résumés, and attempts to disrupt access.

| Module | How it applies |
|---|---|
| 1: Assets and goals | Résumés contain personal information; confidentiality and availability are at risk |
| 2: Attack methods | Social engineering provides access; session reuse and excessive permissions expand impact |
| 3: Personal protection | Independent verification, secure sessions, protected recovery, and minimal shared data reduce exposure |
| 4: Organizational defense | Identity/mail/application logs support correlation; permissions limit scope; response revokes sessions and investigates exports |
| 5: Professional conduct | Authorized responders preserve evidence, respect privacy, and report facts through responsible owners |

**Important distinction:** A preventive control failing does not make detection and recovery pointless. Strong controls at later stages can reduce impact. Conversely, restoring access does not establish that stolen personal information is no longer at risk.

## Glossary and important distinctions

| Term | Meaning |
|---|---|
| Asset | Valuable information, system, identity, or service |
| Threat / vulnerability / exploit | Potential harm / weakness / method taking advantage of a weakness |
| Risk / control | Possibility and consequence of harm / safeguard reducing it |
| CIA | Confidentiality, integrity, availability |
| PII | Personally identifiable information; exact legal categories vary |
| IoT / OT / BYOD | Connected things / technology controlling physical processes / personal devices used for work |
| Authentication / authorization | Establish identity / decide permitted action |
| MFA / SSO | Different factor categories / shared identity login across services |
| Malware / ransomware / botnet | Harmful software / extortion-related malicious activity / coordinated controlled devices |
| DoS / DDoS | Denial of service / distributed sources of denial of service |
| IDS / IPS / WAF | Intrusion detection / intrusion prevention / HTTP-focused filtering |
| EDR / SIEM / SOAR | Endpoint visibility and response / event correlation / workflow orchestration |
| SOC / CSIRT / PSIRT | Security operations / incident-response team / product-security response team |
| DLP / IAM / PAM | Sensitive-data handling controls / identity management / privileged-access management |
| Hash / HMAC / signature | Digest / shared-secret authentication / public-key-verifiable signed data |
| TLS / VPN | Secure transport protocol / protected tunnel to a selected endpoint |
| RTO / RPO | Restoration-time target / acceptable data-loss window |
| Indicator / behavior | A clue such as a domain or hash / an observed action or pattern |
| False positive / false negative | Benign activity flagged / target activity missed |
| CVE / CWE / CVSS | Vulnerability identifier / weakness category / technical severity scoring |
| Big data: three Vs | Volume, velocity, and variety of information |
| Blockchain / cryptojacking | Hash-linked ledger with a consensus design / unauthorized use of resources for cryptocurrency mining |

**Do not confuse:** Encryption with encoding; a signature with secrecy; login with permission; an alert with proof; a backup with prevention; private browsing with anonymity; a VPN with a trustworthy website; technical severity with business priority.

## Practice activities and readiness check

These are original revision exercises, not reproductions of Cisco's labs.

1. **Asset map:** Choose a student portal and list its data, users, trust boundaries, and one CIA risk per goal.
2. **Attack chain:** Write a fictional phishing-to-export timeline and place one preventive and one detective control at relevant stages.
3. **Privacy review:** Inspect your own account permissions and sharing settings without publishing screenshots containing secrets.
4. **Recovery exercise:** Restore a harmless test file from your own backup and record what was actually verified.
5. **Log reasoning:** Use the fictional records in module 4 to separate facts, hypotheses, and next evidence.
6. **Client explanation:** Describe an authorization flaw once to an engineer and once to a manager; keep the facts consistent.

### Readiness questions with brief answers

| Question | Expected reasoning |
|---|---|
| Why is a worm different from a virus? | Standalone propagation versus replication through a host file/program |
| Why do unique passwords matter even when strong? | A breach at one service should not unlock another |
| Why does MFA not repair IDOR? | Identity verification does not enforce object permissions |
| Why can a correct signature still permit replay? | Authenticity is separate from freshness and duplicate processing |
| Why are backups insufficient after theft? | Restoration does not reverse disclosure |
| Why investigate an unusual transfer rather than declare exfiltration? | Approved exports/backups and other benign explanations require validation |
| Why does incognito not create anonymity? | It mainly limits local persistent browsing records |
| Why does a scanner result need analysis? | Version, configuration, reachability, and impact determine relevance |
| Why is “the cloud handles security” incomplete? | Customer identities, configuration, data, and applications retain responsibilities |
| Why is written scope essential? | It defines permitted actions and prevents unauthorized impact |

Use the [question companion](Cisco_Introduction_to_Cybersecurity_Top_50_Questions.md) for full answers and follow-ups. A useful revision schedule is one module per session, followed by a mixed mock and review of weak explanations.

## References and further reading

Primary sources support the outline and selected technical details; the examples and learning exercises are original. These references complement the official course rather than replace access to its activities.

- [Cisco Networking Academy course and public outline](https://www.netacad.com/courses/introduction-to-cybersecurity).
- [Cisco malware category comparison](https://sec.cloudapps.cisco.com/security/center/resources/virus_differences).
- [Cisco social engineering](https://www.cisco.com/site/us/en/learn/topics/security/what-is-social-engineering.html) and [phishing](https://www.cisco.com/site/us/en/learn/topics/security/what-is-phishing.html).
- [Cisco network traffic analysis](https://www.cisco.com/site/us/en/learn/topics/security/what-is-network-traffic-analysis.html).
- [Cisco Talos research on illicit cryptocurrency mining](https://blog.talosintelligence.com/cybercriminals-target-graphic-designers-with-gpu-miners/).
- [Cisco Talos overview](https://www.talosintelligence.com/about) and [how Talos supports defenses](https://blog.talosintelligence.com/how-cisco-talos-powers-the-solutions-protecting-your-organization/).
- [NIST password guidance](https://pages.nist.gov/800-63-4/sp800-63b/passwords/) and [authentication guidelines](https://pages.nist.gov/800-63-4/sp800-63b.html).
- [OWASP Password Storage](https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html).
- [IETF OAuth Security Best Current Practice](https://www.rfc-editor.org/rfc/rfc9700.html).
- [NIST Zero Trust Architecture](https://csrc.nist.gov/pubs/sp/800/207/final) and [incident response guidance](https://csrc.nist.gov/pubs/sp/800/61/r3/final).
