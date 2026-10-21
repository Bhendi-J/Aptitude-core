# EY Senior Analyst cybersecurity study pack

Four separate guides expand sections 5, 6, and 7 of your original notes and add 50 interview questions with answers. Written for someone with computer engineering knowledge, some cryptography exposure, and little cybersecurity experience. Approximately **20,600 words** across the four guides.

| Guide | Word document | Editable Markdown | What you will learn |
|---|---|---|---|
| 5. Cryptography | [Open Word copy](05_Cryptography.docx) | [Read source](05_Cryptography.md) | Encryption, hashes/HMAC, password storage, signatures, PKI, TLS, keys, and compromise scenarios |
| 6. Web Security | [Open Word copy](06_Web_Security.docx) | [Read source](06_Web_Security.md) | HTTP basics, authentication, authorization, major attacks, secure controls, OWASP 2025, and finding reports |
| 7. Security Concepts and Operations | [Open Word copy](07_Security_Concepts_and_Operations.docx) | [Read source](07_Security_Concepts_and_Operations.md) | Risk, SOC/SIEM/EDR, alert triage, incident response, ATT&CK, testing, cloud, governance, and recovery |
| Top 50 Questions | [Open Word copy](08_Top_50_Cybersecurity_Interview_Questions.docx) | [Read source](08_Top_50_Cybersecurity_Interview_Questions.md) | Exactly 50 questions, each with an interview answer, example, and answered follow-up |

The Word copies include clickable contents, heading navigation, tables, and page numbering. The Markdown files are the editable source. Official references appear beside relevant explanations. The material prepares you for a cybersecurity/consulting discussion; it is not an official EY syllabus or a verified EY question bank.

## Recommended order

Start with guide 7, sections 1–5, to learn basic security vocabulary. Then read guide 5, followed by guide 6. Return to guide 7 for investigation and consulting topics. Use the question book throughout: answer first, read the explanation second.

If cryptography already feels comfortable, skim its basic definitions and spend more time on TLS, certificate trust, key handling, and real-world failure scenarios.

## Ten-session learning plan

Allow roughly 90–120 minutes per session, adding time where material is new. This is a starting schedule, not a guarantee of mastery. Split sessions across days as needed.

| Session | Reading | Active practice |
|---|---|---|
| 1 | Guide 7, sections 1–5 | Explain CIA, risk, identity, and the tool ecosystem; Q01–Q06 |
| 2 | Guide 5, sections 1–6 | Compare encryption/hash/HMAC/password hashing; Q11–Q15 |
| 3 | Guide 5, sections 7–16 | Draw TLS and certificate trust; Q16–Q20 |
| 4 | Guide 6, sections 1–5 | Explain one HTTP request, IDOR, SQLi, and XSS; Q22–Q25 |
| 5 | Guide 6, sections 6–10 | Compare CSRF/CORS and sessions/JWT; Q26–Q29 |
| 6 | Guide 6, sections 11–18 | Review upload/SSRF, OWASP, secure development; Q21, Q30–Q35 |
| 7 | Guide 7, sections 6–9 | Investigate the synthetic login timeline; Q36–Q42 |
| 8 | Guide 7, sections 10–14 | Prioritize findings, plan a scoped test, explain recovery; Q43–Q49 |
| 9 | Guide 7, sections 15–17; revisit networking | Explain one finding to a manager; Q07–Q10 and Q50 |
| 10 | Recall sheets and all weak questions | Conduct a mixed mock, then correct answers using the guides |

## Three ways to turn reading into understanding

1. **Explain the normal workflow first.** Describe login, a database query, or an encrypted connection before explaining how it fails.
2. **Use the same example at two levels.** Explain a cross-client invoice leak to an engineer using authorization checks, then to a manager using confidentiality, remediation, and impact.
3. **Keep an error log.** For each weak answer, record the mistaken idea, correct explanation, and a concrete example. Revisit it without looking at the answer.

## Readiness check

You should be able to explain why HTTPS does not fix SQL injection; why a signed JWT is often readable; why a valid HMAC can still be replayed; why an alert is not proof; and why the highest CVSS score is not always the first business priority. These distinctions demonstrate understanding beyond memorized definitions.

Rehearse a truthful introduction connecting your actual computer engineering studies and cryptography knowledge to security work. Use coursework or labs you really completed when discussing experience.
