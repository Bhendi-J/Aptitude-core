---
title: "Security Concepts and Operations: Think Like an Analyst"
subtitle: "Expanded Section 7 | EY Senior Analyst Interview Preparation"
author: "Personal study guide"
date: "21 October 2026"
lang: en-IN
---

# How to use this guide

This expands section 7 of your original notes. It starts with basic vocabulary and builds toward investigating alerts, prioritizing vulnerabilities, explaining risk, and communicating with a client. You can learn the reasoning without having operated an enterprise SOC.

**What a good answer demonstrates:** You understand the asset and business service, distinguish evidence from assumptions, choose proportionate actions, preserve useful evidence, and explain how success will be verified.

**Priority:** Sections 1–9 are essential. Sections 10–16 add assessment and consulting breadth. The examples are fictional study exercises, not EY incidents or an official interview syllabus.

# 1. Security protects business outcomes

**Information security** protects information in digital and other forms. **Cybersecurity** focuses on risks involving systems, networks, software, and digital operations. A control matters because it reduces a plausible loss: customer-data disclosure, fraudulent payments, service downtime, or loss of trust.

## 1.1 CIA triad

| Goal | Meaning | Example failure | Example controls |
|---|---|---|---|
| Confidentiality | Only permitted parties can access information | Customer records downloaded by another tenant | Access controls, encryption, data minimization |
| Integrity | Information and changes remain accurate and authorized | Attacker changes supplier bank details | Approval rules, signatures, constraints, audit trails |
| Availability | Services/data remain usable when needed | Ransomware stops order processing | Redundancy, backups, capacity, recovery exercises |

An incident often affects several goals. Ransomware may both encrypt files and steal data. Restoring backups addresses availability but does not undo disclosure. Encryption alone does not ensure that an application releases data only to the correct user.

**Authenticity** concerns genuine origin/identity. **Accountability** links actions to responsible identities through controlled access and reliable records. **Privacy** concerns appropriate processing of personal information, including purpose and minimization; technically protecting a database does not automatically make every use of that data appropriate.

# 2. Asset, threat, vulnerability, exploit, risk, and control

Use one example consistently: a public customer portal stores confidential invoices.

| Term | Definition | Portal example |
|---|---|---|
| Asset | Something valuable to the organization | Invoice data and portal service |
| Threat | Potential cause of harm | Unauthorized disclosure or service disruption |
| Threat actor | Entity that may cause harm | External criminal or malicious insider |
| Vulnerability | Weakness that can contribute to harm | Missing invoice ownership check |
| Exploit | Method/action that takes advantage of a weakness | Requesting another customer's invoice identifier |
| Exposure | Circumstances making harm reachable | Endpoint accessible from the internet |
| Impact | Consequences if harm occurs | Confidential billing disclosure and client disruption |
| Risk | Possibility and consequence of harmful outcomes | Cross-client disclosure given exposure and controls |
| Control | Safeguard that changes likelihood or impact | Server-side authorization and detection |

“The hacker is the vulnerability” is incorrect. An attacker is an actor; the missing check is a weakness. Threats also include accidents, outages, and environmental events, not only deliberate attackers.

**Inherent risk** is assessed before the controls under consideration; **residual risk** remains after them. Document which controls are assumed, because different organizations use different baselines. A score is an aid to decisions, not objective physical measurement.

**Illustrative risk matrix:** Likelihood 1–5 and impact 1–5 may be combined into a local prioritization score. This is not a precise probability of loss. Do not multiply ordinal ratings and claim financial accuracy.

**Risk treatments:** Avoid an activity, mitigate with controls, transfer/share some consequences through contracts or insurance, or accept residual risk through the accountable owner. An analyst documents and recommends; risk acceptance belongs to an authorized business decision-maker.

# 3. Controls and identity foundations

## 3.1 Controls by purpose

