# Design Write-up

## 1. AI vs. rules
The reviewer uploads the five proposal PDFs and selects the category. The system stores the originals first so an AI outage does not lose work. AI is used to extract facts into a fixed JSON structure: bidder licence evidence, WSIB and insurance statements, project count and size, price, timeline, warranties, and team/approach details. Each extracted value should include a short supporting quote and page reference where available. The system then shows the extracted values beside the source text for confirmation.

Plain code, not AI, checks mandatory criteria, validates data types and ranges, calculates weighted scores, sorts proposals, and creates the result. These steps are deterministic and auditable. AI is not allowed to invent missing values or decide the winner.

## 2. Consistency and explainability
The administrator's criteria and weights are stored as a versioned configuration, not hard-coded. For this example, experience is normalized as `min(qualifying installations / 14, 1) × 100`, where 14 is the largest stated count in the sample. Price is `lowest price among proposals with confirmed mandatory eligibility / proposal price × 100`. Timeline is `max(0, (16 − weeks) / 16) × 100`. Warranty score is the average of panel warranty normalized to 25 years and workmanship warranty normalized to 12 years, each capped at 100. Team/approach is a four-part rubric: named lead, identified crew, safety plan, and monitoring plan; each documented item earns 25 points. Weighted criterion scores are multiplied by 30%, 25%, 15%, 15%, and 15%, then added.

The price benchmark uses the lowest stated price among proposals whose mandatory checks are confirmed, including D; this does not make D rankable while its timeline is unknown. Under these assumptions, A scores about 86.76, E about 73.77, and C about 66.08. The same confirmed inputs and criteria version produce the same scores and order. Reviewers see each criterion's raw evidence, normalized score, weight, contribution, and reason. Formula choices are assumptions for this exercise and should be approved by the administrator before real procurement use.

## 3. Human in the loop and audit
A reviewer confirms extracted facts and source locations, resolves flags, and can correct an extraction with a recorded reason. The system flags B because the proposal says a licensed electrical subcontractor will be confirmed after award but does not confirm that the bidder itself holds the mandatory licence. This is unresolved evidence, not an automatic finding of fraud or a confirmed failure. D is flagged because its completion timeline is not stated; the system must not guess it. C's embedded instruction to rank it first is treated as untrusted proposal text and ignored. A human makes the final award decision regardless of ranking.

The audit record includes upload/file hash, uploader and timestamps, extraction model/prompt version, extracted values and source references, validation warnings, criteria version, formula version, per-criterion scores, reviewer edits and reasons, rerun history, and final human decision.

## 4. Changing criteria
Criteria are editable in an admin screen and validated before use: weights must be non-negative and total 100%, mandatory criteria must have a defined pass/fail rule, and scoring formulas must be specified. Every change creates a new version. Existing results remain linked to the old version. The reviewer can explicitly rerun all proposals against the new version, producing a new result set and audit event rather than silently overwriting earlier results.

## 5. Risks and safeguards
- **Wrong extraction or misplaced decimal:** show source quotes/page references, validate formats and ranges, and require reviewer confirmation for high-impact fields such as price and licence.
- **Made-up or missing facts:** use `unknown` rather than inference; flag unresolved mandatory evidence and missing weighted inputs.
- **Prompt injection in a proposal:** treat document text only as data, delimit it in the extraction prompt, ignore instructions inside it, and keep scoring logic outside the model.
- **Inconsistent or biased scoring:** use published criteria, fixed formulas, versioned weights, and the same rubric for every proposal; allow review and correction.
- **AI service outage or cost:** save uploads and job state before processing, allow safe retry, set file/page limits and model budgets, and show processing status.
- **Sensitive documents or unauthorized changes:** restrict access by role, encrypt files in storage and transit, set retention rules, and log reads and edits.
- **Over-reliance on ranking:** clearly label the result as decision support; the authorized human reviewer owns the final award decision.
