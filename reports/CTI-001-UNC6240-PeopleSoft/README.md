# CTI-001 — UNC6240 / ShinyHunters Exploitation of Oracle PeopleSoft

## Case Study: FBI PeopleSoft Compromise

| Field | Detail |
|---|---|
| **Report ID** | CTI-001 |
| **Report Type** | Cyber Threat Intelligence Assessment |
| **Published** | 6 October 2026 |
| **Information Cut-off** | 6 October 2026 |
| **Sources** | Publicly Available Information |
| **Overall Confidence** | Moderate–High at campaign level; variable for FBI-specific assessments |
| **Status** | Published |
| **Version** | 1.0 |

---

## 1. Intelligence Requirement

### Primary Intelligence Question

**What does the UNC6240 exploitation of Oracle PeopleSoft, including the reported FBI compromise, demonstrate about the risk created by internet-accessible enterprise applications, compensating security controls and application trust relationships, and what should defenders do differently as a result?**

### Supporting Questions

- How did UNC6240 gain initial access to vulnerable PeopleSoft environments?
- How did the actor adapt to defensive controls?
- What post-exploitation capabilities were observed?
- What is publicly established about the FBI compromise?
- Which control assumptions may have contributed to continued exposure?
- What detection opportunities existed across web, endpoint, identity and network telemetry?
- What defensive lessons can reasonably be derived without overstating the available evidence?

---

## 2. Executive Assessment

UNC6240, associated with the ShinyHunters cybercriminal ecosystem, exploited **CVE-2026-35273**, a critical unauthenticated remote-code-execution vulnerability affecting Oracle PeopleSoft PeopleTools. Oracle rates the vulnerability **CVSS 9.8** and confirms that it is remotely exploitable without authentication. Oracle released an out-of-band security alert on 10 June 2026 following exploitation observed by Google Threat Intelligence Group (GTIG) and Mandiant. [1][2]

The campaign provides a particularly useful example of **adversary adaptation to defensive guidance**. After defenders implemented WAF rules intended to block access to the vulnerable `/PSEMHUB/` endpoint, UNC6240 modified its requests to use encoded URL paths such as `/%50SEMHUB/`. Some WAF and reverse-proxy implementations evaluated the literal path before URL decoding, while the downstream PeopleSoft application server decoded the request and processed it normally. Mandiant subsequently observed renewed exploitation against organisations that had implemented WAF mitigations but had not patched the underlying vulnerability. [2]

This leads to a central defensive judgement of this assessment:

> **A compensating control that has not been adversarially validated provides risk reduction, not assurance.**

The FBI case demonstrates the potential consequences. Reuters reported on 6 October that the FBI determined the incident resulted from a contractor failing to implement a security patch explicitly issued to secure the affected platform. Sources identified the platform as Oracle PeopleSoft and the third-party organisation as Accenture. The compromise reportedly exposed sensitive personal, medical and operational information concerning FBI personnel. [3]

However, **there is currently insufficient public evidence to conclude why the FBI environment remained unpatched**, or whether WAF mitigation influenced that decision. Any such explanation must therefore remain a hypothesis rather than a finding.

The wider campaign also demonstrates that the meaningful blast radius of an application compromise cannot be measured purely by network reachability. PeopleSoft service identities may access application configuration, database connection strings, application data and other trusted credentials. Mandiant specifically recommends rotating credentials accessible from the PeopleSoft tier following compromise. [2]

Accordingly:

> **Network blast radius is not equivalent to business, data, identity or trust blast radius.**

---

## 3. Key Judgements

**KJ-1 — High confidence:** CVE-2026-35273 provided UNC6240 with unauthenticated remote code execution against vulnerable PeopleSoft environments exposed to attacker-controlled network traffic. [1][4]

**KJ-2 — High confidence:** UNC6240 adapted its exploitation technique to bypass literal path-based WAF mitigations by manipulating URL encoding, demonstrating active adaptation to published defensive controls. [2]

**KJ-3 — High confidence:** WAF mitigation did not provide equivalent security to remediation of the underlying vulnerability. Mandiant observed renewed targeting of organisations that had implemented WAF rules but had not patched. [2]

**KJ-4 — High confidence:** Compromise of the PeopleSoft web/application tier could provide meaningful access even without conventional domain compromise because service identities may expose database credentials, application data and other trusted credentials. [2]

