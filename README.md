# AI Proposal Triage Design

**Candidate:** Maham Jamil  
**Reference:** MATACS-TH-2026-02  
**Time spent:** 1.5 days

## Overview
This is a design for an AI-assisted process to review contractor proposals. AI extracts information from each proposal. Fixed rules check the mandatory requirements and calculate the weighted scores. A human reviewer checks warnings and makes the final decision.

## Files
- [Process flow diagram](diagram/process-flow.png)
- [Design write-up](WRITEUP.md)
- [AI extraction prompt](PROMPTS.md)
- [Proposal ranking and review flags](ranking.json)

## How I used AI tools
I used ChatGPT to help organize the design, draft parts of the write-up and extraction prompt, and check the scoring calculations. I reviewed the results instead of accepting them automatically. For example, Proposal B is flagged rather than immediately rejected because the proposal does not confirm that the bidder itself holds the required licence. A reviewer needs to resolve that point. I also ignored the instruction inside Proposal C that tried to influence the ranking. Proposal text is treated as data, not as instructions to the system.

## Scope and assumptions
This is a design exercise, not a working application. The brief gives the scoring weights but not the exact formulas, so the normalization formulas are stated assumptions. Missing information is not guessed. A proposal with unresolved mandatory evidence or a missing score input is flagged for review rather than given a final rank.
