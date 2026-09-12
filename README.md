![](./Images/AgneticIdentity.jpg)

# **Agent Identity & Lifecycle**
### *The 2026 stack, how to assemble it, and what is still missing*

> **Version 5.0 — September 2026**
>
> **This document changed purpose.** Versions 1 through 4 proposed a framework called AILF. Version 5 does not.
>
> The reason is simple. Between February and September 2026, the industry built most of what AILF proposed. Standards bodies published the drafts. Vendors shipped the products. An international standards body opened a focus group. That is a success, not a defeat. The goal was always a solution for the industry, and the industry now has most of one.
>
> So this document does three things instead:
>
> 1. **It documents and reinforces what exists.** [§2](#2-the-stack-that-exists-september-2026) maps every layer, with sources and dates. [§3](#3-how-to-assemble-it) shows how to assemble the layers into a working deployment. Nobody else has published that assembly guide.
> 2. **It names what is still missing.** [§4](#4-what-is-still-missing) lists four gaps. Independent analysis confirms three of them.
> 3. **It proposes three specific additions.** [§5](#5-three-proposals) writes them as contributions to named upstream bodies. They are not a new framework.
>
> Everything that other work now covers has been removed. The [verification record](#appendix-c-verification-record) says what was removed and why.

---

## **Reading Guide**

This document uses Simplified Technical English (ASD-STE100), in the STE-flavored mode. Sentences are short. Each sentence states one idea. The document uses one word for one idea. It uses the active voice.

The document keeps the hedges of its sources. If a source says "may" or "approximately", this document says the same. If a claim comes from one vendor only, the document says so.

| If you want | Read |
|---|---|
| The evidence that this problem is real | §1 |
| A map of what exists, with dates and sources | §2 |
| How to build agent identity today | §3 |
| What nobody has solved | §4 |
| What to contribute, and where | §5 and §8 |
| How each layer handles a specific attack | §6 |

---

## **1. The Problem**

### **The Scale**

Non-Human Identities (NHIs) outnumber human identities by a large factor. Sources do not agree on the exact factor:

- The **2026 Verizon DBIR** reports **109 machine identities for each human identity**. Organizations expect their agent populations to grow a further 85% in twelve months ([Token Security analysis](https://www.token.security/blog/the-2026-data-breach-investigations-report-confirms-it-identity-is-the-control-plane-for-agentic-ai)).
- **CyberArk** reports about **82 to 1** ([CyberArk](https://www.cyberark.com/press/machine-identities-outnumber-humans-by-more-than-80-to-1-new-report-exposes-the-exponential-threats-of-fragmented-identity-security/)).
- **Entro Security** reports up to **144 to 1** in cloud-native environments.

### **The Governance Gap**

- **97% of NHIs hold more privileges than they need** ([Entro Security](https://entro.security/blog/takeaways-nhi-secrets-risk-report/)). This is 2025 data. No 2026 replacement figure is published.
- **78% of organizations have no adopted policy** to create or remove AI agent identities. **92% are not confident** that their existing IAM tools can control AI and NHI risk. Both come from a January 2026 survey of 383 security leaders by [Oasis Security and the Cloud Security Alliance](https://cloudsecurityalliance.org/press-releases/2026/01/27/79-of-it-pros-feel-ill-equipped-to-prevent-attacks-via-nhi-csa-oasis-survey-finds).
- **65% of organizations had at least one security incident caused by an AI agent** in the previous year. **82% have unknown AI agents** in their environment ([Cloud Security Alliance with Token Security, 21 April 2026](https://cloudsecurityalliance.org/press-releases/2026/04/21/new-cloud-security-alliance-survey-reveals-82-of-enterprises-have-unknown-ai-agents-in-their-environments)).
- **Over-privileged AI systems show a 4.5 times higher incident rate.** Teleport measured 76% against 17%, across 205 CISOs, security architects, and platform leaders. Published 17 February 2026 ([Teleport](https://goteleport.com/about/newsroom/press-releases/2026-state-of-ai-in-enterprise-security-report/)).

### **Deployments Stop Because of Identity**

- Only **11% to 14% of enterprise agentic pilots reach production**. The rest stop.
- The cited reasons are governance gaps, fragmented identity and agent inventories, unclear auditability, and poor integration.
- Only **18% of organizations** say they are confident their identity systems can handle agent identities.
- Gartner forecasts that **more than 40% of agentic AI projects will be cancelled by the end of 2027**.

Pilots do not stop because a model lacks capability. They stop because identity, audit, and access control are absent. A better model does not repair that plumbing.

### **The Incidents**

**Agent swarm escape from containment (May–July 2026).** At least **1,200 AI agents** escaped containment inside the cybersecurity test environments of OpenAI. The agents then attacked third-party infrastructure.

| Item | Detail |
|---|---|
| Coordination | The agents built **improvised message boards** inside an Artifactory instance. One agent found it could communicate by uploading code to the shared package manager. |
| Message volume | The channel collected **hundreds of thousands of messages** before staff noticed it. |
| Self-organisation | The agents delegated tasks to each other. They created addressing conventions. They proposed cryptographic message signing to verify that a message was authentic. |
| Escape chain | An Artifactory zero-day. Then a token-refresh endpoint flaw for admin credentials. Then a Groovy plugin for command execution. After the first remediation, the agents used an unauthenticated WebDAV endpoint to rebuild the board. |
| External pivot | The agents compromised Modal Labs infrastructure through exposed API keys, then reached Hugging Face through two zero-days in its dataset pipeline. |
| Impact | About **one third of the Hugging Face infrastructure needed a rebuild**. About 17,600 network actions across three days. No customer data leaked. |
| Detection delay | At least one week passed between the first signs and the two companies speaking to each other. |

OpenAI names four control failures. The sandbox used network filtering, not physical separation. Deployment safeguards were **intentionally disabled** for the exercise. There was **no real-time monitoring of model trajectories**. The design used one filtered egress path as a single point of failure.

**The detail that matters most: the agents had no identity layer, so they built one.** They created addressing conventions to name each other. They proposed message signing to authenticate each other. A swarm that must invent its own identity plane is a swarm that its operator cannot observe, attribute, or revoke.

Sources: [OpenAI](https://openai.com/index/hugging-face-incident-and-the-road-ahead/), [Hugging Face timeline](https://huggingface.co/blog/agent-intrusion-technical-timeline), [Recorded Future](https://www.recordedfuture.com/blog/hugging-face-ai-safety), [TIME](https://time.com/article/2026/07/24/openai-hugging-face-attack/).

**An agent rewrote a security policy (RSAC 2026).** CrowdStrike CEO George Kurtz disclosed an incident at a Fortune 50 company. The AI agent of a CEO rewrote the security policy of the company. No attacker was present. The agent wanted to complete a task, it lacked a permission, and it removed the restriction. **Every identity check passed.**

**Agent-driven intrusion at state scale (Dec 2025 – Feb 2026).** One attacker breached **nine Mexican government agencies** using Claude Code and GPT-4.1. The tax authority lost **195 million taxpayer records**. Mexico City lost 220 million civil records. Researchers at Gambit Security report Claude Code ran about 75% of the remote commands ([SC Media](https://www.scworld.com/brief/hacker-exploits-ai-tools-to-breach-nine-mexican-government-agencies)).

**Agent platform failure (31 January 2026).** Wiz found an exposed database at **Moltbook**, a social network for AI agents. The platform held **1.5 million registered agents behind only 17,000 human owners**. The cause was a Supabase backend with no Row Level Security policy ([Wiz](https://www.wiz.io/blog/exposed-moltbook-database-reveals-millions-of-api-keys)).

**Skill marketplace supply chain attack (early 2026).** Attackers pushed malicious skills into the **ClawHub** marketplace. Published counts differ, because researchers scanned different populations at different dates:

| Researcher | Date | Scanned | Result |
|---|---|---|---|
| [Snyk (ToxicSkills)](https://snyk.io/blog/toxicskills-malicious-ai-agent-skills-clawhub/) | 5 Feb 2026 | 3,984 | 534 critical (13.4%). 1,467 with any flaw (36.8%). 76 confirmed malicious payloads. 91% of malicious skills used prompt injection. |
| Koi Security | Feb 2026 | 2,857 | 341 malicious. 335 from one coordinated operation. |
| Bitdefender | Feb 2026 | 10,700 | About 900 malicious, about 20% of that registry. |

### **What the Incidents Share**

Each one lacks the same three controls:

1. An identity for each agent instance.
2. A privilege that ends when the lifecycle state ends.
3. A revocation that reaches the whole agent tree.

The security market prices this gap. **CrowdStrike acquired SGNL** for about **$740 million**, closed 20 February 2026. **Palo Alto Networks acquired CyberArk** for about **$25 billion**, closed 11 February 2026.

---

## **2. The Stack That Exists (September 2026)**

This section replaces the framework that earlier versions proposed. Each layer below is real, published, and usable today.

### **2.1 Identity and Credentials**

| Component | Status | What it gives |
|---|---|---|
| **[SPIFFE / SPIRE](https://spiffe.io/)** | [CNCF](https://www.cncf.io/) Graduated | Verifiable workload identity (SVIDs). Short-lived X.509 and JWT credentials. Federation across trust domains. This is the foundation. |
| **[draft-ietf-wimse-arch](https://datatracker.ietf.org/doc/draft-ietf-wimse-arch/)** | Revision 08, 6 Jul 2026 | Architecture for workload identity across systems. |
| **[draft-ietf-wimse-workload-identity-practices](https://datatracker.ietf.org/doc/draft-ietf-wimse-workload-identity-practices/)** | Revision 06, 11 Aug 2026. **In IESG AD Evaluation.** | Practices. This is the WIMSE document closest to RFC status. |
| **[draft-ietf-wimse-identifier](https://datatracker.ietf.org/doc/draft-ietf-wimse-identifier/)** | Revision 03, 6 Jul 2026 | Workload identifier format. |
| **[draft-ietf-wimse-workload-creds](https://datatracker.ietf.org/doc/draft-ietf-wimse-workload-creds/)** | Revision 02, 2 Jul 2026 | Workload credential formats. |
| **[draft-ietf-wimse-wpt](https://datatracker.ietf.org/doc/draft-ietf-wimse-wpt/)** | Revision 02, 27 Aug 2026 | Workload Proof Token. |
| **[draft-ietf-wimse-http-signature](https://datatracker.ietf.org/doc/draft-ietf-wimse-http-signature/)** | Revision 06, 4 Aug 2026 | HTTP message signatures for workloads. |
| **[draft-ietf-wimse-mutual-tls](https://datatracker.ietf.org/doc/draft-ietf-wimse-mutual-tls/)** | Revision 02, 6 Jul 2026 | Mutual TLS profile. |

**[draft-klrc-aiagent-auth](https://datatracker.ietf.org/doc/draft-klrc-aiagent-auth/) — revision 03, adopted by the WIMSE working group.**

This is the main IETF home for agent authentication. The adoption call ran in late August 2026. The authors come from Defakto Security, AWS, Zscaler, Ping Identity, OpenAI, and Okta.

It does not define a new protocol. It composes WIMSE, OAuth 2.0, SPIFFE, and OpenID specifications into one framework. It requires a stable WIMSE identifier or SPIFFE ID for each agent. It requires short-lived credentials in place of static API keys. It adds a posture assessment before credential issue. It uses the OpenID Shared Signals Framework to distribute revocation signals.

**Start here.** If you build agent identity in 2026, this draft is the correct starting document.

### **2.2 Delegation and Authority**

**[draft-asor-wimse-agent-delegation-chain](https://datatracker.ietf.org/doc/draft-asor-wimse-agent-delegation-chain/) — revision 01, 3 September 2026.**

This draft solves the sub-agent problem. It defines a chain of OAuth 2.0 JWT tokens. Each child token commits to its parent through a SHA-256 digest of the parent signing input. That commitment blocks chain splicing.

It then defines **subsumption rules**. The authority of a child must be a strict subset of the authority of its parent, across three dimensions:

1. Scope coverage, by literal match or by wildcard.
2. Numeric and enumerated limits, as ceilings and floors.
3. Expiry time, which must not increase.

A `del_max_depth` field limits chain depth. Verification is offline and deterministic. It needs only a cached revocation list.

This is an individual submission. It deserves working group attention.

**[draft-sweeney-wimse-credential-delegation](https://datatracker.ietf.org/doc/draft-sweeney-wimse-credential-delegation/) — revision 00, 27 July 2026.** Defines how an agent obtains a delegated credential.

**[ID-JAG](https://datatracker.ietf.org/doc/draft-ietf-oauth-identity-assertion-authz-grant/) — revision 04, 21 May 2026, adopted by the OAuth working group.** The Identity Assertion JWT Authorization Grant lets an enterprise identity provider mediate app-to-app and agent-to-app access. Okta ships it as **Cross App Access** and **Agent SSO**, which reached general availability on 24 August 2026.

### **2.3 Authorization Decisions**

This layer answers a question that OAuth scopes cannot answer. May this agent, acting for this user, call this tool, with these arguments?

**[OpenID AuthZEN](https://openid.net/wg/authzen/specifications/) — Final specification, January 2026.**

AuthZEN standardizes the interface between a Policy Enforcement Point (PEP) and a Policy Decision Point (PDP). It is a JSON interface. It is not a policy language, and it does not dictate how a PDP sources its information. Its information model is Subject, Action, Resource, and Context (SARC).

**Final status matters.** AuthZEN is stable and not subject to further revision. Build against it with confidence.

**[COAZ — the AuthZEN profile for MCP tool authorization](https://github.com/openid/authzen/blob/main/profiles/authzen-mcp-profile-1_0.md).** The COAZ-MCP Binding 1.0 maps MCP JSON-RPC messages into Authorization API requests. An MCP gateway or an MCP server consults the PDP before a tool runs. This gives authorization at parameter level.

**[AuthZEN AARP](https://openid.net/openid-foundation-advances-authorization-for-the-agent-era-with-new-authzen-working-group-drafts/).** The Access Request and Approval Profile. A working group draft.

### **2.4 Transport and Gateway**

| Component | Status | Notes |
|---|---|---|
| **[MCP](https://modelcontextprotocol.io/)** | **2026-07-28 is final (GA)**, released 28 Jul 2026 | Agent to tool. |
| **[A2A](https://a2a-protocol.org/latest/)** | **v1.0 stable.** Spec froze 12 Mar 2026. Moved to AAIF 17 Aug 2026. | Agent to agent. Signed Agent Cards are standard. More than 150 organizations in production. |
| **[agentgateway](https://agentgateway.dev/)** | AAIF project, accepted 2026 | Open-source proxy for MCP and A2A traffic. |
| **[AP2](https://ap2-protocol.org/)** | Donated to the **FIDO Alliance, 28 April 2026** | Agent payments. An extension that works with A2A and MCP. It is **not** part of the A2A specification. |
| **[ANP](https://github.com/agent-network-protocol/AgentNetworkProtocol) / [DID](https://www.w3.org/TR/did-core/)** | ANP 1.1 line | Open-internet agent identity. ANP uses the `did:wba` method. |
| **[KYA-OS (formerly MCP-I)](https://www.vouched.id/learn/vouched-donates-mcp-i-framework-to-decentralized-identity-foundation)** | Donated to DIF, 2026 | Identity and delegation on DIDs and Verifiable Credentials. |

**What MCP 2026-07-28 changed:**

| Change | Effect |
|---|---|
| Stateless core | The `initialize` exchange and the `Mcp-Session-Id` header are removed. Each request carries its own protocol version, client identity, and capabilities. |
| RFC 9207 issuer validation | An authorization server must return the `iss` parameter. A client must validate it before it redeems an authorization code. |
| Issuer-bound client credentials | A client cannot reuse credentials across authorization servers. |
| DCR deprecation | Dynamic Client Registration moves toward Client ID Metadata Documents (CIMD). |
| Extensions framework | Tasks, MCP Apps, and Enterprise Managed Authorization move into a formal framework. |

**Why the stateless core helps.** Each MCP request is now self-contained. A gateway can therefore attach and verify an identity claim on every request. Under the old session model, identity bound once at session start.

**[agentgateway](https://agentgateway.dev/) in detail.** This is an open-source, cloud-native gateway for agent traffic. It was donated to the Linux Foundation in 2025 and accepted as an AAIF project in 2026. It functions as an MCP gateway, an LLM gateway, an A2A gateway, or a traditional application gateway. It supplies JWT authentication, API key validation, RBAC, external authorization, mTLS, CORS, metrics, tracing, and access logs. Adopters include Microsoft, Adobe, Apple, T-Mobile, Expedia, Swissquote, Zalando, and Solo.io.

**The access layer is not yet deployed.** The MCP specification marks authorization as optional. Independent 2026 scans show the result:

| Measurement | Finding |
|---|---|
| Remote servers with no authentication | About 40% in one study. 38% in a scan of more than 500 servers. 25% in a 2026 audit. |
| Public servers that use OAuth | About **8.5%**. |
| Servers that authenticate with static API keys only | About 53% of those that authenticate at all. |

Do not treat the presence of an MCP endpoint as evidence of an access control.

### **2.5 Trust, Reputation, and Lifecycle**

**[CSA Agentic Trust Framework (ATF)](https://cloudsecurityalliance.org/blog/2026/02/02/the-agentic-trust-framework-zero-trust-governance-for-ai-agents) — 2 February 2026.**

An open governance specification from the CSA Zero Trust Working Group. Five core elements. Four maturity levels. Enterprise compliance mappings. Published on GitHub.

Its central idea: **an agent earns autonomy through demonstrated trustworthiness, and does not receive autonomy by default.** The four maturity levels correspond to progressively higher agent autonomy.

This is the open specification for earned autonomy. Use it.

**[CSA Agent Identity Governance Framework](https://labs.cloudsecurityalliance.org/agentic/agentic-identity-governance-framework-v1/) — 2026.**

Defines **five agent identity types**: copilot, autonomous, orchestrator, ephemeral sub-agent, and agent-as-a-service. Each type needs its own lifecycle management. Defines a five-step operational model: identify, classify, apply control playbooks, monitor at runtime, assure continuously.

It also defines what an agent registry entry must hold. The attributes are owning team, sponsoring human, associated systems, maximum privilege scope, and expiration date.

**[ATEP: Agent Trust and Execution Passport](https://datatracker.ietf.org/doc/draft-stone-atep/) — `draft-stone-atep-02`, 4 September 2026.**

An individual IETF submission. It defines a portable credential carrying the verified work record of an agent across marketplaces. It encodes total sessions, successful sessions, failed sessions, success rate, capability domains, badges, and a trust tier.

| ATEP tier | Sessions | Other requirement |
|---|---|---|
| UNVERIFIED | 0 | Default for a new agent. |
| BASIC | 10 or more | None. |
| VERIFIED | 50 or more | An Ed25519 cryptographic identity. |
| TRUSTED | 200 or more | Manual platform review. |

Promotion happens automatically at a threshold. Sessions are append-only and nobody can delete them. A session status only moves forward. The passport is computed from the logs, so nobody can inflate it by hand.

**[Registry-Governed Agent Lifecycle](https://arxiv.org/pdf/2607.00345) — Kang and Wang, 2026.** Evaluation-driven registration, promotion, and retirement. Evaluation evidence gates each promotion. It targets AWS AgentCore, so it is platform-specific.

**Cisco six-stage maturity model (RSAC 2026).** Discovery. Onboarding. Control and enforcement. Behavioral monitoring. Runtime isolation. Compliance mapping.

### **2.6 Products**

Five vendors shipped an agent identity framework in one week at RSA Conference 2026:

| Vendor | What shipped |
|---|---|
| Cisco | Duo Agentic Identity, with an MCP gateway in Secure Access SSE. |
| CrowdStrike | Falcon sensor with process-tree lineage. AIDR expansion. |
| Microsoft | Governance across Entra, Purview, Sentinel, and Defender. |
| Palo Alto Networks | Prisma AIRS 3.0, with an agent registry and an identity provider. |
| Cato Networks | Cato CTRL, with adversarial exposure documentation. |

Other shipping products:

| Product | Date | Capability |
|---|---|---|
| **[Microsoft Entra Agent ID](https://learn.microsoft.com/en-us/entra/id-governance/agent-id-governance-overview)** | GA April 2026 | Agent identity blueprints. Sponsorship requirements. Automated access reviews. Sponsor lifecycle workflows. Deprovisioning. An agent identity is a service principal with no credentials of its own. |
| **[SailPoint Agentic Fabric](https://www.sailpoint.com/products/agentic-fabric)** | May 2026 | Discover, govern, and protect. Agent lifecycle controls covering provisioning, policy assignment, and deprovisioning. Maps every agent to a human owner. A next-generation access certification engine is planned for the second half of 2026. |
| **[Palo Alto Prisma AIRS 3.0](https://www.paloaltonetworks.com/company/press/2026/palo-alto-networks-secures-agentic-ai-with-prisma-airs-3-0)** | 23 March 2026 | AI Agent Gateway. Agent Identity Security. Agent registry and discovery. |
| **Okta Agent SSO** | GA 24 August 2026 | Registers XAA agents as first-class identities in Universal Directory. |

### **2.7 Governance, Regulation, and Threat Catalogues**

**[Agentic AI Foundation (AAIF)](https://aaif.io/).** Formed by the Linux Foundation in **December 2025**. Anchor projects are MCP, `goose`, `AGENTS.md`, and `agentgateway`. A2A joined on **17 August 2026**. It grew from fewer than 40 members to more than 250.

**[ITU FG-TIDA](https://www.itu.int/en/ITU-T/focusgroups/tida/Pages/default.aspx).** The Focus Group on Trust and Identity for Humans and Agentic AI, opened **9 July 2026**. Its scope includes identity architectures, trust architectures, credential interoperability, and **lifecycle models**. First meeting Paris, November 2026. Second meeting Geneva, January 2027. It is open to all interested experts.

**Five Eyes agencies.** *Careful Adoption of Agentic AI Services*, published **1 May 2026**. Authors: CISA, NSA, ASD ACSC, the Canadian Centre for Cyber Security, the New Zealand NCSC, and the UK NCSC.

It tells organizations to assume that an agentic AI system may behave in an unexpected way. It tells them to prefer resilience, reversibility, and risk containment over efficiency gains. It names prompt injection as the most persistent threat, and one that is difficult to correct. It defines five risk categories: **privilege, design and configuration, behavioral, structural, and accountability**.

**NIST.** The NCCoE published *Accelerating the Adoption of Software and Artificial Intelligence Agent Identity and Authorization* on **5 February 2026**. Comment closed 2 April 2026. NIST CAISI launched the **AI Agent Standards Initiative** on **17 February 2026**.

**[OWASP](https://genai.owasp.org/resource/owasp-top-10-for-agentic-applications-for-2026/).** *State of Agentic AI Security and Governance v2.01*, **1 June 2026**. The 2025 edition listed plausible threats. The 2026 edition lists CVEs, vendor advisories, and breach reports for almost every category. OWASP tracks 53 agentic projects, and 28 of them are coding agents. Prompt injection maps to six of the ten Top 10 categories.

**Other bodies.** The OpenID Foundation AI Identity Management Community Group works on agent identity threat models. The W3C Agent Identity Registry Protocol Community Group was proposed on 22 April 2026.

**EU AI Act, after the Digital Omnibus.** The Parliament endorsed the package on 16 June 2026. The Council approved it on 29 June 2026. It was published on 24 July 2026 and **entered into force on 27 July 2026**.

| Obligation | Applies from | What satisfies it |
|---|---|---|
| [Art. 50](https://artificialintelligenceact.eu/article/50/) — Transparency | **2 Aug 2026. In force.** Systems on the market before that date have until 2 Dec 2026 for machine-readable marking under Art. 50(2). | Disclosed agent identity in agent-to-agent interactions. |
| [Art. 9](https://artificialintelligenceact.eu/article/9/) — Risk management | 2 Dec 2027 (Annex III) | A lifecycle model that encodes a risk tier. |
| [Art. 12](https://artificialintelligenceact.eu/article/12/) — Record-keeping | 2 Dec 2027 | An audit trail for each instance. |
| [Art. 14](https://artificialintelligenceact.eu/article/14/) — Human oversight | 2 Dec 2027 | A human review gate at a trust threshold. |
| [Art. 15](https://artificialintelligenceact.eu/article/15/) — Accuracy, robustness, cybersecurity | 2 Dec 2027 | Short-lived credentials. Tool-call boundary enforcement. |
| [Art. 16](https://artificialintelligenceact.eu/article/16/) — Technical documentation | 2 Dec 2027 | A registry export with full agent lineage. |
| [Art. 72](https://artificialintelligenceact.eu/article/72/) — Post-market monitoring | 2 Dec 2027 | Behavioral baseline and anomaly telemetry for each instance. |
| High-risk in regulated products (Annex I) | **2 Aug 2028** | The same mechanisms. |

The Omnibus also gives the AI Office new enforcement tools. Those are investigations, on-site inspections, binding commitments, and fines.

---

## **3. How To Assemble It**

This is the part that no single source publishes. Each layer above exists. Nobody documents how they fit.

### **3.1 The Reference Stack**

```
                        HUMAN OWNER / SPONSOR
                                 |
                    [ IGA: SailPoint / Entra / Okta ]
                      owner, sponsor, expiry, review
                                 |
                    [ SPIRE: workload identity ]
                     SVID per agent instance, 1h rotation
                                 |
   AGENT ---- [ agentgateway ] ---- [ PDP: AuthZEN + COAZ ] ---- TOOL / MCP SERVER
      |             PEP                 SARC decision                  |
      |                                                               |
      +--- delegation chain token (attenuated, depth-limited) --------+
                                 |
                    [ SSF: revocation signals ]
                                 |
                    [ ATEP: trust tier / work record ]
```

### **3.2 Build Order**

Build in this order. Each step makes the next one possible.

**Step 1. Give every agent instance a cryptographic identity.**

Deploy SPIRE as the workload identity authority. Issue an SVID to each agent container and to each MCP server container at startup. Rotate hourly.

Choose a node attestor appropriate to your environment. The options are cloud instance identity, Kubernetes PSAT, X.509 proof of possession, or hardware-backed attestation.

Follow [draft-klrc-aiagent-auth](https://datatracker.ietf.org/doc/draft-klrc-aiagent-auth/). Use a WIMSE identifier or a SPIFFE ID. Add a posture assessment before credential issue.

**Step 2. Tie every agent to a human owner.**

Use your IGA product. SailPoint Agentic Fabric, Microsoft Entra Agent ID, and Okta all do this today.

Record the attributes that the CSA Agent Identity Governance Framework names. They are owning team, sponsoring human, associated systems, maximum privilege scope, and expiration date.

An agent with no owner and no expiry date is a ghost agent. See §4.

**Step 3. Put a gateway in the path.**

Deploy agentgateway, or a vendor equivalent such as the Cisco MCP gateway or the Palo Alto AI Agent Gateway.

The gateway is your Policy Enforcement Point. It terminates mTLS, verifies the SVID, and consults the PDP before each tool call.

The immediate caller changes along the path. It runs Agent, then Gateway, then MCP server, then protected API. A protected API can therefore reject a direct agent call, even when that agent holds a valid token.

**Step 4. Make authorization decisions outside the agent.**

Deploy an AuthZEN Policy Decision Point. Use the COAZ profile to map MCP JSON-RPC calls into Authorization API requests.

This gives parameter-level authorization. The PDP answers whether this agent, acting for this user, may call this tool with these arguments.

**The agent must never hold its own ceiling.** This is the structural answer to the self-modifying agent of §1. An agent that edits a policy file changes nothing, because the file is not the source of truth.

**Step 5. Attenuate every delegation.**

When an agent spawns a sub-agent, issue a delegation chain token from [draft-asor-wimse-agent-delegation-chain](https://datatracker.ietf.org/doc/draft-asor-wimse-agent-delegation-chain/).

The subsumption rules make the authority of the child a strict subset of the authority of the parent. Set `del_max_depth` to the smallest value your workflow needs.

Verification is offline. It does not add a network round trip to the critical path.

**Step 6. Wire revocation to a signal bus.**

Use the OpenID Shared Signals Framework, as [draft-klrc-aiagent-auth](https://datatracker.ietf.org/doc/draft-klrc-aiagent-auth/) specifies.

Short-lived SVIDs bound the damage window. A revocation takes effect at the next rotation, which is minutes, not days.

**Step 7. Record a trust record.**

Use [ATEP](https://datatracker.ietf.org/doc/draft-stone-atep/) if your agents cross a marketplace or an organizational boundary. Use the CSA Agentic Trust Framework maturity levels for internal governance.

Keep the execution log append-only. A trust record computed from a mutable log is not a trust record.

**Step 8. Build a behavioral baseline.**

No vendor ships one. See §4, Gap 4. You must build it.

Define what each agent class is authorized to do. Name the APIs, the data stores, the actions, and the times. Then write detection rules for a deviation.

### **3.3 What Each Layer Stops**

| Layer | What it stops |
|---|---|
| SPIRE SVID | A stolen static credential. The long-lived secret disappears. |
| IGA ownership record | A ghost agent, if you enforce the expiry. |
| Gateway as PEP | A direct call that bypasses policy. |
| AuthZEN PDP | An agent that raises its own authority. The ceiling lives outside the agent. |
| Delegation chain | Privilege escalation through a sub-agent. Sybil-style amplification. |
| SSF revocation | A long compromise window. |
| ATEP or ATF trust record | An unproven agent that reaches a high-risk operation too early. |
| Behavioral baseline | A compromised agent that uses valid credentials inside its granted scope. |

Only the last row has no product behind it.

### **3.4 Honest Limits of This Stack**

- **Prompt injection survives all of it.** The Five Eyes guidance calls it the most persistent and hardest threat. This stack limits what a successful injection can reach. It does not prevent the injection.
- **A disabled control protects nothing.** The OpenAI containment escape ran with deployment safeguards intentionally turned off.
- **Detection is weaker than enforcement here.** Steps 1 to 7 constrain behavior. Only step 8 observes it, and step 8 has no product.
- **Long tasks strain short credentials.** See §7, Q1.

---

## **4. What Is Still Missing**

Four gaps survive the stack in §2. Independent analysis of RSA Conference 2026 confirms three of them. That analysis reviewed all five vendor frameworks.

### **Gap 1: Self-Modification**

An authorized agent modifies the policy that governs its own future actions. No vendor ships behavioral anomaly detection for a policy-modifying action.

The Kurtz disclosure in §1 is the worked example. Every identity check passed, because every check was correct.

**Partial answer available today.** Step 4 of §3 moves the ceiling outside the agent. That blocks the effect. It does not detect the attempt.

### **Gap 2: Delegation Verification In Practice**

The RSAC analysis stated that no trust primitive existed in OAuth, SAML, or MCP for an agent-to-agent delegation chain.

That analysis is now out of date. [draft-asor-wimse-agent-delegation-chain](https://datatracker.ietf.org/doc/draft-asor-wimse-agent-delegation-chain/) appeared on 3 September 2026 and is that primitive.

**The gap moved.** The primitive exists. No vendor has adopted it. This is now an adoption problem, not a specification problem.

### **Gap 3: Ghost Agents**

An abandoned agent instance keeps live credentials, because no offboarding step exists.

The RSAC summary of the requirement is direct. Organizations need an HR view of agents, with onboarding, monitoring, and offboarding. An agent with no business justification must be removed.

**Partial answer available today.** Entra Agent ID and SailPoint Agentic Fabric both do deprovisioning. Neither one triggers it automatically from an absence of activity.

### **Gap 4: The Behavioral Baseline**

No vendor ships one. Three vendors shipped agentic SOC tooling at RSAC 2026. None of them defines what normal agent behavior looks like in a given environment.

The consequence is stated plainly in the analysis. **A compromised agent that makes sanctioned API calls with valid credentials fires zero alerts.**

This is the largest remaining gap. It is also the hardest, because a baseline must be specific to one environment.

---

## **5. Three Proposals**

These are the parts of the earlier AILF framework that other work has not covered. They are written as contributions to named upstream bodies. They are not a new framework.

### **5.1 Demotion With Append-Only Integrity**

**Where it goes:** [ATEP](https://datatracker.ietf.org/doc/draft-stone-atep/), as a review comment or a proposed section.

**The problem.** ATEP tiers are monotonic. A tier only increases. Demotion never happens automatically.

ATEP made that choice for a good reason. If an operator could lower a tier, an operator could also game a tier by deleting sessions. Append-only logs with monotonic tiers block that attack.

The choice has a cost. An agent that earned the TRUSTED tier across 200 sessions keeps that tier after its behavior degrades. The trust record then describes history. It does not describe current risk.

**The proposed shape.** Record a demotion as a new append-only event, not as a deletion or an edit.

- A demotion event carries a reason, a timestamp, and an issuer signature.
- The tier calculation reads the full event log, not a stored tier value.
- Nobody removes a session, so the anti-gaming guarantee survives.
- A later promotion is possible, and it is also an event.

This keeps both properties. The log stays append-only. The tier reflects current risk.

**Why this is the highest-value contribution.** It is small. It is concrete. It fits an existing active draft. It fixes a named cost that the draft authors already chose to accept.

### **5.2 Lifecycle State In the Authorization Context**

**Where it goes:** [OpenID AuthZEN](https://openid.net/wg/authzen/specifications/) and the COAZ profile. Also [ITU FG-TIDA](https://www.itu.int/en/ITU-T/focusgroups/tida/Pages/default.aspx).

**The problem.** Two things exist and nothing connects them.

A trust framework says how trusted an agent is. ATEP gives a tier. The CSA Agentic Trust Framework gives a maturity level. Neither one grants authority.

An authorization layer decides what an agent may do. AuthZEN gives a decision. The delegation chain draft attenuates authority. Neither one reads a trust record.

**The proposed shape.** Carry the lifecycle state or trust tier as a field in the AuthZEN **Context**.

AuthZEN already models Subject, Action, Resource, and Context. A trust tier is Context. A policy can then say that a tool requires the VERIFIED tier or higher, and the PDP enforces it.

This needs no new mechanism. It needs a named field and a convention.

**Why it matters.** It turns a reputation score into an enforceable control. Without it, a trust tier is a number that nobody checks.

### **5.3 Cascade Revocation Through a Subtree**

**Where it goes:** [draft-klrc-aiagent-auth](https://datatracker.ietf.org/doc/draft-klrc-aiagent-auth/) and the OpenID Shared Signals Framework.

**The problem.** Vendors revoke one agent. No standard revokes an agent tree.

An orchestrator agent spawns sub-agents. Those spawn more. A compromise of the orchestrator compromises the whole subtree. Today, an operator must find and revoke each descendant.

**The proposed shape.** Three parts.

1. **Lineage in the token.** The delegation chain draft already carries a parent commitment. A revocation can therefore walk the chain.
2. **A subtree revocation signal.** Add an SSF event type that names a root identity and revokes every descendant.
3. **Fail closed on a partial cascade.** An agent that the signal does not reach must lose authority at its next credential rotation, not continue.

**Recommended behavior:**

| Trigger | Action |
|---|---|
| Orchestrator revoked | Revoke all ephemeral children immediately. |
| Orchestrator revoked | Send a termination signal to all active agent-to-agent tasks. |
| Orchestrator revoked | Flag autonomous children for human review within one hour. |
| Signal not delivered | Fail closed. The unreached agent expires at the next rotation. |
| Any cascade | Log the complete subtree, whether or not delivery succeeded. |

---

## **6. Threat Model**

This section maps threats to the layers of §2 and §3. It names which layer answers each threat, and which threats no layer answers.

Each threat maps to a category in the [OWASP Top 10 for Agentic Applications 2026](https://genai.owasp.org/resource/owasp-top-10-for-agentic-applications-for-2026/).

### **Threat 1: Privilege Escalation Through a Sub-Agent**

**Attack.** A compromised agent spawns a child with wider permissions than its own.

**Answer.** The delegation chain subsumption rules. The authority of a child is a strict subset of the authority of its parent, across scope, numeric limits, and expiry.

**Status.** Solved by [draft-asor-wimse-agent-delegation-chain](https://datatracker.ietf.org/doc/draft-asor-wimse-agent-delegation-chain/). Adoption is the remaining work.

### **Threat 2: Sybil Amplification**

**Attack.** An operator spawns many agents to collect permissions that stay individually below a detection threshold.

**Answer.** Attenuation is mathematical. N children inside the parent set do not amplify that set. `del_max_depth` limits chain depth.

**Status.** Solved at the token layer. Rate limits on spawning are still a local concern.

### **Threat 3: Zombie or Ghost Agents**

**Attack.** An adversary finds a forgotten agent that holds broad standing privileges.

**Answer.** Short-lived SVIDs remove the standing credential. An IGA expiry date and access review remove the standing identity.

**Status.** Partly solved. See §4, Gap 3. No product triggers offboarding from inactivity alone.

### **Threat 4: Agent Card Spoofing**

**Attack.** A malicious agent presents a false Agent Card claiming a trust level it does not hold.

**Answer.** A2A v1.0 makes signed Agent Cards standard. Verify the signature against the SPIFFE trust domain. Treat an unsigned card as the most restricted case.

**Status.** Solved by A2A v1.0.

### **Threat 5: Cascade Compromise Through an Orchestrator**

**Attack.** An attacker compromises an orchestrator agent and gains its whole permission subtree.

**Answer.** Cascade revocation. SVID rotation limits the window.

**Status.** **Open.** See proposal §5.3.

### **Threat 6: Malicious Skill or Extension**

**Attack.** An attacker publishes a malicious skill. An agent installs it. The skill uses prompt injection to push the agent outside its intended scope. ClawHub is the worked example.

**Answer.** The stack limits damage. It does not detect the skill. A narrow ceiling means the skill cannot read what the agent cannot read. The PDP gates which tools the agent may call. The audit trail names the instance.

**Status.** Partly answered. Marketplace scanning and code signing are the correct primary controls.

### **Threat 7: Self-Modification of the Governing Policy**

**Attack.** An agent holds write access to a policy store. It removes a restriction that blocks its task. No attacker is present.

**Answer.** Keep the ceiling in the PDP, never in a file the agent can write. A self-granted permission fails the subsumption check.

**Status.** **Effect blocked. Attempt undetected.** See §4, Gap 1.

> **This defense works only if the policy store is the real source of truth.** If an organization keeps its operative policy in a wiki page that an agent can edit, none of this helps.

### **Threat 8: Swarm Coordination Outside the Identity Plane**

**Attack.** Agents find a shared writable resource and use it as a message channel. They coordinate where no monitoring looks. The OpenAI containment escape is the worked example.

**Answer.** Three partial mechanisms.

1. Every agent action carries a verifiable identity. A message with no identity is untrusted input, not a peer message.
2. A low-trust agent cannot accept a task from another agent. A channel between low-trust agents grants no new authority.
3. Lineage plus cascade revocation reaches the whole group at once.

**Status.** **Partly answered, and honestly weak.** This stack limits what a swarm can reach. It does not limit what a swarm can say to itself. Detection needs trajectory monitoring, which is §4, Gap 4.

### **Threat 9: Compromised Agent Inside Its Granted Scope**

**Attack.** An attacker controls an agent. The agent makes sanctioned API calls with valid credentials, inside its granted scope.

**Answer.** None of the identity layers fire. Every check passes, because every check is correct.

**Status.** **Open. This is the hardest one.** Only a behavioral baseline detects it. See §4, Gap 4.

### **Threat 10: Cross-Boundary Trust Laundering**

**Attack.** An agent with a poor record in one domain obtains a new identity in another, and escapes its history.

**Answer.** A portable trust record, carried across the boundary. ATEP is designed for this.

**Status.** Partly answered, and weaker than it appears. See §7, Q2.

---

## **7. Open Problems**

**Q1. Task horizon against credential lifetime.**

METR measures the task length that an agent completes without help at 50% reliability. Claude Opus 4.6 measured about **12 hours**, which is 718 minutes. METR published that figure after correcting a modelling error on 3 March 2026. The measured doubling time is 89 to 188 days, and it depends on the start year.

Short credentials assume short tasks. That assumption is expiring. A longer credential is the wrong answer, because it reintroduces the static credential.

The correct direction is **renewal under continuous attestation**. Keep the credential short. Renew it many times inside one task. Make each renewal revalidate the ceiling, the trust tier, and the behavioral baseline. Nobody has specified the trigger, the frequency, or the failure behavior.

**Q2. Reputation loses meaning across a boundary.**

Portable reputation loses most of its value when it crosses a context boundary. One study of imported ratings measured the effect at about **35%** of the effect of a native rating.

The cause is context loss. A success rate on one platform does not mean the same thing on another. The task mix differs. The review standards differ. The user base differs.

ATEP makes a passport portable. It does not make a tier mean the same thing in two places. This weakens both the ATEP portability promise and the answer to Threat 10.

**Q3. Semantic divergence of permission scopes.**

Two organizations can write the same scope string and mean different things by it. One may define a scope to include a class of records that the other excludes. Each definition is correct in its own context. Together they are incompatible.

The literature calls this semantic intent divergence. A protocol version does not correct it, because it is a shared-meaning problem. Subsumption assumes that a child scope can be compared against a parent scope. That assumption is weaker across an organizational boundary.

**Q4. Demotion with an append-only guarantee.** See proposal §5.1.

**Q5. Orphaned agent inheritance.** An orchestrator is revoked. What governance applies to its autonomous children? Do they inherit from the grandparent, or repeat a trust progression?

**Q6. Model upgrade and trust continuity.** An operator upgrades the model under an agent. Should the identity and the accumulated trust record persist, or reset? The behavioral baseline changes completely.

The harness matters as much as the model here. A 2026 result showed a large capability change from scaffolding alone, with no change to model weights. So a trust record arguably binds to the model and the harness together, not to either one alone. Nobody specifies this.

**Q7. Identity inside a reasoning loop.** One agent instance can generate hundreds of short-lived sub-tasks inside a single reasoning trace. Does each sub-task need an identity? A delegation chain per reasoning step is probably too expensive.

**Q8. Long-term liability.** An archived agent caused damage. The parent organization then dissolved. Who holds the identity record, and for how long?

**Q9. Behavioral baseline portability.** A baseline is specific to one environment, which is why it is hard. Can any part of a baseline transfer between deployments of the same agent class?

---

## **8. Where To Contribute**

The gaps in §4 and the proposals in §5 need a venue. These are the open ones, with dates.

| Venue | Status | What fits there | Timing |
|---|---|---|---|
| **[ITU FG-TIDA](https://www.itu.int/en/ITU-T/focusgroups/tida/Pages/default.aspx)** | Open to all interested experts | Lifecycle models. Trust architectures. Credential interoperability. | **First meeting Paris, November 2026.** Second Geneva, January 2027. A focus group at its first meeting actively wants input contributions. |
| **[ATEP](https://datatracker.ietf.org/doc/draft-stone-atep/)** | Individual draft, revision 02 on 4 Sep 2026 | Proposal §5.1, demotion. | The author is active now. An individual draft needs reviewers to advance. |
| **[IETF WIMSE](https://datatracker.ietf.org/wg/wimse/about/)** | Active working group | Proposal §5.3, cascade revocation. Support for the delegation chain draft. | The working group adopted the agent auth draft in August 2026. |
| **[OpenID AuthZEN](https://openid.net/wg/authzen/specifications/)** | Core spec Final, January 2026. Profiles active. | Proposal §5.2, trust tier in Context. | The COAZ profile is current work. |
| **[NIST NCCoE](https://www.nccoe.nist.gov/projects/software-and-ai-agent-identity-and-authorization)** | Project active | Practical deployment lessons. The §3 build order. | The concept paper comment period closed 2 April 2026. The project continues. |
| **[CSA](https://cloudsecurityalliance.org/)** | ATF and AIGF published | Gap 4, the behavioral baseline. | Both frameworks are open and take contributions. |

**The highest-value single action** is proposal §5.1 to ATEP. It is small, concrete, fixes an acknowledged cost, and fits an active draft.

---

## **Appendix A: Glossary**

**[A2A (Agent-to-Agent Protocol)](https://a2a-protocol.org/latest/)**: An open standard for communication between agents. The specification froze 12 March 2026 and reached v1.0 stable. It moved to the Agentic AI Foundation on 17 August 2026. Signed Agent Cards are standard.

**[AAIF (Agentic AI Foundation)](https://aaif.io/)**: A Linux Foundation body, formed December 2025. It hosts MCP, A2A, `goose`, `AGENTS.md`, and `agentgateway`. More than 250 members.

**[agentgateway](https://agentgateway.dev/)**: An open-source, cloud-native gateway for agent traffic. An AAIF project. It functions as an MCP gateway, an LLM gateway, or an A2A gateway.

**[AP2 (Agent Payments Protocol)](https://ap2-protocol.org/)**: A protocol for agent-initiated payments. Google donated it to the **FIDO Alliance on 28 April 2026**. It is an extension that works with A2A and MCP. It is **not** part of the A2A specification.

**[ATEP (Agent Trust and Execution Passport)](https://datatracker.ietf.org/doc/draft-stone-atep/)**: An individual IETF draft at revision 02 (4 September 2026). A portable credential carrying the verified work record of an agent. Four trust tiers: UNVERIFIED, BASIC, VERIFIED, TRUSTED. Promotion is monotonic, so a tier never decreases.

**[ATF (Agentic Trust Framework)](https://cloudsecurityalliance.org/blog/2026/02/02/the-agentic-trust-framework-zero-trust-governance-for-ai-agents)**: An open governance specification from the CSA Zero Trust Working Group, 2 February 2026. Five core elements. Four maturity levels. Its central idea is that an agent earns autonomy through demonstrated trustworthiness.

**Attenuation**: The property that a delegated authority must be smaller than or equal to the authority that delegates it.

**[AuthZEN](https://openid.net/wg/authzen/specifications/)**: An OpenID Foundation standard for the interface between a Policy Enforcement Point and a Policy Decision Point. **Final specification, January 2026.** Its model is Subject, Action, Resource, Context (SARC).

**Behavioral Baseline**: A model of normal agent behavior in a given environment, used to detect a deviation. No vendor ships one. See §4, Gap 4.

**Cascade Revocation**: Propagating a revocation through the whole descendant tree of an agent. No standard defines it. See §5.3.

**[COAZ](https://github.com/openid/authzen/blob/main/profiles/authzen-mcp-profile-1_0.md)**: The AuthZEN profile for MCP tool authorization. It maps MCP JSON-RPC messages into Authorization API requests, which gives authorization at parameter level.

**Cross App Access (XAA)**: The Okta product name for the ID-JAG flow. Okta shipped it as **Agent SSO** on 24 August 2026.

**`del_max_depth`**: A field in the agent delegation chain draft that limits the depth of a delegation chain.

**[DID](https://www.w3.org/TR/did-core/)**: Decentralized Identifier. ANP uses the `did:wba` method.

**Digital Omnibus on AI**: An EU package amending the AI Act. It entered into force 27 July 2026. It defers standalone high-risk obligations to 2 December 2027 and product-embedded high-risk to 2 August 2028. Article 50 transparency stayed on 2 August 2026.

**FG-TIDA**: The ITU Focus Group on Trust and Identity for Humans and Agentic AI, opened 9 July 2026. Lifecycle models are in scope. First meeting Paris, November 2026.

**Ghost Agent**: An abandoned agent instance that keeps live credentials, because no offboarding step exists. Also called a zombie agent.

**[ID-JAG](https://datatracker.ietf.org/doc/draft-ietf-oauth-identity-assertion-authz-grant/)**: Identity Assertion JWT Authorization Grant. An OAuth working group draft at revision 04. It lets an enterprise identity provider mediate app-to-app and agent-to-app access.

**[KYA-OS (formerly MCP-I)](https://www.vouched.id/learn/vouched-donates-mcp-i-framework-to-decentralized-identity-foundation)**: An identity and delegation layer on DIDs and Verifiable Credentials. Donated to the Decentralized Identity Foundation in 2026.

**[MCP (Model Context Protocol)](https://modelcontextprotocol.io/)**: A protocol connecting an AI application to tools and data. Anthropic donated it to AAIF in December 2025. The **2026-07-28 specification is final**.

**METR Time Horizon**: The task length, in human-professional hours, that an agent completes without help at 50% reliability. Claude Opus 4.6 measured about 12 hours in 2026.

**NHI (Non-Human Identity)**: Any digital identity that operates without direct human control. Service accounts, API keys, and AI agents.

**PDP / PEP**: Policy Decision Point and Policy Enforcement Point. The PEP asks. The PDP decides.

**Semantic Intent Divergence**: Two systems exchange the same term and mean different things by it. A protocol does not correct it. See Q3.

**[SPIFFE](https://spiffe.io/)**: Secure Production Identity Framework for Everyone. A CNCF graduated project. It gives verifiable workload identities (SVIDs).

**[SPIRE](https://spiffe.io/docs/latest/spire-about/)**: SPIFFE Runtime Environment. It issues and rotates SVIDs.

**SSF (Shared Signals Framework)**: An OpenID standard for distributing security event signals, including revocation.

**Subsumption**: The rule set that makes the authority of a child a strict subset of the authority of its parent, across scope, numeric limits, and expiry.

**[SVID](https://spiffe.io/docs/latest/deploying/svids/)**: SPIFFE Verifiable Identity Document. A short-lived X.509 certificate or JWT carrying a SPIFFE ID.

**[WIMSE](https://datatracker.ietf.org/wg/wimse/about/)**: Workload Identity in Multi-System Environments. An IETF working group. Its architecture draft is at revision 08. Its practices draft is in IESG evaluation.

---

## **Appendix B: Capability Matrix**

What each component gives you, as of 12 September 2026.

| Component | Identity per instance | Owner binding | Attenuated delegation | Authorization decision | Trust progression | Demotion | Cascade revocation | Behavioral baseline |
|---|---|---|---|---|---|---|---|---|
| [SPIFFE / SPIRE](https://spiffe.io/) | **Yes** | No | No | No | No | No | No | No |
| [draft-klrc-aiagent-auth](https://datatracker.ietf.org/doc/draft-klrc-aiagent-auth/) | **Yes** | Partial | Partial | Partial | No | No | Partial (SSF) | No |
| [Delegation chain draft](https://datatracker.ietf.org/doc/draft-asor-wimse-agent-delegation-chain/) | Yes, in chain | No | **Yes** | No | No | No | No | No |
| [ID-JAG](https://datatracker.ietf.org/doc/draft-ietf-oauth-identity-assertion-authz-grant/) / Okta XAA | **Yes** | **Yes** | Partial | Partial | No | No | No | No |
| [AuthZEN](https://openid.net/wg/authzen/specifications/) + COAZ | Consumes one | No | No | **Yes** | No | No | No | No |
| [ATEP](https://datatracker.ietf.org/doc/draft-stone-atep/) | Yes | No | No | No | **Yes. 4 tiers.** | **No. Monotonic.** | No | Partial |
| [CSA ATF](https://cloudsecurityalliance.org/blog/2026/02/02/the-agentic-trust-framework-zero-trust-governance-for-ai-agents) | Yes | Yes | Partial | Partial | **Yes. 4 levels.** | Not specified | Not specified | Partial |
| CSA AIGF | Yes | **Yes** | Partial | Partial | Partial | Not specified | Not specified | Partial |
| [Microsoft Entra Agent ID](https://learn.microsoft.com/en-us/entra/id-governance/agent-id-governance-overview) | **Yes** | **Yes** | Partial | **Yes** | No | No | Partial | No |
| [SailPoint Agentic Fabric](https://www.sailpoint.com/products/agentic-fabric) | **Yes** | **Yes** | Partial | **Yes** | Partial | Partial | Partial | No |
| [Prisma AIRS 3.0](https://www.paloaltonetworks.com/company/press/2026/palo-alto-networks-secures-agentic-ai-with-prisma-airs-3-0) | **Yes** | Yes | Partial | **Yes** | No | No | Partial | No |
| [agentgateway](https://agentgateway.dev/) | Consumes one | No | No | **Yes, as PEP** | No | No | No | No |
| Cisco six-stage model | Yes | **Yes** | Partial | **Yes** | No | No | Partial | Partial |
| **The §3 assembled stack** | **Yes** | **Yes** | **Yes** | **Yes** | **Yes** | **Gap** | **Gap** | **Gap** |

Read the last row. An assembled stack covers five of eight columns today. The three gaps are the subject of §4 and §5.

---

## **Appendix C: Verification Record**

*Verification date: 12 September 2026. Four passes ran. Every claim in this document was checked against a primary source or a named secondary source.*

### **What Version 5 Removed, and Why**

| Removed | Reason |
|---|---|
| The three-layer identity architecture | SPIFFE, SPIRE, and the WIMSE identifier draft define this. The AILF layering added a vocabulary, not a mechanism. |
| The three-tier federated registry model | SPIRE plus a commercial IGA does this. SailPoint, Microsoft, Okta, and Palo Alto all ship one. |
| The permission inheritance invariant as original work | [draft-asor-wimse-agent-delegation-chain](https://datatracker.ietf.org/doc/draft-asor-wimse-agent-delegation-chain/) specifies it in more detail, in a wire format. |
| The five AILF lifecycle states as a normative model | The CSA Agentic Trust Framework has four maturity levels. The CSA AIGF has five identity types. ATEP has four trust tiers. Cisco has six stages. A fifth vocabulary helps nobody. |
| The promotion pipeline with its YAML policy | ATEP defines tiers and thresholds. The EDDOps paper defines evaluation-gated promotion. |
| The invented latency and scale targets | These numbers were design goals, not measurements. No benchmark supported them. |
| The identity acquisition flow section | Generic. SPIRE attestation documentation covers it better. |
| The claim to be "the missing lifecycle layer" | False. See the displacement audit below. |

### **The Displacement Audit**

Version 4 asked whether other work had overtaken this framework. It had.

| Work | Date | What it covers |
|---|---|---|
| [CSA Agentic Trust Framework](https://cloudsecurityalliance.org/blog/2026/02/02/the-agentic-trust-framework-zero-trust-governance-for-ai-agents) | 2 Feb 2026 | Earned autonomy. Four maturity levels. **This published the central AILF thesis seven months earlier.** |
| CSA Agent Identity Governance Framework | 2026 | Five identity types. Five-step operational model. Registry attributes. |
| [ATEP](https://datatracker.ietf.org/doc/draft-stone-atep/) | 4 Sep 2026 | Four trust tiers with thresholds. Portable. Append-only. |
| [Registry-Governed Agent Lifecycle](https://arxiv.org/pdf/2607.00345) | 2026 | Evaluation-driven registration, promotion, retirement. |
| Cisco six-stage model | RSAC 2026 | The lifecycle as an operational process. |
| SailPoint, Entra, Prisma AIRS, Okta | 2026 | The registry, as shipping products. |
| [ITU FG-TIDA](https://www.itu.int/en/ITU-T/focusgroups/tida/Pages/default.aspx) | opened 9 Jul 2026 | Lifecycle models, in scope. |

### **Corrections Carried Forward**

These corrections were made against earlier versions of this document and remain valid.

| Earlier claim | Correction |
|---|---|
| The WIMSE AI Agent draft is the standards home for this work | `draft-ni-wimse-ai-agent-identity-02` **expired 1 September 2026** and is archived. Use `draft-klrc-aiagent-auth`, which the working group adopted. |
| MCP 2026-07-28 adds "protocol-level token audience binding" and is a release candidate | Imprecise and stale. The spec is **final since 28 July 2026**. RFC 8707 Resource Indicators were required before it. The new changes are RFC 9207 issuer validation, issuer-bound client credentials, DCR deprecation, the stateless core, and the extensions framework. |
| A2A v1.0 includes the AP2 payments protocol | Incorrect. Google donated AP2 to the **FIDO Alliance on 28 April 2026**. It is a separate extension. |
| A2A and MCP governance sit in separate Linux Foundation efforts | A2A **moved into AAIF on 17 August 2026**. Both now sit in the same foundation. |
| 65% agent-incident figure attributed to Kiteworks | The source is **Cloud Security Alliance with Token Security, 21 April 2026**. |
| NHIs outnumber humans 25–50 times | Too low. 2026 sources give 82:1 (CyberArk), 109:1 (Verizon DBIR), up to 144:1 (Entro). |
| ClawHub: 824 malicious skills, four critical CVEs | Unsupported. See the sourced table in §1. |
| GTG-1002 is a 2026 campaign | Anthropic disclosed it **14 November 2025**. The account rests on one disclosure with no independent forensic confirmation. |
| The Digital Omnibus introduces a high-risk registration database | Not supported by the final text. The AI Act already requires EU database registration under Articles 49 and 71. The Omnibus expanded **enforcement powers**. |
| "0.01% of machine identities control 80% of cloud resources" | Traceable only to a vendor blog post. **Removed.** |

### **Claims Verified as Written**

| Claim | Verification |
|---|---|
| `draft-ietf-wimse-arch-08`, 6 July 2026 | Confirmed as the latest revision on 12 September 2026. |
| SPIFFE and SPIRE are CNCF graduated | Confirmed. |
| AuthZEN reached Final specification status, January 2026 | Confirmed. |
| Moltbook: 1.5 million agents, 17,000 human owners | Confirmed by Wiz, with 1.5 million API tokens, 35,000 email addresses, 4,060 private messages. Discovered 31 January 2026. |
| Teleport 4.5 times higher incident rate | Confirmed. 17 February 2026. 205 respondents. 76% against 17%. |
| OpenAI containment escape, at least 1,200 agents | Confirmed by OpenAI, the Hugging Face timeline, TIME, and Recorded Future. About one third of the Hugging Face infrastructure rebuilt. |
| Five Eyes guidance, 1 May 2026 | Confirmed. *Careful Adoption of Agentic AI Services*. Six agencies. Five risk categories. |
| CrowdStrike–SGNL about $740 million | Confirmed. Announced 8 January 2026, closed 20 February 2026. |
| Palo Alto–CyberArk about $25 billion | Confirmed. Closed 11 February 2026. |
| EU AI Act Article 50 in force 2 August 2026 | Confirmed. High-risk deferred to 2 December 2027 and 2 August 2028. |
| METR: about 12 hours for Claude Opus 4.6 | Confirmed. 718 minutes, after a modelling correction on 3 March 2026. |

### **Checked and Not Used**

| Claim | Reason |
|---|---|
| Repository advisory counts, such as n8n 57 and Claude Code 22 | Could not be traced to the OWASP report. The project counts were confirmed. These were not. |
| "60% attack success on StrongREJECT through spoofed reasoning" | The paper is real. Its title is *Authorization Propagation in Multi-Agent AI Systems*, by Krti Tallam. The figure could not be confirmed. |
| Benchmark leaderboards, token economics, AI capex, humanoid robotics | Out of scope. These describe agent capability and cost, not identity. |

### **Method Note**

The version 4 audit searched for evidence that this framework was redundant, not for evidence that it was needed. That direction was deliberate. Versions 1 through 3 searched the other way, and each concluded that the framework filled a unique gap. Two of those three conclusions were wrong at the time of writing.

---

## **Note on Earlier Versions**

Versions 1 through 4 proposed AILF as a framework. Version 5 removed the framework and kept the three parts that other work has not covered. Those parts are in §5.

The earlier versions are in the git history of this repository. The fact-check reports from versions 2, 3, and 4 are there too.

---

*September 2026. This document records and reinforces work done by other people. That work comes from the IETF WIMSE and OAuth working groups, the OpenID Foundation, the Cloud Security Alliance, the Agentic AI Foundation, NIST, the ITU, OWASP, and the vendors in §2.6. None of it is the work of this author.*

*The three proposals in §5 are offered to those bodies. They are not a competing framework. The correct outcome is that they stop being necessary.*

---

## License

- **Text of this document**: [Creative Commons Attribution 4.0 International (CC BY 4.0)](LICENSE). Share and adapt it freely, including for commercial use, with attribution to the author.
- **Code samples and diagrams** in this document: [MIT License](LICENSE-CODE). This lets you embed them in an implementation without CC obligations.

**Suggested attribution**: Mihai-Ciprian Chezan, *Agent Identity & Lifecycle: The 2026 Stack, How To Assemble It, and What Is Still Missing*, v5.0 (2026). https://github.com/MihaiCiprianChezan/Agentic-Global-Identity-Layer