**KJ-5 — Moderate confidence:** Detection of this activity should be achievable where appropriate web and endpoint telemetry exists, particularly through monitoring abnormal PSEMHUB requests and shell execution spawned by the WebLogic Java process. Whether such detections existed or fired in the FBI environment is unknown. [2]

**KJ-6 — Moderate confidence:** Enterprise defenders should treat administrative endpoints that are believed to be inaccessible externally as high-value negative-security assumptions. Successful external interaction with such an endpoint should be treated as an indicator that a preventive control has failed.

**KJ-7 — Low–Moderate confidence:** The FBI's delayed remediation may have involved operational constraints, compensating controls, change-management delays or another risk-acceptance process. Public reporting does **not** currently establish which explanation applies.

---

## 4. Threat Actor — UNC6240 / ShinyHunters

UNC6240 is associated by Google Threat Intelligence Group with the wider **ShinyHunters** cybercriminal ecosystem. The group is primarily associated with large-scale data theft and extortion rather than traditional state-sponsored espionage. [2]

The FBI incident appears atypical in motivation. Public reporting described retaliatory and reputational motivations connected to law-enforcement activity against the group. This creates an important intelligence distinction:

> **Attacker motivation and asset value are related, but they are not equivalent.**

Even where the original objective is reputational or retaliatory rather than financial, successfully acquired sensitive personnel information may have secondary criminal, counterintelligence or coercive value.

---

## 5. Campaign Timeline

### 27 May – 9 June 2026

GTIG observed UNC6240 exploiting CVE-2026-35273 as a zero-day, initially with significant targeting of higher-education organisations. [4]

### 10 June 2026

Oracle issued an out-of-band Security Alert addressing CVE-2026-35273. Oracle described the vulnerability as remotely exploitable without authentication and capable of resulting in remote code execution, assigning a CVSS v3.1 score of **9.8**. [1]

### June 2026

Mandiant recommended patching and restricting external access to `/PSEMHUB/*` where immediate remediation was not possible. [4]

### September 2026

UNC6240 returned with modified exploitation capable of bypassing literal path-based WAF rules. Rather than requesting `/PSEMHUB/`, the actor could request `/%50SEMHUB/`, where `%50` represents the character `P`. The application server ultimately decoded the path while some upstream security controls evaluated the non-normalised representation, allowing traffic to reach the vulnerable endpoint. [2]

### September 2026 — FBI Incident

ShinyHunters claimed responsibility for compromising the FBI jobs platform and obtaining sensitive employee information. Subsequent reporting described exposure of personally identifiable, role-related and medical information. [3]

### 6 October 2026

The FBI publicly confirmed that its review had determined the incident resulted from a third-party-managed platform where a contractor failed to implement a security patch explicitly issued to secure it. Reuters sources identified the platform as PeopleSoft and the third-party organisation as Accenture. [3]

---

## 6. Technical Attack Lifecycle

### 6.1 Initial Access

**MITRE ATT&CK: T1190 — Exploit Public-Facing Application**

CVE-2026-35273 affects the Environment Management functionality within Oracle PeopleSoft PeopleTools. Oracle states that exploitation can occur remotely, requires no authentication or user interaction, and may result in remote code execution. [1]

Mandiant observed UNC6240 sending crafted requests to the PSEMHUB endpoint containing serialized Java objects. An unpatched system could disclose host information, allowing the actor to validate exploitability before deploying additional payloads. [4]

This means exploitation could begin with relatively low-impact verification activity before obvious persistence or malware deployment occurred.

---

## 7. Defence Evasion — WAF Bypass

One of the most significant elements of the campaign was not the vulnerability itself, but the attacker's response to defensive mitigation.

Following disclosure, organisations could restrict access to `/PSEMHUB/` at their perimeter while preparing to patch. UNC6240 subsequently modified the path representation. For example, `/PSEMHUB/` became `/%50SEMHUB/`. Some security controls evaluated the literal URL before normalisation. The application server subsequently decoded the path and routed the request to the vulnerable servlet. [2]

The underlying problem can therefore be represented as:

**Attacker request** → **Security control evaluates representation A** → **Downstream application canonicalises input** → **Application evaluates representation B** → **Security policy and application interpretation disagree**

This is fundamentally a **canonicalisation problem**.

### Defensive implication

Security controls that evaluate attacker-controlled input differently from the protected application can create bypass opportunities. Testing should therefore include URL encoding, double encoding where relevant, mixed case, alternate path representations, normalisation differences, unexpected HTTP methods and equivalent representations interpreted differently across security layers.

