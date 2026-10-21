---
title: "Web Security: Understand the Request, the Attack, and the Fix"
subtitle: "Expanded Section 6 | EY Senior Analyst Interview Preparation"
author: "Personal study guide"
date: "21 October 2026"
lang: en-IN
---

# How to use this guide

This expands section 6 of your original notes for a computer engineering student entering cybersecurity. You do not need web development expertise first. Understand the normal request flow, then study each weakness as **root cause → attack effect → prevention → verification**.

The running example is a fictional client portal where customers log in, view invoices, upload documents, and change account details. All examples are educational; practice with your own application or an explicitly authorized training environment.

**Priority:** Learn sections 1–11 first. Sections 12–18 broaden your coverage and consulting reasoning. These topics are useful preparation, not a claim about EY's exact interview questions.

# 1. What happens when you use a website?

The **browser** is the client. A **server** receives requests and returns responses. A **backend application** implements business rules and often talks to a database. An **API** is an interface through which another program or the browser requests data or actions.

```text
Browser -> HTTPS -> reverse proxy/web server -> application/API -> database
```

Each arrow crosses a potential **trust boundary**: a point where data or authority comes from another security context. Data is not trustworthy just because it arrived from the visible user interface. Users can modify requests using developer tools or an HTTP client.

## 1.1 Anatomy of a request

```http
GET /api/invoices/101 HTTP/1.1
Host: portal.example
Cookie: session=opaque_random_value
Accept: application/json
```

`GET` is the method; `/api/invoices/101` is the path; headers carry metadata. The cookie may identify an authenticated session. The server must determine which user it represents and whether that user may read invoice 101.

A response includes a status code, headers, and optional body. `200` usually means success, `400` a malformed request, `401` missing/invalid authentication, `403` refusal based on permissions, `404` not found, and `500` an internal error. Applications may deliberately use `404` to avoid revealing whether another user's resource exists. The response body matters too: an error code with sensitive data still leaks data.

**GET** should retrieve data without requested state changes. **POST** commonly creates or triggers actions. **PUT/PATCH** update; **DELETE** removes. The method is not a permission system: every sensitive operation still needs authorization.

## 1.2 Stateless HTTP and sessions

HTTP does not automatically remember a previous request's login. Applications attach an identifier or credential to subsequent requests. A common browser design uses a cookie containing an opaque random session ID; server-side state maps it to a user.

Cookies are often sent automatically when browser rules permit. A bearer token in an `Authorization` header is another pattern; anyone who possesses a valid bearer token may be able to act as its subject. Token storage and lifecycle therefore matter.

**First security principle:** Check authentication, authorization, input handling, and action intent on the server. Disabling a button or hiding an admin menu is only interface behavior.

# 2. Authentication, authorization, and access control

**Authentication:** Who are you? **Authorization:** What may you do to this resource? **Accounting/auditing:** What did you do? Together these explain the familiar AAA model.

| Model/principle | Meaning | Portal example |
|---|---|---|
| RBAC | Permissions assigned through roles | Support analyst can view assigned cases |
| ABAC | Decision considers user, resource, action, environment attributes | Finance staff may approve low-value invoices for their region |
| Ownership/relationship checks | Access depends on relation to a resource | Customer may view only their organization's invoice |
| Least privilege | Only necessary access | Upload service cannot administer identities |
| Deny by default | No access unless explicitly permitted | New API endpoint has no implicit public access |
| Separation of duties | Different people approve sensitive steps | Payment creator cannot approve their own transfer |

