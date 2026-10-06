# Analytical Methodology

## Purpose

This document describes the analytical standard applied to intelligence assessments published in this repository. The objective is to produce decision-support intelligence rather than collections of threat reporting.

Each assessment begins with a defined intelligence requirement and aims to make clear what is known, what is assessed, what remains uncertain, and why the resulting judgement matters to defenders.

## Analytical Standard

### Intelligence Requirements

Assessments are driven by a defined intelligence question. The requirement establishes the problem being investigated and keeps collection and analysis focused on information that can support a security decision.

### Evidence and Sourcing

Collection prioritises primary and authoritative sources where available, including vendor research, vulnerability advisories, government reporting and direct technical evidence. Secondary reporting may provide additional context.

Material factual claims are validated against underlying sources and remain traceable through citations.

### Evidence, Assessment and Uncertainty

Published reports distinguish between:

- **Established / reported facts** — information supported by available source material.
- **Analytical assessments** — judgements derived from evidence and technical reasoning.
- **Hypotheses** — plausible but unconfirmed explanations.
- **Intelligence gaps** — information that remains unknown and could materially affect an assessment.

A technically plausible path is not treated as evidence that the path occurred in the incident being assessed.

### Analytical Challenge

Material judgements are tested against available evidence, underlying assumptions and credible alternative explanations. Confidence is assigned according to the quality, consistency and completeness of supporting information.

See [confidence-language.md](confidence-language.md) for the confidence terminology used in this repository.

### Defensive Relevance

Analysis should explain what adversary activity means for defenders. Lessons and recommendations are derived from the evidence and assessment and may inform areas such as detection engineering, threat hunting, vulnerability management, security architecture, identity, cloud security, incident response and control assurance.

## Human-Led, AI-Assisted

The analytical process is **human-led and AI-assisted**.

AI may support research, source discovery, synthesis, analytical challenge, consideration of alternative explanations and drafting. AI output is not treated as an authoritative source.

I remain responsible for defining the intelligence requirement, verifying material claims, evaluating uncertainty, and determining the final analytical judgements, conclusions and recommendations presented in each assessment.

Reports undergo source verification and analytical review before publication.

## Information Handling

Published material is limited to publicly available information and independent analysis. Non-public employer, client, customer or operational information is not used as evidence or included in this repository.

Published assessments represent point-in-time judgements based on the information available at the stated information cut-off. Material changes are documented through versioned updates rather than silently rewriting earlier assessments.