---

## 8. Execution

Mandiant observed two significant exploitation patterns.

### Web-shell deployment

UNC6240 deployed JSP web shells into PeopleSoft application directories, providing persistent remote command execution. [2][4]

### Fileless command execution

The actor could also execute commands directly through the vulnerable servlet without first writing a web shell to disk. At endpoint level, this could manifest as process relationships such as `java → cmd.exe` or `java → /bin/sh`, depending on the operating system. [2]

This is particularly important because a detection strategy based exclusively on malicious-file creation would fail to detect fileless exploitation.

---

## 9. Post-Exploitation

Mandiant observed a broader post-exploitation toolkit and activity including JSP web shells, SIDEEYE, MeshAgent/MeshCentral, Neo-reGeorg tunnelling, host and user discovery, credential access, command execution, reverse-proxy capabilities and staging activity. [2]

Across compromised instances observed by Mandiant, approximately one quarter of actor commands executed as `root` or `NT AUTHORITY\SYSTEM`. Other commands executed under PeopleSoft or WebLogic service accounts. Those lower-privileged contexts were nevertheless valuable because they could provide access to PeopleSoft configuration files, database connection strings and application data. [2]

### Important attribution boundary

These techniques describe the **wider UNC6240 PeopleSoft campaign**. They must **not** automatically be attributed to the FBI compromise. Public reporting currently does not provide sufficient technical detail to establish which post-exploitation techniques were used specifically against the FBI environment.

---

## 10. FBI Case Assessment

### Established / Reported

The FBI determined that a third-party organisation managed the affected platform, a contractor failed to implement a security patch explicitly issued to secure it, the incident resulted from that security failure, the contractor was subsequently removed, and mitigation actions were taken. [3]

Reuters sources identify Oracle PeopleSoft as the affected platform and Accenture as the third-party organisation. Sensitive data reportedly exposed included personal information, operational role details, addresses and medical information. [3]

### Not currently established

Public evidence does **not** establish:

- why the patch was not applied;
- whether patch deployment had been attempted;
- whether compatibility concerns existed;
- whether a formal vulnerability exception existed;
- whether a WAF was deployed;
- whether WAF protection was considered an adequate temporary control;
- whether PSEMHUB was deliberately exposed;
- the host's domain membership;
- the network segmentation model;
- the PeopleSoft service account privileges;
- which credentials were accessible;
- whether lateral movement occurred;
- whether cloud infrastructure was subsequently accessed;
- which security alerts fired;
- how SOC analysts handled any precursor activity.

These therefore remain intelligence gaps.

---

## 11. Blast Radius Analysis

A common way of evaluating application compromise is to ask: **What other systems can this server reach?** That question is necessary but insufficient.

A compromised application server may have very limited general network access while retaining a single highly valuable trust relationship:

**Internet → PeopleSoft Web/Application Tier → Permitted database connection → Sensitive workforce dataset**

A firewall may therefore reduce network blast radius while having little effect on **data blast radius**.

Similarly, the application may possess database credentials, integration credentials, API tokens, certificates, service-account privileges, cloud workload identity, access to middleware or downstream business-system trust.

The more useful analytical question becomes:

> **What does this system trust, and what systems trust this system?**

This moves analysis from simple network topology toward **trust-path analysis**.

---

## 12. Identity and Trust Considerations

PeopleSoft HCM can occupy an important position within an organisation's identity lifecycle. Depending on architecture, HR systems may act as authoritative workforce sources feeding identity-governance or identity-management processes that subsequently provision or update Active Directory, identity providers, SaaS applications, enterprise directories and access-governance systems.

This does **not** mean every PeopleSoft deployment has these relationships. It means compromise analysis should establish both directions of trust:

**What identities and credentials can PeopleSoft consume?**

and

**What downstream systems consume information or instructions originating from PeopleSoft?**

The second question may reveal impact that is invisible from conventional firewall analysis.

---

## 13. Cloud Considerations

Where the PeopleSoft workload is hosted in a cloud environment, application compromise introduces another critical question:

> **What identity does this workload run as?**

A compromised application process may potentially gain access to instance or workload metadata, temporary cloud credentials, secrets accessible to the workload, object storage, APIs or other cloud services permitted by its role.

