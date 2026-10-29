# AI Proposal Triage Design

**Candidate:** Maham Jamil  
**Reference:** MATACS-TH-2026-02  
**Time spent:** Update this line with your actual total time before submission.

## Overview
This repository describes a proposed AI-assisted workflow for reviewing contractor proposals. AI extracts facts from proposal text; deterministic rules check mandatory requirements and calculate weighted scores. A human reviewer resolves warnings and makes the final award decision.

## Files
- [Process flow diagram](diagram/process-flow.png)
- [Design write-up](WRITEUP.md)
- [AI extraction prompt](PROMPTS.md)
- [Expected proposal ranking](ranking.json)

## How AI tools were used
I used ChatGPT to help organize the design, draft the explanation and prompt, and check the ranking logic. I reviewed the proposed decisions rather than accepting them automatically. For example, I kept Proposal B as **flagged** rather than automatically declaring it ineligible: the proposal does not confirm that the bidder itself holds the required licence, so a reviewer must resolve the evidence before a final eligibility decision. I also excluded the instruction embedded in Proposal C that tried to influence automated ranking; proposal content is treated as untrusted data, not as system instructions.

## Scope and assumptions
This is a design exercise, not a deployed application. The scoring formulas are explicit design assumptions because the brief provides weights but not exact normalization formulas. Missing evidence is not guessed. Proposals with unresolved mandatory evidence or a missing weighted input are flagged and are not given a final rank until reviewed.