**Preventive** controls reduce unwanted events, such as MFA or access restrictions. **Detective** controls reveal possible events, such as alert rules and audit reviews. **Corrective/recovery** controls restore safe operation, such as patching and tested restoration. **Compensating** controls reduce risk when a preferred control is temporarily unavailable; they need evidence and an expiry/review plan.

Controls may be **administrative** (policies, training, approvals), **technical** (permissions, segmentation, encryption), or **physical** (building access, protected equipment). A policy alone does not show a control operates effectively.

**Defense in depth** uses complementary controls so one failure is less likely to become a complete compromise. It should be reasoned coverage, not the largest possible tool collection.

## 3.2 IAM, PAM, and least privilege

**Identity and Access Management (IAM)** governs identities, authentication, permissions, and lifecycle. A joiner-mover-leaver process grants needed access, updates it when duties change, and revokes it on departure. Access reviews identify excessive or stale permissions.

**Privileged Access Management (PAM)** adds stronger handling of powerful accounts, such as limited-duration elevation, approval, credential protection, and monitored administrative sessions where appropriate. A shared administrator account makes attribution and revocation harder.

**MFA** uses different factor categories. Permissions still require authorization. **Service/workload identities** also need owners, limited privileges, secret rotation or managed identity, and decommissioning; non-human identities are not exempt from access governance.