Relevant ATT&CK techniques may include, **where evidence supports them**, T1190 (Exploit Public-Facing Application), T1552.005 (Cloud Instance Metadata API), T1528 (Steal Application Access Token) and T1078.004 (Cloud Accounts).

Application compromise must not automatically be equated with cloud-control-plane compromise. The correct analytical chain is:

**Application execution → workload identity exposure? → credential/token acquisition? → IAM permissions available? → control-plane actions observed?**

Each transition requires evidence.

---

## 14. Detection Opportunities

### Web Telemetry

Defenders should monitor for external requests to PSEMHUB, encoded or otherwise non-normalised PSEMHUB paths, repeated POST requests to the hub endpoint, unexpected JSP/JSPX access, unusual requests to administrative components and external access to interfaces expected to be internal only. [2]

### Endpoint Telemetry

High-value behaviours include Java/WebLogic spawning command shells, unexpected JSP or executable creation within web application directories, RMM installation, unusual archive creation, credential-access activity and abnormal outbound network activity originating from application service processes. [2]

### Identity Telemetry

Service identities should be baselined by expected hosts, normal parent processes, expected network destinations, normal logon type, expected privilege and expected data access.

A process executing as a legitimate PeopleSoft service identity is not inherently benign.

> **Legitimate identity does not imply legitimate intent.**

---

## 15. Detection-to-Response Problem

Even where telemetry exists, successful defence requires an entire decision chain:

**Telemetry generated → Detection exists → Alert generated → Analyst receives alert → Analyst has sufficient context → Activity interpreted correctly → Correct escalation occurs**

A failure at any point can turn technically detectable behaviour into an operational miss.

This becomes especially relevant in large environments where knowledge is distributed. The network team may understand ingress; the application team may understand PeopleSoft; the vulnerability team may understand CVE exposure; the WAF team may understand perimeter mitigation; the IAM team may understand service identity; and the SOC analyst may see only `java.exe → cmd.exe`.

Without appropriate asset and control context, each team can possess individually correct information while no single decision-maker sees the complete attack path.

---

## 16. Security Context and Need-to-Know

SOC analysts do not necessarily require complete infrastructure diagrams or unrestricted access to sensitive architectural information. They do require enough **security-relevant context to make the correct decision**.

Useful enrichment includes asset criticality, internet exposure, system function, data sensitivity, whether administrative interfaces should be externally reachable, service owner, escalation route, active vulnerability exceptions, compensating controls and expiry date of temporary controls.

This enables better decisions without unnecessarily violating need-to-know principles.

---

## 17. Monitor the Impossible

One of the strongest detection lessons from this case is to monitor events that preventive controls claim should not occur.

Suppose the security architecture asserts: **External users cannot reach PSEMHUB.** That statement creates a high-value detection opportunity: **alert when an external source successfully reaches PSEMHUB.**

The same principle can apply elsewhere: administrative interfaces that should never be internet accessible; service accounts that should never log on interactively; servers that should never initiate internet connections; workloads that should never call particular cloud APIs; or endpoints that should never execute from particular directories.

> **Monitor for events your preventive architecture says should be impossible.**

---

## 18. Analytical Hypotheses

### H1 — Compensating controls may have contributed to delayed FBI remediation

**Assessment:** It is plausible that operational complexity or temporary compensating controls contributed to the delay in patching the affected FBI PeopleSoft environment.

**Confidence:** **Low–Moderate**

#### Supporting reasoning

- Oracle issued an emergency patch.
- Enterprise PeopleSoft patching can involve operational complexity.
- Mandiant recommended temporary perimeter mitigation where immediate remediation was not possible.
- UNC6240 subsequently targeted organisations that had implemented WAF mitigation without applying the patch. [2]
- Reuters reports that the FBI environment remained vulnerable because the relevant patch was not implemented. [3]

#### Alternative explanations

The patch failure may instead have resulted from administrative error, unclear ownership, failed patch deployment, compatibility concerns, testing delays, asset inventory failure, contractual responsibility gaps, vulnerability-management workflow failure, change freeze or simple negligence.

#### Intelligence gap

There is currently **no public evidence establishing that the FBI relied upon a WAF or deferred patching because of a compensating control**.

#### Collection requirement

Useful evidence would include vulnerability exception records, change tickets, patch deployment history, WAF change history, risk-acceptance documentation, contractor communications and the incident-response timeline.

#### Defensive implication

Even if H1 is ultimately rejected, the wider campaign demonstrates the danger independently:

> **Compensating controls should buy remediation time, not become indefinite substitutes for remediation.**

