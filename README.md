# Cyber Threat Intelligence

Independent cyber threat intelligence assessments, case studies and defensive analysis based on publicly available information.

## Purpose

This repository contains analyst-produced Cyber Threat Intelligence (CTI) reports and case studies. Its purpose is not simply to summarise incidents or reproduce existing threat research. Each assessment begins with an intelligence requirement and aims to turn available information into analysis that supports better security decisions.

The work explores adversary behaviour and motivation, attack paths, vulnerabilities, control assumptions and failures, detection opportunities, and wider defensive implications. Particular emphasis is placed on understanding **why an intrusion was possible**, **which assumptions may have failed**, and **what defenders should do differently as a result**.

This repository also documents my continued development across cyber threat intelligence, threat hunting, detection engineering, threat modelling and adversary tradecraft.

## Analytical Approach

The analytical process used here is **analyst-led and AI-assisted**.

I define the intelligence requirement, research the available evidence and make the final analytical judgements. AI may support research, source discovery, synthesis, analytical challenge, consideration of alternative explanations and drafting.

AI output is not treated as an authoritative source. Material factual claims are validated against underlying sources before publication. Reports distinguish between:

- **Established / reported facts** — information supported by cited sources.
- **Analytical assessments** — judgements derived from the available evidence.
- **Hypotheses** — plausible explanations that remain unconfirmed.
- **Intelligence gaps** — information that is unknown and may materially affect the assessment.

Material judgements are tested against available evidence and credible alternatives, with confidence expressed where uncertainty materially affects the assessment. Final conclusions, lessons and recommendations remain the responsibility of the analyst.

For the public analytical standard, see [methodology/analytical-methodology.md](methodology/analytical-methodology.md) and [methodology/confidence-language.md](methodology/confidence-language.md).

## Published Intelligence

| ID | Report | Intelligence Requirement | Published |
|---|---|---|---|
| **CTI-001** | [UNC6240 / ShinyHunters Exploitation of Oracle PeopleSoft](reports/CTI-001-UNC6240-PeopleSoft/README.md) | What does the UNC6240 PeopleSoft campaign and FBI compromise demonstrate about internet-accessible enterprise applications, compensating controls and application trust? | 6 Oct 2026 |

## Scope & Handling

All published assessments are based on publicly available information and independent analysis. This repository does not contain non-public information from any current or previous employer, client, customer or operational environment.

Professional experience may inform the questions asked and analytical techniques used, but it is not used as unpublished evidence for public assessments.

## Intended Outcome

The central question behind the work in this repository is:

> **What does the available intelligence mean for the defender, and what should they do differently as a result?**

The goal is to move beyond describing threat activity toward useful, defensible intelligence that connects adversary behaviour with detection, architecture, vulnerability management, incident response and security decision-making.

## Repository Structure

cyber-threat-intelligence/
├── README.md
├── methodology/
│   ├── analytical-methodology.md
│   └── confidence-language.md
├── reports/
│   ├── README.md
│   └── CTI-001-UNC6240-PeopleSoft/
│       └── README.md
└── sources/
    └── README.md

Individual published assessments receive their own folder under reports/, containing a web-readable report and, where useful, supporting material or a formal PDF.