Checks belong at every sensitive read and write, including downloads, exports, background jobs, and internal service calls. An internal API can still be reached through a compromised service or misconfigured gateway. See [OWASP Authorization guidance](https://cheatsheetseries.owasp.org/cheatsheets/Authorization_Cheat_Sheet.html).

**Horizontal escalation** means accessing another user's resources at a similar privilege level. **Vertical escalation** means gaining higher-level functions, such as an ordinary user invoking an administrator action.

# 3. IDOR and BOLA: a complete worked example

**IDOR** means Insecure Direct Object Reference. **BOLA** means Broken Object Level Authorization. Changing an identifier is normal application behavior; the vulnerability is failure to verify access to the referenced object.

An unsafe handler effectively does this:

```python
# Educational pseudocode: login alone is insufficient.
require_login(request)
invoice = database.get_invoice(request.path.invoice_id)
return invoice
```

Customer A changes invoice 101 to 102 and receives Customer B's invoice. The server authenticated A but did not authorize A's access to invoice 102.

A safer pattern retrieves within the caller's permitted scope and checks the requested action:

```python
# Educational pseudocode; real policy may be more complex.
user = require_authenticated_user(request)
invoice = database.find_invoice(
    invoice_id=request.path.invoice_id,
    tenant_id=user.trusted_tenant_id
)
if invoice is None or not policy.allows(user, "read", invoice):
    return access_denied_without_sensitive_data()
return permitted_invoice_fields(invoice)
```

The tenant identity must come from trusted authenticated context, not a client-controlled `tenant_id` parameter. Multi-tenant applications also need field-level and function-level policies.

**Fix:** Enforce object/action/tenant authorization consistently. Random UUIDs reduce easy guessing but do not create permission. A WAF cannot reliably infer every customer relationship.

**Verify in a lab:** Create two test users with separate invoices. Confirm each can read their own invoice but cannot read, edit, download, or delete the other's. Add a role-restricted test and a cross-tenant test. Do not validate by collecting real customers' records.

**Business explanation:** “A missing ownership check may expose confidential billing information across clients. I would establish the scope with minimal evidence, fix the server-side policy, test all related paths, and review logs for prior misuse.”

# 4. SQL injection: data becomes query syntax

An SQL injection vulnerability occurs when untrusted values are incorporated into executable SQL structure. The database cannot tell which parts were intended by the developer and which came from the user.

```python
# Vulnerable teaching example.
query = "SELECT id FROM users WHERE email = '" + email + "'"
cursor.execute(query)

# SQLite-style safe value binding; driver syntax varies.
cursor.execute("SELECT id FROM users WHERE email = ?", (email,))
```

In the safe pattern, query structure and values are passed separately. Quotes in an email remain part of a value instead of changing SQL meaning. Parameterization is stronger than trying to remove suspicious characters: apostrophes are legitimate input too.

**Variants:** In-band injection returns effects through the same response, error-based injection uses informative database errors, and blind techniques infer behavior from differences rather than returned records. Interview depth means understanding the execution boundary, not memorizing payloads.

**Important limit:** Placeholders generally bind values, not table names, column names, or sort directions. Map requested sort options to a fixed allowlist. Stored procedures and ORMs can still be vulnerable if they build unsafe dynamic SQL.

**Defense:** Bound parameters, allowlisted dynamic identifiers, restricted database accounts, safe error handling, and regression tests. A WAF can be a supplementary control but does not correct unsafe query construction. See [OWASP SQL Injection Prevention](https://cheatsheetseries.owasp.org/cheatsheets/SQL_Injection_Prevention_Cheat_Sheet.html).

**Impact:** Disclosure, unauthorized changes, and potentially broader compromise depending on database capabilities and privileges. Never claim every SQL injection automatically results in complete server control.

# 5. Cross-site scripting: unsafe content becomes browser code

**XSS** occurs when attacker-influenced content executes as script in a trusted site's browser context. That script may act as the user, read accessible page data, modify the interface, or issue same-origin requests.

| Form | Where the dangerous data comes from | Example |
|---|---|---|
| Stored | Saved content later displayed | Unsafe support comment executes when staff view it |
| Reflected | Request data included in the immediate response | Search term rendered as executable markup |
| DOM-based | Client-side script uses data in an unsafe browser sink | Fragment value assigned to `innerHTML` |

These descriptions can overlap: stored data can reach a DOM-based sink. They describe different aspects of the data flow.

```javascript
// Unsafe if userComment is untrusted and HTML is not intended.
container.innerHTML = userComment;

// Appropriate when the feature should display plain text.
container.textContent = userComment;
```

**Output encoding** makes data safe for its destination context. HTML text, HTML attributes, JavaScript, CSS, and URLs have different rules. An HTML encoder does not make arbitrary JavaScript interpolation safe. Framework auto-escaping is valuable, but unsafe rendering escape hatches can undo it.

**Sanitization** is appropriate when a feature intentionally permits limited rich HTML. A maintained sanitizer removes dangerous constructs while preserving allowed formatting. Ad hoc regular expressions are not a reliable HTML security parser.

**CSP**, Content Security Policy, restricts permitted scripts and resources. A carefully deployed policy reduces some exploitation opportunities, but it is defense in depth. `HttpOnly` prevents ordinary script access to a cookie; XSS may still submit authenticated actions without reading that cookie. See [OWASP XSS Prevention](https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html).

**Interview distinction:** SQLi crosses into a database query interpreter. XSS crosses into a browser execution context. Both involve mishandled trust, but their primary defenses differ.

# 6. CSRF: the browser performs an unwanted action

**Cross-Site Request Forgery** abuses a browser's existing authenticated relationship with another site. An attacker-controlled page causes a state-changing request to a target where the victim is logged in; relevant cookies may be included automatically.

**Portal example:** A malicious page causes the browser to submit a change-email action. If the portal relies only on the session cookie, it may treat the request as intentional.

An anti-CSRF token is an unpredictable value tied to the appropriate user/session or otherwise securely validated. The legitimate form or application includes it, and the server checks it on state-changing requests. The attacker should not be able to obtain or manufacture the required token through a cross-site page.

**Controls:** Use the framework's recommended token mechanism, appropriate `SameSite` cookies, validated Origin/Referer or fetch metadata checks where suitable, and reauthentication/confirmation for especially sensitive operations. Do not use GET for state changes. Origin checks need exact parsing and a plan for missing values.

**Key nuance:** Cross-origin response reading and cross-origin request sending are different. A browser can block JavaScript from reading a response after an unwanted request has already changed state. Blocking CORS reads alone does not prove CSRF is prevented.

CSRF exposure depends on how authentication is attached. A token that hostile sites cannot obtain and must explicitly set in a protected header changes the attack assumptions; it does not eliminate token theft, XSS, or every login-flow issue. See [OWASP CSRF Prevention](https://cheatsheetseries.owasp.org/cheatsheets/Cross-Site_Request_Forgery_Prevention_Cheat_Sheet.html).

| Question | XSS | CSRF |
|---|---|---|
| Attacker's mechanism | Execute script in the target site's context | Induce an unwanted request from another context |
| Needs an injection flaw at target? | Usually an unsafe content/script flow | Not necessarily |
| Main controls | Safe rendering, encoding/sanitization, CSP | Action-intent validation, token/origin controls, cookie policy |

# 7. Login, account recovery, and session security

## 7.1 Common authentication attacks

**Brute force** tries many guesses. **Password spraying** tries a small number of likely passwords across many accounts. **Credential stuffing** tests previously stolen username/password pairs. They call for monitoring across both account and source dimensions; locking an account after a few attempts can itself enable denial of service.

Defenses include appropriate rate limits, breached-password screening, secure password hashing, phishing-resistant MFA where appropriate, anomaly monitoring, and secure recovery. Avoid overly informative differences that reveal whether an account exists.

**Recovery flow:** Use unpredictable, short-lived, single-use reset tokens, prevent account enumeration, avoid leaking tokens in logs or referrers, and consider session revocation after resets. MFA recovery should not become a weak alternate login.

## 7.2 Sessions and cookies

An opaque session identifier should be unpredictable. Rotate it after login and privilege changes to reduce **session fixation**, where an attacker tries to make a victim use an attacker-known identifier. Use idle and absolute timeouts appropriate to the application and invalidate server-side state on logout.

| Cookie attribute | Useful behavior | Limitation |
|---|---|---|
| `Secure` | Cookie is sent over secure transport, subject to browser details | Does not protect a compromised endpoint |
| `HttpOnly` | Ordinary JavaScript cannot read the cookie | Browser can still send it; XSS can still act |
| `SameSite=Strict` | Restricts cross-site inclusion strongly | Can disrupt legitimate navigation/login flows |
| `SameSite=Lax` | Permits some top-level navigation cases | Not a universal CSRF solution |
| `SameSite=None; Secure` | Allows cross-site usage when browser rules permit | Requires deliberate CSRF and privacy design |
| `Domain` / `Path` | Control matching scope | Do not treat them as complete isolation boundaries |

Cookies should have the narrowest useful scope. `__Host-` cookie prefixes can enforce useful browser constraints where supported. Avoid putting session secrets in URLs because history, logs, and referrers may expose them. See [OWASP Session Management](https://cheatsheetseries.owasp.org/cheatsheets/Session_Management_Cheat_Sheet.html).

## 7.3 Where should tokens be stored?

There is no single location that removes all threats. Browser local storage is readable by same-origin JavaScript and therefore exposed to XSS. An HttpOnly cookie reduces direct script reading but introduces automatic sending considerations and needs CSRF defenses. Evaluate the full browser/application architecture, token lifetime, refresh flow, and revocation needs.

# 8. JWT, OAuth, and OpenID Connect

A **JSON Web Token (JWT)** carries claims. A common signed compact JWT has three Base64URL-encoded sections: `header.payload.signature`. The payload is readable. Encrypted JWT constructions also exist and have a different format.

Typical claims include `sub` (subject), `iss` (issuer), `aud` (audience), `exp` (expiry), and `nbf` (not before). A role claim is trustworthy only after the token is properly verified and still relevant to the requested authorization decision.

**Verifier responsibilities:** Configure approved algorithms and trusted keys independently of untrusted token choices; verify cryptographic protection; validate expected issuer, audience, times, and token type; reject malformed or inappropriate tokens. A key identifier is a selection hint, not permission to fetch an arbitrary attacker-supplied key. See [IETF JWT Best Current Practices](https://www.rfc-editor.org/rfc/rfc8725.html).

**Session vs JWT:** A server-side session uses an identifier and central state. A self-contained JWT can be checked locally, helping distributed services, but immediate revocation often requires state, version checks, or short lifetimes. Logout in the browser alone does not invalidate every copied token.

**Access token** grants access to a resource server. **Refresh token** obtains new access tokens and deserves strong storage, rotation/reuse detection where supported, and revocation controls. Do not place sensitive data in a readable token just because it is signed.

**OAuth 2.0** is a framework for delegated authorization: an application obtains constrained access rather than asking for another service's password. **OpenID Connect (OIDC)** adds an identity layer. A JWT is a token format; OAuth and OIDC are protocols/frameworks using defined flows. They are not interchangeable names.

For a beginner interview, explain the roles: user/resource owner, client application, authorization server, and resource server/API. An authorization-code flow with PKCE helps bind a code exchange to the initiating client. Redirect validation, state, nonce where applicable, token validation, and library configuration are implementation responsibilities; avoid inventing a login protocol. See [IETF OAuth 2.0 Security Best Current Practice](https://www.rfc-editor.org/rfc/rfc9700.html).

# 9. Same-origin policy and CORS

An **origin** is scheme + host + port. `https://portal.example` and `https://api.portal.example` are different origins. Same-site cookie reasoning uses a different definition, so “same-site” and “same-origin” are not synonyms.

The **same-origin policy** restricts many ways one site's scripts access another origin's resources. **CORS** lets a server grant selected browser cross-origin read access. It is enforced by browsers, not by every HTTP client, and it does not authenticate users.

**Trusted example:** A portal frontend at one origin needs to read its API at another. The API permits that exact origin and still authenticates/authorizes each request.

**Dangerous example:** An API reflects any supplied Origin and permits credentials. If the browser's credential and cookie rules also allow the request, a hostile website may read sensitive user-specific responses. This impact must be demonstrated under the actual browser conditions.

**Preflight:** For certain methods/headers, the browser sends an OPTIONS request to check whether the actual request is allowed. Many ordinary/simple requests do not require this preflight. An endpoint should not use preflight as its authorization mechanism.

Use exact trusted-origin allowlists, minimal credential sharing, and `Vary: Origin` when choosing the allow-origin value dynamically. `Access-Control-Allow-Origin: *` cannot authorize credentialed browser response exposure. See [MDN CORS documentation](https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/CORS).

# 10. Security headers and clickjacking

Headers instruct the browser; they do not repair missing business rules.

| Header/control | What it contributes | Interview caveat |
|---|---|---|
| Content-Security-Policy | Restricts script/resource behavior; `frame-ancestors` restricts embedding | Weak allowlists and unsafe directives reduce value |
| Strict-Transport-Security | Remembers HTTPS-only access for a host for a period | Initial contact and preload/subdomain choices need planning |
| X-Content-Type-Options: nosniff | Restricts certain MIME-sniffing behavior | Send correct content types too |
| Referrer-Policy | Limits outgoing referrer information | Avoid secrets in URLs regardless |
| Permissions-Policy | Restricts selected browser capabilities | Does not replace browser/user permissions |
| Cache-Control: no-store | Helps keep sensitive responses out of compliant caches | Does not erase data already leaked or captured |
| X-Frame-Options | Older framing defense where needed | CSP frame-ancestors offers more flexible policy |

**Clickjacking:** A victim interacts with a genuine page hidden or disguised inside an attacker-controlled frame. Limit framing and require clear confirmation for sensitive actions. This differs from executing injected script. See [MDN's CSP reference](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers/Content-Security-Policy) for directive behavior.

**HSTS deployment:** Ensure HTTPS works correctly before applying long durations and broad subdomain scope. Operational availability matters; a security configuration can break legitimate services if deployed blindly.

# 11. SSRF, command injection, and file handling

## 11.1 Server-Side Request Forgery

**SSRF** makes the server send requests to unintended destinations. In the portal, an “import document from URL” feature may fetch a user-supplied URL. The server may reach internal systems that the external user cannot access directly.

**Impact:** Internal data access, interaction with internal services, or cloud credential exposure depending on the environment. Server credentials and egress rights can enlarge the effect.

**Fix:** Prefer a small destination allowlist when business requirements permit. Parse URLs consistently, restrict schemes and ports, validate resolved destinations, address redirects and DNS changes, apply egress controls, and protect metadata access and workload permissions. Rejecting only the string `localhost` misses many address forms and resolution paths. See [OWASP SSRF Prevention](https://cheatsheetseries.owasp.org/cheatsheets/Server_Side_Request_Forgery_Prevention_Cheat_Sheet.html).

**SSRF vs CSRF:** SSRF abuses the server's network position. CSRF abuses a user's authenticated browser.

## 11.2 OS command injection

If an application constructs a shell command using untrusted input, the input may become command syntax. Prefer a dedicated library/API instead of a shell. Where a process is necessary, use a fixed executable and explicit argument handling with validation. Even without a shell, argument injection or unsafe program options may remain possible.

**Example:** A file-conversion feature should call an approved converter using controlled inputs in an isolated environment, rather than append uploaded filenames to a shell command. Run with limited privileges and resource limits.

## 11.3 Path traversal and file upload

**Path traversal** occurs when attacker-controlled path components escape an intended directory or resource scope. Map server-generated IDs to files, constrain canonical resolved paths, account for symlinks and decoding, and enforce access permissions. Removing a single suspicious substring is not a complete solution.

**Upload design:** Allow only required formats; validate extension and actual content where possible; generate storage names; limit size, count, and decompression; store outside executable application paths; authorize reads; and scan or transform content where appropriate. The client-provided MIME type is not trustworthy proof.

A valid image can still contain sensitive metadata or create unsafe processing conditions. Archive extraction needs directory-boundary and resource-limit checks. Download authorization matters even if upload validation is correct. See [OWASP File Upload guidance](https://cheatsheetseries.owasp.org/cheatsheets/File_Upload_Cheat_Sheet.html).

# 12. More attack patterns worth recognizing

**Insecure deserialization:** Untrusted serialized data causes unsafe object creation, code paths, or business-state manipulation. Prefer simple data formats, validate schemas and allowed types, and avoid unsafe object deserialization. Signing data does not make an unsafe parser safe if trusted producers or keys can be compromised.

**XXE:** Unsafe XML external-entity processing may expose files or induce requests. Disable unneeded external entities/DTD processing with a securely configured parser; evaluate the actual parser behavior.

**Open redirect:** An attacker-controlled destination sends users away through a trusted site's link. It can aid phishing or undermine authentication flows. Use fixed/allowlisted destinations and robust URL parsing.

**Business logic flaws:** Valid requests break intended business rules, such as applying a coupon repeatedly or approving one's own payment. Generic input filtering will not understand the missing rule. Define abuse cases and enforce limits and invariants server-side.

**Race conditions:** Two requests concurrently pass the same check before either updates state, such as spending the same balance twice. Use transactions, atomic updates, locking where appropriate, uniqueness rules, and idempotency mechanisms. A rate limit is not a correctness guarantee.

**Denial of service:** Excessive work, data size, concurrency, or dependencies can exhaust resources. Add quotas, timeouts, backpressure, isolation, and suitable upstream protection. Distinguish high-volume network attacks from expensive application requests.

**Mass assignment:** Automatically binding all submitted fields to an internal object lets a caller modify unintended properties such as role or approval state. Explicitly allow writable fields and enforce authorization separately.

**API risks:** Object/function/property authorization, inventory of old versions, excessive data exposure, resource consumption, and unsafe third-party API trust. Browser controls do not protect an API from direct requests.

# 13. OWASP Top 10:2025, without memorizing only numbers

OWASP's Top 10 is an awareness document covering broad risk categories. It is not a complete assessment checklist. These are the official 2025 category names; numbering differs from older editions. See the [official OWASP Top 10:2025](https://top10.owasp.org/2025/).

| Category | A concrete way to remember it |
|---|---|
| A01 Broken Access Control | Wrong person can read or change a resource |
| A02 Security Misconfiguration | Unsafe deployment settings expose capabilities |
| A03 Software Supply Chain Failures | Dependencies or delivery systems introduce compromise |
| A04 Cryptographic Failures | Sensitive information is inadequately protected |
| A05 Injection | Input crosses into executable syntax |
| A06 Insecure Design | A necessary security rule was never designed |
| A07 Authentication Failures | Identity or session assurance breaks |
| A08 Software or Data Integrity Failures | Untrusted changes are accepted |
| A09 Security Logging and Alerting Failures | Important activity is unseen or unactionable |
| A10 Mishandling of Exceptional Conditions | Unexpected states lead to unsafe behavior |

**2021 comparison:** Injection was A03; SSRF had its own A10 category. Always name the edition. Do not assume absence of a standalone category means a vulnerability stopped mattering.

**Design vs implementation example:** An implementation bug skips an existing approval rule on one endpoint. An insecure design never required independent approval for high-value payments at all. Correct code can faithfully implement an insecure design.

**Supply-chain vs integrity example:** A compromised dependency enters through the build process; a separate integrity issue could be accepting an unsigned or incorrectly verified update. Categories overlap; explain the actual causal path rather than forcing a unique label.

# 14. Secure development and practical review

**Threat modeling** asks what you are building, what can go wrong, which controls address it, and how you will verify them. Start with assets, data flows, actors, and trust boundaries. For the portal, examine account takeover, cross-tenant access, invoice alteration, malicious uploads, and fraudulent approval.

**SAST** examines code or related static artifacts for possible issues. **DAST** tests a running application externally. **SCA** examines third-party components and dependencies. Secret scanning looks for exposed credentials. They cover different problems and can produce false positives/negatives. None guarantees absence of business logic flaws.

**Practical review sequence:** Understand architecture and data sensitivity; inventory endpoints and identities; review authentication and object/function policies; trace untrusted input to interpreters; inspect session and token handling; review dependencies/secrets/configuration; test normal and forbidden workflows; document findings and retest fixes.

**Logging:** Record security-significant events such as failed logins, permission changes, denied cross-tenant actions, and administrative exports. Include useful actor/resource/time context and request correlation IDs. Redact secrets and unnecessary personal data; protect log access and integrity. Alerting needs owners and a response path.

**Safe errors:** Return a useful generic error and correlation ID while storing sanitized details internally. Authorization-service failure should not grant unrestricted access. Transactions should roll back safely. Avoid returning stack traces, database queries, or tokens to clients.

# 15. Finding report: example a client can act on

**Title:** Cross-tenant invoice download due to missing object authorization.

**Observation:** In an authorized test, Customer A's test account downloaded Customer B's seeded invoice by changing the invoice identifier. No real customer dataset was collected.

**Affected assets:** Invoice download endpoint and related export paths pending review.

**Root cause:** The endpoint verified login but did not enforce the caller's tenant relationship to the invoice.

**Impact:** Possible disclosure of billing records across clients. Scale and sensitivity require investigation; one test confirms the flaw but does not prove past malicious exploitation.

**Severity rationale:** Consider network exposure, account requirements, enumeration, data sensitivity, reachable functions, and compensating controls. Use an agreed rating method and document assumptions.

**Remediation:** Centralize object/action policy enforcement, bind tenant identity to trusted context, review sibling endpoints, and add negative authorization tests.

**Verification:** Confirm own-resource access still works; reject cross-user, cross-role, and cross-tenant reads/writes without exposing data. Review logs and incident implications with appropriate owners.

# 16. Interview scenarios with reasoned answers

## “The client already uses HTTPS. Why test for SQLi?”

TLS protects bytes in transit. The server decrypts and processes those bytes; unsafe query construction can still interpret input as SQL. Fix parameterization and database privileges. Evaluate how much data the affected account can reach.

## “We changed invoice IDs to UUIDs. Is the issue fixed?”

UUIDs make guessing harder but do not prove access rights. A leaked link or another API response can reveal the ID. Verify server-side policy for the authenticated user, resource, and action.

## “The cookie is HttpOnly, so does XSS matter?”

Yes. A script may read page data or make authenticated actions even without reading the cookie. Fix the unsafe rendering path and strengthen CSP; assess which actions and data were reachable.

## “The CORS error appears in the console. Are we secure?”

It means a particular browser read was blocked, not that the server rejected the request or enforced user permissions. Examine the actual network request, side effects, authentication, authorization, and CSRF controls.

## “A vulnerability scanner says the site has XSS. What next?”

Validate within scope using harmless evidence, trace the source and sink, determine affected users and rendering context, and reduce false positives. Document impact and context-specific remediation. Retest that malicious content is inert and legitimate behavior still works.

## “How do you secure the portal end to end?”

Identify sensitive data and roles; enforce login and object/function policies; use TLS and secure sessions; protect query/rendering boundaries; secure uploads and outbound requests; limit abuse and resources; protect dependencies and secrets; log meaningful events; test denied paths and recovery. Prioritize based on business exposure and verify each control.

# 17. Practice: ten questions with solutions

1. **A hidden admin button can be invoked through a direct API request. What failed?** Server-side function authorization; hiding UI is insufficient.
2. **A parameterized query includes a user-chosen column by string concatenation. Safe?** Values may be protected but identifiers need a fixed mapping/allowlist.
3. **A text comment is assigned through `textContent`. Why useful?** It is displayed as text rather than parsed as HTML in that sink.
4. **A cookie-authenticated money-transfer action accepts GET. Concern?** State-changing GET requests and absent intent validation expose CSRF and accidental triggering risks.
5. **A signed JWT has a private salary field. Is it confidential?** Not in the usual readable signed-token format.
6. **A logout clears local storage, but a copied JWT still works. Why?** Server acceptance remains until expiry or an effective revocation mechanism applies.
7. **A URL-import API rejects `localhost` only. Enough for SSRF?** No; consider IP formats, DNS resolution, redirects, internal destinations, and egress policy.
8. **An upload's filename ends in `.jpg`. Enough validation?** No; verify allowed content, storage/execution boundaries, size, and access.
9. **Two concurrent requests spend one remaining coupon twice. What is this?** A business-rule race requiring atomic enforcement/idempotency or suitable transaction controls.
10. **The authorization service is unavailable and the app allows all requests. Category?** Unsafe exceptional-condition handling and an access-control failure; deny sensitive actions safely.

# 18. Last revision page

| Weakness | Primary fix to say aloud |
|---|---|
| IDOR/BOLA | Server-side object/action/tenant authorization |
| SQL injection | Parameterized values; allowlist dynamic structure |
| XSS | Safe rendering, context-specific encoding, sanitization when HTML is intended |
| CSRF | Verify action intent with appropriate token/origin and cookie controls |
| Weak login/recovery | Strong credentials, MFA, abuse controls, secure recovery |
| Session theft/fixation | Secure transport/storage, rotate identifiers, expire and revoke |
| JWT misuse | Verify configured cryptography and expected claims; manage lifecycle |
| CORS error/misconfiguration | Exact read-sharing policy plus separate auth and CSRF controls |
| SSRF | Restrict/validate destinations and control egress |
| Command injection | Avoid shells, use safe APIs and validated arguments |
| Upload/path problems | Content and path boundaries, safe storage, access checks |
| Business logic/races | Enforce invariants atomically and test abuse cases |

**Speak in this order:** What is trusted? Where is the boundary crossed? What can the attacker do? Which control changes that outcome? How will the fix be verified?