### H2 — The exposed application tier may have been deliberately isolated

**Assessment:** The compromised PeopleSoft tier may have existed within a deliberately restricted security boundary, potentially including a DMZ, dedicated domain or non-domain-joined configuration.

**Confidence:** **Low**

Internet-facing enterprise applications are commonly segmented to reduce exposure to internal environments. The application could alternatively have been conventionally domain joined with network controls providing segmentation. The FBI architecture has not been publicly established.

**Defensive implication:** **Identity isolation does not equal application isolation.** A WORKGROUP or DMZ host may still possess database credentials, certificates, API tokens, cloud identities and explicitly permitted network paths.

### H3 — Application trust may have been more important than conventional lateral movement

**Assessment:** UNC6240 may not have required broad lateral movement to achieve meaningful objectives if the compromised PeopleSoft application already provided access to sufficiently valuable information.

**Confidence:** **Moderate analytical plausibility; FBI-specific occurrence unconfirmed**

The FBI compromise reportedly resulted in theft of highly sensitive personnel information. The PeopleSoft ecosystem therefore represented a valuable target independently of whether an attacker achieved domain-level compromise. [3]

**Defensive implication:** Incident responders should not define severity solely according to how far an attacker travelled. **Attacker objective determines meaningful blast radius.** An attacker who acquires the desired dataset from the first compromised system may have no operational need to become Domain Administrator.

### H4 — Legitimate-looking service activity may have complicated detection

**Assessment:** Execution occurring under expected PeopleSoft or WebLogic service identities could make malicious activity harder to distinguish from legitimate administration where behavioural detections and application context are weak.

**Confidence:** **Moderate analytical plausibility; FBI-specific occurrence unknown**

Mandiant observed UNC6240 executing commands under legitimate PeopleSoft/WebLogic service contexts across the wider campaign. [2]

**Alternative:** Strong process-tree detection could make the activity conspicuous regardless of identity.

**Intelligence gap:** No public evidence currently establishes which endpoint alerts or SOC decisions occurred during the FBI incident.

**Defensive implication:** Service identities should be evaluated according to **behaviour**, not merely identity legitimacy.

---

## 19. Intelligence Gaps

Information that would materially improve this assessment includes:

### Architecture
- Was PSEMHUB directly internet reachable?
- Was the PeopleSoft tier located in a DMZ?
- Was the host domain joined?
- What backend systems trusted it?
- What outbound connectivity existed?

### Preventive Controls
- Was a WAF deployed?
- Was `/PSEMHUB/` blocked?
- Had an exception been granted?
- Had the compensating control been adversarially tested?

### Vulnerability Management
- Why was the June patch not applied?
- Was deployment attempted?
- Was there an explicit risk owner?
- Was a remediation deadline established?
- Did an exception expire without remediation?

### Detection
- Were WebLogic access logs centrally ingested?
- Was endpoint telemetry available?
- Did relevant detections fire?
- Were precursor requests observed?
- Were alerts incorrectly classified as benign?

### Post-Exploitation
- What privilege did the application process hold?
- Which credentials were accessible?
- Did lateral movement occur?
- Were identity systems affected?
- Was cloud workload identity exposed?
- Were cloud-control-plane actions performed?

These gaps should constrain confidence rather than being filled with assumptions.

---

## 20. Lessons Derived

1. **Compensating controls buy time; they do not remove vulnerabilities.** A WAF rule may reduce exploitability but does not change the vulnerable application code.
2. **An unvalidated control is a hypothesis, not assurance.** A control should not be considered effective merely because it exists or appears logically sound.
3. **Monitor events your architecture says should be impossible.** Preventive assumptions can become high-value detective controls.
4. **Network blast radius is not business blast radius.** One permitted database or identity relationship may provide more value than access to hundreds of ordinary hosts.
5. **Map trust, not only connectivity.** Ask: *What does this system trust, and what systems trust this system?*
6. **Legitimate identity does not imply legitimate behaviour.** Service accounts require behavioural baselines.
7. **Analysts need context at decision time.** Telemetry without system purpose, criticality, expected behaviour and control context can produce technically correct but operationally poor decisions.
8. **Defence-in-depth requires independent effectiveness.** Multiple controls provide limited value if they share the same assumption or interpretation weakness.
9. **Attacker objectives define meaningful impact.** Domain compromise is not a prerequisite for a severe breach.
10. **Security claims should be challenged.** What evidence demonstrates the claim? What assumptions must remain true? How could an attacker deliberately violate those assumptions?

