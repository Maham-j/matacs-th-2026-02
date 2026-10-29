# Design Write-up

## 1. What AI does and what the rules do
The reviewer uploads the five proposal PDFs and chooses the procurement category. The system saves the original files before processing them, so an AI service failure does not cause the uploads to be lost.

AI extracts the relevant details into a fixed JSON structure: the bidder's licence evidence, WSIB and insurance, number of qualifying projects, price, timeline, warranties, and team and approach details. Each extracted value should include a short supporting quote and a page reference when available. The reviewer can compare the extracted information with the proposal text and correct it.

The rules-based part checks the mandatory requirements, validates values, calculates the weighted scores, and sorts proposals. These steps should be deterministic so the results can be checked and reproduced. AI does not fill in missing facts, calculate the final ranking, or choose the winner.

## 2. Scoring and consistency
The administrator's criteria and weights are saved in a versioned configuration. For this example, the scoring formulas are assumptions because the brief gives weights but does not define how each criterion should be normalized.

- **Experience:** min(qualifying installations / 14, 1) × 100, using 14 as the largest stated project count in these proposals.
- **Price:** lowest price among proposals with confirmed mandatory eligibility / proposal price × 100.
- **Timeline:** max(0, (16 − weeks) / 16) × 100.
- **Warranty:** average of the panel warranty score (normalized to 25 years) and workmanship warranty score (normalized to 12 years), with each capped at 100.
- **Team and approach:** four checks—named lead, identified crew, safety plan, and monitoring plan. Each documented item earns 25 points.

Each criterion score is multiplied by its weight: 30% for experience, 25% for price, 15% for timeline, 15% for warranty, and 15% for team and approach. The weighted contributions are then added.

The price benchmark is the lowest stated price among proposals whose mandatory requirements are confirmed. That is Proposal D's $231,000. D can be included in this benchmark even though it cannot receive a final rank until its timeline is known. Under the formulas above, A scores 86.76, E scores 73.77, and C scores 66.08. These scores depend on the stated assumptions; the formulas should be approved before the process is used for a real procurement. Reviewers should be able to see each input, its evidence, the normalized score, the weight, and the weighted contribution.

## 3. Recommendation
Based on the stated scoring assumptions, **Proposal A is the highest-scoring proposal at 86.76**, followed by Proposal E at 73.77 and Proposal C at 66.08. This is a preliminary ranking to support review, not an automatic award decision.

**Proposal B should remain on hold** because the proposal says its licensed subcontractor will be confirmed after the award, but it does not confirm that the bidder itself holds the required Ontario electrical contractor licence. The evidence needs to be checked before eligibility can be decided.

**Proposal D should also remain on hold** because its completion timeline is not provided. The system should request the missing timeline before calculating a complete score or assigning a rank. It should not guess a number.

The authorized reviewer should confirm the proposal evidence, resolve both flags, and consider the full procurement requirements before making the final award decision.

## 4. Human review and audit trail
A reviewer checks the extracted facts and source references, resolves warnings, and can correct an extraction with a recorded reason. The system flags B because the bidder's own licence is not confirmed; this is an evidence gap, not proof of wrongdoing or an automatic finding that the bidder is ineligible. It flags D because its timeline is missing. The instruction in C asking the system to rank it first is treated as untrusted proposal text and ignored.

The audit record should include the uploaded file's hash, uploader and timestamps, extraction model and prompt versions, extracted values and source references, validation warnings, criteria and formula versions, individual scores, reviewer edits and reasons, rerun history, and the final human decision.

## 5. Changing the criteria
An administrator can edit the criteria and weights in a settings screen. Before a new version can be used, the system checks that weights are non-negative and total 100%, every mandatory requirement has a defined pass/fail rule, and every scored criterion has a formula.

Each change creates a new version. Earlier results stay linked to the version used to produce them. If the reviewer wants to apply changed criteria to the proposals, they explicitly rerun the assessment. The system creates a new result and audit event instead of silently replacing the old result.

## 6. Risks and safeguards
- **Incorrect extraction or decimal placement:** show supporting quotes and page references, validate formats and ranges, and require reviewer confirmation for important fields such as price and licence.
- **Missing or invented information:** use null or unknown when the proposal does not provide a fact, and flag missing mandatory evidence or score inputs.
- **Instructions hidden in a proposal:** treat proposal text as untrusted data, keep it separate from system instructions, and keep eligibility and scoring logic outside the AI model.
- **Inconsistent scoring:** apply the same published criteria and formulas to every proposal, keep versions of the rules, and allow reviewers to correct errors with a recorded reason.
- **AI outage or unexpected cost:** save files and job status before processing, support safe retries, limit file size and page count, and track processing status and model usage.
- **Sensitive documents or unauthorized changes:** restrict access by role, encrypt files in storage and transit, define retention rules, and log document access and edits.
- **Over-reliance on the ranking:** label the result as decision support. The authorized human reviewer remains responsible for the final award decision.
