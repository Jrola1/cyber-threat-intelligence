# Analytical Methodology

## Purpose

This document describes the analytical standard used for intelligence assessments published in this repository. The objective is to produce decision-support intelligence rather than collections of threat reporting.

A report should answer a defined intelligence question and make clear what is known, what is assessed, what remains hypothetical, and what information could change the assessment.

## Intelligence-Led Process

### 1. Define the Intelligence Requirement

Each assessment begins with a question. The requirement defines the problem being investigated, the intended decision-maker or defensive audience, and the decision the intelligence should help inform.

### 2. Collect and Evaluate Evidence

Collection prioritises primary and authoritative sources where available, including vendor research, vulnerability advisories, government reporting and direct technical evidence. Secondary reporting may provide additional context but should not silently replace the underlying source.

Material claims should remain traceable to their source.

### 3. Separate Evidence from Judgement

Reports deliberately distinguish four classes of information:

- **Established / reported fact:** directly supported by available source material.
- **Analytical assessment:** a judgement derived from evidence and technical reasoning.
- **Hypothesis:** a plausible but unconfirmed explanation requiring additional evidence.
- **Intelligence gap:** information that is currently unavailable and could affect the assessment.

The existence of a plausible technical path does not establish that the path occurred in the incident being assessed.

### 4. Develop and Challenge Hypotheses

Hypotheses are used to explore how or why observed activity may have occurred. A useful hypothesis records:

- the hypothesis itself;
- supporting evidence and technical basis;
- assumptions required for it to be true;
- credible alternative explanations;
- current confidence;
- relevant intelligence gaps; and
- evidence or collection that could confirm, weaken or reject it.

A recurring challenge question is:

> **What would have to be true for this conclusion or control assumption not to hold?**

This is intended to expose hidden dependencies and reduce premature closure.

### 5. Assess Confidence

Confidence reflects the quality, consistency and completeness of the evidence supporting a judgement. It is not a measure of how strongly the analyst personally believes a conclusion.

See [`confidence-language.md`](confidence-language.md) for the repository standard.

### 6. Derive Defensive Implications

Analysis should connect adversary behaviour to defensive decisions. Depending on the intelligence requirement, implications may relate to vulnerability management, threat hunting, detection engineering, security architecture, identity, cloud security, incident response or control assurance.

Recommendations should follow from the evidence and assessment rather than being generic security guidance appended to an incident summary.

## Analyst-Led, AI-Assisted Research

AI may be used as an analytical aid to:

- accelerate research and synthesis;
- identify potentially relevant sources or avenues of investigation;
- challenge assumptions;
- propose alternative hypotheses;
- organise complex information; and
- assist with drafting and presentation.

AI is not treated as an authoritative source and does not own the analytical judgement. Material factual claims are checked against underlying sources before publication. The analyst remains responsible for the intelligence requirement, source verification, hypothesis formation, confidence assessment, final judgements, recommendations and publication decision.

A practical quality standard is:

> **If a judgement cannot be independently explained and defended by the analyst, it should not be presented as the analyst's judgement.**

## Publication & Review

The standard workflow is:

**Research → Analysis → Draft → Source Verification → Analytical Challenge → Final Analyst Review → Explicit Sign-off → Publish**

The final review is a mandatory publication gate. Draft reports are not uploaded to the public repository as published intelligence before explicit analyst sign-off.

Published assessments are point-in-time judgements. When material new evidence changes an assessment, updates should identify what changed and why. Earlier analytical judgements should not be silently rewritten.

## Information Handling

Published material is limited to publicly available information and independent analysis. Non-public employer, client, customer or operational information must not be used as evidence or included in the repository. Professional experience may inform analytical questions and reasoning techniques without disclosing or relying upon protected information.