---

## 21. Recommended Defensive Actions

Organisations operating PeopleSoft or similarly exposed enterprise applications should:

1. **Prioritise remediation of internet-accessible critical vulnerabilities.** Temporary mitigations should have explicit owners and expiry dates.
2. **Minimise public exposure of administrative and management interfaces.** Components such as EMHub/PSEMHUB should not be externally accessible where business functionality does not require it. [2]
3. **Adversarially validate compensating controls.** Test encoding, canonicalisation, alternate paths and other representation differences.
4. **Detect successful external interaction with management endpoints that architecture claims are inaccessible.**
5. **Detect abnormal application process relationships**, particularly WebLogic/Java spawning command shells.
6. **Baseline service identities behaviourally**, including hosts, child processes, destinations and authentication patterns.
7. **Inventory credentials accessible from application tiers**, including database credentials, API secrets, certificates and cloud workload credentials.
8. **Map application trust relationships**, not merely firewall connectivity.
9. **Apply least privilege to workload and cloud identities.**
10. **Provide SOC analysts with security-relevant asset context** at triage time.
11. **Track temporary security exceptions centrally**, including risk owner, rationale, compensating control, validation status and expiry.
12. **Hunt retrospectively following critical vulnerability disclosure**, particularly where a vulnerability was exploited as a zero-day before patch availability.

---

## 22. Overall Assessment

The UNC6240 PeopleSoft campaign demonstrates a recurring problem in enterprise defence: organisations may correctly deploy multiple security controls while remaining vulnerable because the assumptions connecting those controls have not been tested against adversarial behaviour.

The September exploitation campaign is particularly instructive because UNC6240 did not require an entirely new vulnerability. The actor adapted its representation of traffic so that the defensive control and protected application interpreted the same request differently. [2]

The FBI incident then demonstrates the potential impact when a critical application remains vulnerable after remediation becomes available. Public evidence establishes the failure to implement the relevant security patch but does not yet establish **why** that failure occurred. [3]

It would therefore be analytically unsound to conclude that the FBI relied on WAF mitigation or that any specific architecture or SOC failure caused the breach without additional evidence.

What can be concluded more confidently is broader:

> **Compensating controls buy time, not confidence.**

> **A control that has not been adversarially validated should be treated as an assumption requiring evidence.**

> **The blast radius of an application compromise should be measured through data, identity and trust relationships—not solely network reachability.**

For defenders, the most valuable outcome from this incident is therefore not simply another instruction to patch critical vulnerabilities. It is a reminder to continuously challenge the assumptions on which defensive architecture depends.

---

## 23. Source Set

**[1] Oracle — Security Alert Advisory CVE-2026-35273**  
https://www.oracle.com/security-alerts/alert-cve-2026-35273.html

Authoritative source for vulnerability characteristics, affected PeopleTools versions, CVSS assessment and remediation urgency.

**[2] Google Threat Intelligence Group / Mandiant — ShinyHunters Renewed Mass Exploitation Campaign Targeting Oracle PeopleSoft**  
https://cloud.google.com/blog/topics/threat-intelligence/shinyhunters-renewed-mass-exploitation-campaign-targeting-oracle-peoplesoft

Primary technical source for the renewed campaign, WAF bypass, exploitation lifecycle, post-exploitation behaviour and defensive recommendations.

**[3] Reuters — Accenture contractor removed from FBI following damaging data breach, sources say (6 October 2026)**  
https://www.reuters.com/technology/accenture-contractor-removed-fbi-following-damaging-data-breach-sources-say-2026-10-06/

Independent reporting for the FBI's attribution of the incident to an unimplemented patch, identification of PeopleSoft/Accenture through sources, exposed information and subsequent response.

**[4] Google Threat Intelligence Group / Mandiant — ShinyHunters Targets Education Sector with Oracle PeopleSoft Exploit (11 June 2026)**  
https://cloud.google.com/blog/topics/threat-intelligence/shinyhunters-targets-education-sector-oracle-exploit/

Primary technical source for the initial May–June zero-day exploitation period and early campaign observations.

---

## Publication Note

This assessment represents the analyst's judgement based on publicly available information as of the information cut-off date. Confidence levels may change as additional evidence becomes available. Material future changes should be versioned and documented rather than silently replacing prior analytical judgements.