**Zero trust** removes automatic trust based only on network location or ownership. Access decisions focus on identity, device/resource context, least privilege, and ongoing evaluation. It is an architectural approach, not a single product or “verify a password on every click.” See [NIST SP 800-207](https://csrc.nist.gov/pubs/sp/800/207/final).

# 4. Networks and systems: connect your engineering knowledge

| Familiar concept | Security relevance |
|---|---|
| Processes and OS permissions | Identify which account/process accessed files or network services |
| TCP/IP and ports | Understand communication paths and what a firewall permits |
| DNS | Investigate name resolution and suspicious destinations |
| Database transactions | Prevent inconsistent balances and race-condition abuse |
| Logs and timestamps | Build a sequence of observed events |
| Segmentation | Limit paths between user devices, servers, and sensitive systems |

**Firewall:** Applies network traffic policy; modern capabilities vary. **WAF:** Inspects HTTP/application patterns and can reduce some web attacks. **IDS:** Detects suspicious activity. **IPS:** Can block traffic according to its placement and policy. **VPN:** Protects a tunnel but does not make every connected device trustworthy.

**DDoS** uses distributed sources to overwhelm resources; it may target network capacity or application work. Upstream capacity/filtering, caching, rate limits, and resilient architecture address different layers. A local firewall cannot absorb unlimited upstream bandwidth exhaustion.

**Common ports to recall:** SSH 22, DNS 53, HTTP 80, HTTPS 443, SMTP 25, SMB 445, RDP 3389. Services can run on nonstandard ports; a port number alone does not establish maliciousness or application identity.

# 5. SOC and the security-tool ecosystem

A **Security Operations Center (SOC)** is the people, process, and technology function that monitors, investigates, and coordinates response. Roles and tier structures vary; do not assume a universal L1/L2/L3 job boundary.

| Capability | Main purpose | Limit |
|---|---|---|
| SIEM | Collect, normalize, search, correlate, and alert on security events | Missing/poor logs create blind spots |
| EDR | Endpoint telemetry, detection, and supported response actions | Coverage depends on agent health, platform, and configuration |
| NDR | Analyze network activity | Encrypted payload visibility and placement affect coverage |
| SOAR | Orchestrate workflows and automate response steps | Unsafe automation can cause outages or erase context |
| XDR | Integrate detections/response across multiple sources | Scope and meaning vary by implementation/vendor |
| Threat intelligence | Context about threats, behaviors, and indicators | Stale indicators and weak relevance can create noise |
| DLP | Detect/control defined sensitive-data movement | Needs accurate data policy and workflow tuning |

**Event → alert → incident:** An event is observed activity. An alert is a rule or analytic's signal for review. An incident is an occurrence assessed as requiring incident handling under policy. An alert is not automatically a confirmed breach.

**Example pipeline:** An identity provider logs a login → the SIEM parses user, device, source, result, and time → a detection identifies an unusual sequence → an analyst enriches and triages → a case is escalated or closed with evidence → response actions are tracked.

Installing a SIEM does not mean all threats are detected. Ensure source coverage, reliable time, parser health, retention, query permissions, alert ownership, and exercised response procedures.

# 6. Alert triage step by step

## 6.1 A repeatable method

1. **Read the signal.** What rule fired, what behavior was observed, and which assets/identities are involved?
2. **Validate the data.** Check timestamp/time zone, ingestion delay, parser fields, duplicates, sensor health, and missing context.
3. **Enrich.** Identify asset criticality, user role, expected behavior, device ownership, network context, and related cases.
4. **Correlate.** Search identity, endpoint, network, application, and cloud evidence around the same period. Expand scope based on actual links.
5. **Assess.** Separate confirmed activity from suspected intent. Estimate current impact, reachable systems, urgency, and confidence.
6. **Act within the playbook.** Escalate and contain proportionately with the relevant operational owners. Record what changed.
7. **Document and hand over.** Include evidence, queries/time ranges, decisions, open questions, ownership, and next update.

**Severity vs priority vs confidence:** Severity describes impact; priority determines response order using urgency and context; confidence describes how strong the interpretation is. High impact with low confidence may still require urgent validation.

## 6.2 Synthetic login example

All times below are UTC and all addresses/accounts are fictional. Documentation IP ranges are used intentionally.

```text
10:02 user=asha src=198.51.100.25 result=fail device=unknown
10:03 user=asha src=198.51.100.25 result=fail device=unknown
10:04 user=asha src=198.51.100.25 result=success device=unknown mfa=success
10:07 user=asha action=create_mail_forward destination=external
10:09 user=asha action=download_bulk_records count=850
```

**What is confirmed?** The logs report a successful login and subsequent forwarding/export actions. First verify source authenticity, timestamps, parser accuracy, and what the count measures.

**What is suspected?** Account compromise followed by data collection/exfiltration preparation. Successful MFA does not prove the human intended the action; stolen sessions, phishing, or malicious approval are possible explanations.

**What to check next?** User confirmation through a trusted channel, device details, prior sessions, MFA/recovery changes, forwarding configuration, export authorization, endpoint events, and additional affected identities. IP geolocation or “impossible travel” alone is inconclusive because VPNs, proxies, and enrichment errors occur.

**Reasonable containment:** Follow the playbook for session/token revocation, suspicious-rule removal after preserving its details, credential changes if needed, and account restriction proportionate to risk. Coordinate with identity/incident owners; a password change alone may not revoke existing tokens.

## 6.3 Detection pseudocode

```text
For each identity in a rolling window:
  count failed logins
  identify later successful login
  enrich with new-device and privileged-account context
  correlate sensitive post-login actions
  create a case with evidence and relevant uncertainty
```

There is no universal “five failures means attack” threshold. Tune against baseline, shared IPs, account type, expected travel, and response capacity. Test both expected detections and benign lookalikes.

# 7. False positives, false negatives, and detection quality

| Outcome | Meaning | Example |
|---|---|---|
| True positive | Malicious/target behavior correctly detected | Confirmed unauthorized export alerted |
| False positive | Benign behavior incorrectly flagged | Approved migration mistaken for data theft |
| False negative | Malicious/target behavior missed | Compromised account export creates no alert |
| True negative | Benign behavior correctly unflagged | Routine permitted activity produces no alert |

Definitions depend on the detection's stated purpose; document it. A rule designed to detect policy violations is assessed against that target, not only malware.

**Precision = TP / (TP + FP). Recall = TP / (TP + FN).** If a rule creates 100 alerts, 20 confirmed malicious and 80 benign, observed precision is 20%. If independent evidence establishes 10 additional missed malicious cases, recall over those known cases is 20/30, about 66.7%. In reality, unknown attacks make complete recall hard to measure.

**Detection engineering loop:** Define behavior → identify telemetry → write logic → test against representative malicious and benign cases → tune → document owner and assumptions → monitor health → revisit when systems or threats change. Reducing alerts to zero is not a success metric by itself.

**IOC vs behavior:** An indicator of compromise can be a known suspicious file hash, domain, or address. A behavioral detection recognizes a pattern, such as unexpected credential access followed by lateral movement. Indicators expire and infrastructure is shared; one match is a lead to investigate.

# 8. Incident response, including evidence and communication

## 8.1 Lifecycle and framework accuracy

A useful practical sequence is prepare → detect/analyze → contain → eradicate → recover → improve. These activities overlap. Preparation and improvement are continuous, not tasks performed once per incident.

NIST SP 800-61 Rev. 3, published in April 2025, superseded Rev. 2 and integrates incident response with CSF 2.0 risk management. Do not label the older phase diagram as the exact current NIST structure. See [NIST SP 800-61 Rev. 3](https://csrc.nist.gov/pubs/sp/800/61/r3/final).

| Activity | Practical work | Question to ask |
|---|---|---|
| Prepare | Contacts, roles, tools, backups, access, exercises | Can responders actually execute the plan? |
| Detect/analyze | Validate, scope, build timeline and hypotheses | What do we know, and what evidence is missing? |
| Contain | Limit spread or further loss | Which action reduces harm without needless disruption? |
| Eradicate | Remove persistence and fix access/root cause | What would let the attacker return? |
| Recover | Restore trusted service and monitor | How do we know the restored environment is safe enough? |
| Improve | Update controls, playbooks, and training | What would shorten or prevent recurrence? |

## 8.2 Evidence and chain of custody

Preserve relevant logs, configuration, files, volatile data where appropriate, and action history. Record who collected what, when, from which system, using which method, and how it was protected/transferred. This is the practical purpose of **chain of custody**.

Hashes help check whether collected bytes changed; they do not prove that the original collection was complete or truthful. Restrict evidence access and follow retention and privacy requirements. Avoid uploading confidential files to public analysis services without an approved handling process.

Evidence preservation and containment must be balanced. Do not delay urgent harm reduction solely to obtain perfect evidence. Conversely, immediately deleting files or powering off every machine can destroy useful state or interrupt critical services. Explain the trade-off and follow the incident lead's decision process.

## 8.3 Ransomware scenario

An endpoint alert is followed by widespread file changes and inaccessible shared folders. Validate the behavior, escalate, and identify affected identities, hosts, shares, and business services. Use authorized isolation to limit spread; protect backups and administrative systems. Preserve available evidence and investigate initial access, persistence, lateral movement, and possible data theft.

Recovery requires trusted restoration, credential/secret remediation, patching the entry path, service-owner validation, and heightened monitoring. Restoring before removing persistence can reinfect systems. Decisions about notification or extortion involve accountable leadership and specialist teams; do not invent universal legal deadlines or promise backups solve all consequences.

## 8.4 A clear status update

“We have confirmed unauthorized forwarding on one account and are investigating whether other accounts are affected. Existing sessions have been revoked under the response playbook. Export logs are being reviewed; data disclosure is not yet confirmed. The incident lead owns the next update at the agreed time.”

This communicates facts, uncertainty, action, and ownership without overstating attribution.

# 9. MITRE ATT&CK, teams, and attack paths

**MITRE ATT&CK** organizes observed adversary behavior. A **tactic** describes an objective; a **technique** describes a way to achieve it; a **sub-technique** adds specificity. It helps organize evidence, detection coverage, and exercises. It is not itself a scanner or a proof of compromise. See [MITRE ATT&CK](https://attack.mitre.org/).

**Example reasoning:** A suspicious email may support a phishing hypothesis. Later identity abuse and a new persistence mechanism require separate evidence. Map each observed behavior carefully; do not infer an entire attack sequence or named threat group from a single matching technique.

A **kill chain** is a higher-level staged attack model. ATT&CK gives a more detailed behavior catalog; real incidents can skip, repeat, or combine stages.

| Team/activity | Main aim |
|---|---|
| Blue team | Prevent, detect, investigate, and respond |
| Red team | Emulate realistic adversary objectives under agreed rules |
| Purple teaming | Collaborate to test and improve defensive coverage |
| Penetration testing | Validate exploitability/impact on a defined scope |
| Threat hunting | Proactively test hypotheses using available evidence |

A red-team exercise often focuses on reaching an objective while testing detection and response. A penetration test often focuses on finding and validating weaknesses in specified targets. Exact engagement scope matters more than the label.

# 10. Vulnerability management and prioritization

The lifecycle is inventory → identify → validate → prioritize → remediate/mitigate → verify → track residual risk. Without reliable ownership and inventory, findings become an unactionable spreadsheet.

**CVE** is an identifier for a published vulnerability record. **CWE** describes a weakness class, such as an injection-related error. **CVSS** characterizes technical severity using a defined scoring system. None automatically tells you the business priority for one client.

CVSS v4.0 includes Base, Threat, Environmental, and Supplemental metric groups; not all groups change the score. State the score version and assumptions rather than treating “9.8” as a universal business verdict. See [FIRST CVSS v4.0 specification](https://www.first.org/cvss/v4.0/specification-document).

**EPSS** estimates the probability of exploitation activity for a CVE over the next 30 days; it does not measure impact or guarantee what happens to a specific host. **CISA KEV** provides evidence of known exploitation for listed vulnerabilities; absence from the catalog is not evidence of safety. See [FIRST EPSS](https://www.first.org/epss/) and [CISA's official KEV data mirror](https://github.com/cisagov/kev-data).

## Worked prioritization

**Finding A:** A technically critical vulnerability on an isolated, unused test service, with restricted access and no sensitive data.

**Finding B:** A lower-scored vulnerability in an internet-facing identity gateway, with credible exploitation activity and a path to privileged access.

B may deserve faster action because reachability, threat activity, and business impact are greater. Validate the assumptions: “isolated” must be verified, and test systems can contain secrets or paths to production. Do not permanently dismiss A.

**If patching must wait:** Evaluate supported configuration changes, service restriction, segmentation, disablement, or other tested mitigations. Agree an owner, residual-risk approval, monitoring, and review/expiry date. Track exceptions until remediation is verified.

**Verification:** Check the installed version/configuration and validate the affected behavior safely. A scanner's disappearance is useful evidence but may also reflect a failed scan or lost access.

# 11. Vulnerability assessment and penetration testing

A **vulnerability assessment** identifies, validates, and prioritizes weaknesses, often combining scanners and manual review. A **penetration test** uses controlled validation to establish exploit paths and business impact within an explicit scope. Neither guarantees all weaknesses have been found.

| Phase | What happens | Output |
|---|---|---|
| Scope and authorization | Targets, methods, time, contacts, limits, data handling | Written rules of engagement and stop conditions |
| Reconnaissance | Permitted target information gathering | Initial attack-surface inventory |
| Discovery | Services, configuration, and likely weaknesses | Candidates requiring validation |
| Analysis | Remove false positives and assess preconditions | Supported findings |
| Controlled exploitation | Minimal safe demonstration of impact | Reproducible bounded evidence |
| Post-exploitation, if allowed | Assess explicitly authorized additional impact | Scoped attack-path description |
| Reporting/remediation | Explain root cause, impact, priority, and fixes | Executive and technical findings |
| Retesting/cleanup | Verify fixes and remove approved test artifacts | Closure evidence and residual issues |

**Black-box** starts with limited internal knowledge, **white-box** with substantial internal information, and **grey-box** with partial access/knowledge. These do not define permission to attack unrelated assets.

**Tool literacy:** An intercepting proxy helps inspect HTTP; packet capture helps analyze traffic; a service scanner discovers exposed services; a vulnerability scanner generates candidates. Explain methodology and limitations before listing product names.

**Reporting template:** Title, affected asset, scope/time, description and root cause, preconditions, evidence, reproduction boundaries, impact, severity rationale, remediation, owner, target date, and retest status. Protect client data and collect the minimum evidence needed.

# 12. Governance, risk, compliance, and audit

**Governance** sets direction, accountability, and oversight. **Risk management** identifies and treats uncertain harmful outcomes. **Compliance** demonstrates adherence to applicable requirements. Compliance supports security but does not prove no compromise can occur.

**NIST CSF 2.0** organizes outcomes into Govern, Identify, Protect, Detect, Respond, and Recover. It helps structure discussion across technical and business teams; it is not a single mandatory tool configuration. See [NIST's Cybersecurity Framework](https://www.nist.gov/cyberframework).

**ISO/IEC 27001** specifies requirements for an information security management system (ISMS). Certification has a defined scope and does not certify that every system is vulnerability-free. See [ISO's standard overview](https://www.iso.org/standard/27001).

**Audit reasoning:** A policy says privileged access is reviewed quarterly. Ask for the complete account population, review criteria, evidence of reviewer decisions, exception follow-up, and samples across the period. One screenshot proves less than repeatable evidence of operation. Distinguish control **design effectiveness** from **operating effectiveness**.

**Policy vs procedure:** A policy states required direction, a standard defines specific requirements, and a procedure describes how work is performed. Terminology varies, but knowing “what must happen” differs from proving it happened.

# 13. Business continuity, recovery, and cloud basics

**Business continuity** keeps critical business activities operating through disruption, possibly through manual workarounds. **Disaster recovery** restores affected technology and data. A **business impact analysis** helps establish priorities and tolerable disruption.

**RTO**, recovery time objective, is the target restoration time. **RPO**, recovery point objective, is the acceptable data-loss window expressed in time. If a service has a 4-hour RTO and 15-minute RPO, the target is restore within four hours with no more than fifteen minutes of data loss. These are objectives, not automatic guarantees.

Backups need tested restoration, appropriate isolation/immutability, access control, and retention. Replication alone can replicate deletion or malicious changes. Successful backup jobs do not prove that complete business workflows can be restored.

**Cloud shared responsibility:** Provider and customer duties differ by service model. For a virtual machine, the customer typically retains guest OS, application, identity, and data responsibilities; managed services shift some tasks but leave important configuration and access duties. “The cloud provider handles security” is incomplete. See [AWS Shared Responsibility Model](https://aws.amazon.com/compliance/shared-responsibility-model/).

**Analyst cloud checks:** Public data exposure, overly broad identities, long-lived access keys, management-plane logging, network restrictions, encryption-key permissions, backup recovery, and asset ownership. An “internal” storage service can still be exposed by a permissive policy.

# 14. Three practical investigation scenarios

## Scenario A: Suspicious Linux process

Identify host criticality and permitted access. Record process owner, parent, command line, executable path, start time, open files, sockets, persistence indicators, and related logs using approved tools. Correlate network connections and user activity. A strange filename is not proof of malware; legitimate administrative tools can also be abused.

If risk warrants containment, follow the incident playbook and coordinate service impact. Preserve relevant evidence before removing artifacts when feasible. Do not delete a binary and conclude the initial access path or persistence is resolved.

## Scenario B: Employee reports a phishing message

Preserve the message/header safely, avoid opening attachments casually, and identify recipients and whether anyone clicked, submitted credentials, approved MFA, or executed content. Check mail, identity, endpoint, and proxy evidence. If credentials or sessions may be exposed, use the identity containment process. Search for related messages and block relevant indicators after validation. Report user impact factually without blaming the reporter.

## Scenario C: Large outbound data transfer

Check destination, data sensitivity, user/service identity, normal baseline, approved transfers, time window, proxy/cloud logs, and endpoint processes. A large transfer may be a backup, deployment, or exfiltration. If urgent credible exposure exists, coordinate proportionate containment and evidence collection. Define which data was confirmed transferred versus merely accessible.

# 15. Consulting communication and interview judgment

**Technical statement:** “An invoice endpoint lacks object-level authorization.”

**Client explanation:** “A customer may be able to access another customer's invoices. We confirmed this with test data. We recommend enforcing ownership and tenant checks on every related action, reviewing historical access, and verifying the fix across download and export routes.”

**When a client disagrees:** Clarify the threat scenario and business assumptions; show minimal reproducible evidence; explain impact and options; revise the rating if evidence changes. Escalate unresolved material risk through agreed governance. The goal is a defensible decision, not winning an argument.

**When you do not know a tool:** “I have not operated that platform in production. I understand its role in centralizing and correlating telemetry. I would start with source coverage, data fields, investigation queries, and the response workflow, then learn the product-specific interface.”

**When asked for experience:** Use your real projects, coursework, or lab observations. State what you personally did and learned. Do not turn these hypothetical scenarios into claims about employment or client work.

**A strong reasoning structure:** Asset/service → confirmed observation → likely explanations → impact and urgency → next evidence → authorized action → verification and communication.

# 16. Practice with solutions

1. **One admin login from a new country. Confirmed compromise?** No. Validate source, device, session, user activity, VPN context, and related actions; prioritize by privilege and evidence.
2. **High CVSS on an unreachable asset vs lower severity on an exposed gateway. Which first?** Compare actual reachability, business impact, exploitation activity, and controls; the gateway may be more urgent.
3. **SIEM has no alerts from a server. Is it safe?** Check telemetry ingestion, sensor health, coverage, rules, and baseline; absence of alerts is not proof.
4. **Password changed but attacker session persists. Why?** Token/session revocation was not completed or propagation differs; verify lifecycle behavior.
5. **Ransomware restored from backups. Incident complete?** Not until persistence, access path, credentials, data theft, validation, and monitoring are addressed.
6. **A penetration test finds an adjacent production database outside scope. What next?** Stop that path, preserve permitted minimal evidence, and ask the engagement lead to resolve scope before continuing.
7. **Client accepts the risk verbally. Enough?** Record the risk, assumptions, owner, approval under the client's process, compensating controls, and review date.
8. **A log file's hash matches. Does that prove its events are truthful?** No; it supports unchanged bytes after the reference point, not source truth/completeness.
9. **RPO 30 minutes, nightly backups only. Concern?** Backup frequency alone appears insufficient unless another recovery mechanism provides the required point; verify restoration evidence.
10. **A detection's false positives fall after disabling half the logs. Improvement?** Not established; coverage and missed detections may have worsened.

# 17. Five-minute recall sheet

| Prompt | Recall |
|---|---|
| CIA | Who can see, what can change, whether service works |
| Risk | Asset + threat/exposure + weakness + likelihood/impact + controls |
| SOC/SIEM/EDR | Operational function / event correlation / endpoint visibility and response |
| Triage | Validate → enrich → correlate → assess → act → document |
| Incident response | Prepare, analyze, contain, eradicate, recover, improve; activities overlap |
| ATT&CK | Tactic is objective, technique is method; map evidence |
| CVSS/EPSS/KEV | Technical severity / exploitation estimate / known exploitation evidence |
| Testing | Authorization and scope before activity; validate and retest |
| Recovery | RTO is time to restore; RPO is acceptable data-loss window |
| Consulting | Explain business effect, options, owner, and evidence |

**Final speaking drill:** Explain one alert, one vulnerability, and one business risk in sixty seconds each. Include what you know, what is uncertain, and the next useful action.
