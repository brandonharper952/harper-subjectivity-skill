---
name: harper-subjectivity-rendering
description: Safely classify and render insurance subjectivities in Harper customer-facing messages. Use whenever internal, carrier, underwriter, order, or subjectivity data may appear in a customer email, template, preview, or test send.
---

# Harper Customer Subjectivity Rendering

Underwriter subjectivities arrive as raw carrier and broker language. That source text is NOT customer-safe. Visibility is earned by classification, never assumed: every item must prove, through a responsibility signal, that the customer owns the action before anything renders. Unknown or ambiguous items are hidden and the service team backstops them.

Use this skill before designing, implementing, reviewing, or testing any customer-facing rendering of subjectivities or underwriting requirements.

## 1. Decision tree

Classify each raw item by asking WHO ACTS, in this exact order. The first match wins, and every tie or doubt resolves toward the less visible outcome.

CENTRAL GATE ORDER (blind validation round 4, 2026-07): the vetoes in steps 1-3 run FIRST, at one central choke point, and their verdict is FINAL FOR EVERY BUCKET - listed, DocuSign, and confirmation alike. No positive matcher may run before them, and no matcher path may skip them. Round 4 proved that wiring the vetoes into individual matcher paths leaks: the untouched paths (licenses, DocuSign, confirmation) rendered vetoed content.

```
START: one raw subjectivity item
|
1. Third-party actor owns the document?
   Subcontractors' / vendors' / tenants' COIs, their coverage or
   contracts, coverage maintained ON subcontracted labor,
   confirmations that vendors carry limits ("all participating
   alcohol vendors carry Liquor Liability limits..."),
   consequence-only language about uninsured subs ("claims related
   to subcontractors may not be covered if..."). These are never
   customer deliverables OR confirmations UNLESS the ask targets the
   insured's OWN artifact ("Copy of standard contract used by
   Applicant with subcontractors" still renders).
   -> HIDE (unknown ownership; the service team backstops).
|
2. Never-render evidence ANYWHERE in the item?
   Payment or deposit references, premium finance and ANY completed or
   signed financing agreement (match the family by semantics -
   "financing agreement", "finance agreement", "premium financing
   agreement" - never one exact wording), bind requests or
   authorization to bind, order-of-coverage or effective-date
   confirmations, carrier rescind / cancel / withdraw / void /
   re-underwrite language, application-approval and binder-receipt
   conditions, quotation-accuracy boilerplate ("issued based on the
   truthfulness and accuracy of the responses"), carrier-conducted
   premises surveys and inspections, TRIA / terrorism forms and
   notices (signed or not), UW margin notes and broker commentary
   ("Q3:", "we are ok with that"), MVRs and other broker-run reports
   ("Motor Vehicle Records" and "Motor Vehicle Reports" alike).
   -> HIDE. Never-render evidence beats every other match,
      even a strong deliverable surface form.
|
3. Carrier-conditional language ANYWHERE in the item?
   "Subject to change", "upon review", "receipt and acceptance",
   "receipt, review and acceptance/approval", "reserves the right",
   re-underwrite markers ("may affect our pricing, terms, and/or
   acceptability"). The quote's pricing or terms being provisional on
   carrier review is carrier workflow and VETOES every positive match
   IN EVERY BUCKET: "please provide a full list of products; pricing
   and terms subject to change upon review" is hidden despite the
   please-provide construction, a signed supplement wrapped in
   "receipt and acceptance" is hidden despite the signed-form match,
   and a confirmation carrying re-underwrite language is hidden
   despite the confirm verb.
   ONE exception: the standardized loss-runs ask (step 4) outranks
   conditional wrappers ("Subject to receipt and acceptance of 5
   years currently valued loss runs" still renders the loss-runs
   contract). The carve-out is applied at the central gate, not
   inside a matcher.
   -> HIDE.
|
4. Loss runs or loss history?
   -> RENDER the standardized loss-runs contract (section 3),
      always first in the rendered list.
|
5. Broker or carrier acts?
   Surplus lines tax filings and SL forms of any state, non-admitted
   forms of any state, state/tax packets, stamping office filings,
   affidavits, diligent effort, no-known-loss letters signed at
   binding, SIGNED statements of no losses, MVR pulls, recurring
   annual program filings, internal routing and service tasks.
   -> HIDE (broker-only). The service team owns it.
|
6. Customer signs a broker-prepared form?
   Signature or completion marker (signed, signs, completed,
   executed, dated, fill out) plus a form noun (application, ACORD,
   supplemental, disclosure, questionnaire), or a standalone
   signature document (PIP / UM-UIM selection, coverage
   election/rejection forms, named warranty/consent statements like
   a "Client Consent Warranty Statement").
   -> Collapse into ONE anonymous DocuSign line. Never name forms.
|
7. Explicit attestation of eligibility facts?
   "Confirmation of / that", "please confirm", "must confirm",
   "reconfirm", "subject to confirming", "warrant that", a leading
   "WARRANTED:" label (the warranty compound takes confirmation
   collapse even when it also mentions a certificate page), "attest
   that", "confirm no ...", or a letter the insured signs explicitly
   confirming eligibility facts ("letter signed and dated by the
   insured confirming...") about exposures, operations, staffing,
   revenue, or other eligibility conditions.
   EXCEPTION: a LEADING copy-of / proof-of document ask with a
   trailing confirm-purpose clause ("Copy of expiring policy and all
   endorsements, to confirm retroactive dates") is the document ask,
   not an attestation - route it to step 8.
   Confirmations fail closed: third-party coverage content and
   carrier re-underwrite language never reach this step (vetoed at
   steps 1-3).
   -> Collapse into ONE generic confirmation line.
      Never list the underlying conditions.
|
8. Clear customer-owned deliverable WITH an explicit ask?
   Rendering a listed item requires an explicit customer-directed ask
   construction FROM EVERY MATCHER, including licenses and
   certifications: an imperative or request verb (provide / submit /
   forward / send / furnish), a copy-of / proof-of / details-of
   construction, or a direct question to the customer about their own
   coverage or claims history. Document nouns alone ("product list",
   "procedure", "contract", "license") DO NOT qualify; a bare
   declarative fact or coverage condition ("Armed personnel must be
   properly certified...", "Licensing and background checks conducted
   as required...") and an acknowledgement ("Acknowledgement that any
   available licenses must be obtained...") are NEVER information
   requests from any matcher. The retail agent's / producing broker's
   own license number is broker-channel data, never a customer
   deliverable. Classes: VINs, driver licenses, declaration pages,
   photos, resumes, inspection contact details, website, licenses and
   certifications, contracts, or a specific information ask.
   -> RENDER the fixed friendly phrasing for that class.
      Never render raw or lightly cleaned source text.
|
9. Anything else: unmatched, compound, or ambiguous ownership.
   -> HIDE. Unknown items are never customer-visible.
```

### Taxonomy summary

| Bucket | Responsibility | Customer rendering |
|---|---|---|
| Client-provides | Customer supplies a document, fact, contact, or photo | One friendly line per item, fixed phrasing |
| Broker-fulfills via DocuSign | Customer signs a form Harper or the carrier prepares | ONE anonymous DocuSign line, forms never named |
| Confirmation | Customer attests eligibility assertions are true | ONE generic confirmation line, conditions never listed |
| Broker-only | Harper, service, placement, or carrier workflow owns it | Never shown |
| Never-render | Payment, binding, carrier rights, internal commentary | Never shown, even when phrased as a customer request |
| Unknown | Ownership unproven | Never shown; service team backstops |

Precedence: never-render > broker-only > confirmation collapse > DocuSign collapse > client-provides. A compound item takes its least visible classification.

Note on bare declarative eligibility statements ("No employees, no subcontractors", "Must maintain a liquor liability policy"): taxonomically these are confirmations, but a conservative implementation may hide them when no explicit attestation signal is present. Both outcomes are safe because individual conditions are never listed either way.

## 2. Labeled example bank

Real production carrier and underwriter language, anonymized where a name or account detail appeared. These are regression labels, not suggestions.

### Client-provides (LISTED)

| Raw source | Rendered as |
|---|---|
| "Company valued loss runs within 30 days of effective date, reflecting no losses for the past 3 years if applicable" | Loss-runs contract, 3 years |
| "This quote is subject to receiving currently valued, acceptable loss runs for the last 5 policy terms prior to binding or Signed No Loss Statement." | Loss-runs contract, 5 years (the alternative form is never named) |
| "This quote is subject to receiving currently valued, acceptable loss runs for the last 3 to 5 policy terms prior to binding showing no undisclosed losses." | Loss-runs contract, 3 to 5 years (true source range) |
| "Minimum 3 years of company loss runs or signed no known loss letter if no prior insurance" | Loss-runs contract, 3 years |
| "Provide the staff titles for the 4 'other' staff listed on the application" | Please provide the requested information. |
| "Prior to binding, please provide an accessible website and full list of products" | Please provide a link to the business website. |
| "Copy of Home Health license" | Please provide copies of the requested licenses or certifications. |
| "Inspection Contact - Name, Phone Number, Email Address" | Please provide the best contact information for the inspection. |
| "A copy of the owner's resume showing related experience is required for firm terms prior to binding." | Please provide the requested resumes. |
| "Copy of expired policy" | Evidence-of-coverage line (section 3) |
| "Copy of Personal Auto Policy for: [Name]" | Evidence-of-coverage line (section 3) |
| "Copies of State Inspections and Complaint Investigations for the last 36 months to include all related plans of corrections" | Please provide the requested documents or information. |

Friendly phrasing changes tone and strips internal context, carrier instructions, and consequences. It never invents requirements and never echoes raw text.

### Broker-fulfills via DocuSign (one anonymous line)

All of these collapse into the single DocuSign line. Ten forms still produce one line. Form names are never shown to the customer.

- "Signed and completed Acord Application with applicant's signature and date"
- "Completed Heavy Vehicle Questionnaire required."
- "Completed, signed, and currently dated [Carrier] application (within 60 days of effective date)."
- "Signed application and supplemental"
- "Fully completed and signed New Mexico Uninsured Motorists Coverage Disclosure/Selection/Rejection form"
- "Completed and signed 'Employee's Rejection of Statutory Coverage' form for any owner/officer to be excluded."

- "Fully completed, currently signed and dated Hiscox Client Consent Warranty Statement" (a named warranty/consent statement is a standalone customer-signature document)
- "An authorized representative of the Named Insured signs the Coalition application in 10 days of the issuance of a binder" (the consequence clause is not never-render evidence)

Broker-side paperwork boundary (Brandon ruling, 2026-07): signed TRIA/terrorism forms, no-known-loss letters, signed statements of no losses, surplus lines / SL forms of any state, non-admitted forms of any state, state and tax packets, licensee disclosures, and affidavits NEVER reach the customer, signed or not - the broker handles them at binding. They are hidden, not DocuSign. Customer-signed coverage election/rejection forms (PIP, UM/UIM, officer exclusion) and named warranty/consent statements remain DocuSign.

Carrier-review boundary (blind validation round 4, 2026-07): "Underwriter receipt, review and acceptance of the fully completed application" and any signed form wrapped in receipt-and-acceptance language are HIDDEN, not DocuSign - the carrier accepting a document is not the customer owing one.

### Confirmations (one generic line, conditions never listed)

- "Confirmation of no prohibited exposures: transportation of children and overnight operations, risks where operations take place in home, risks with swimming pool exposures."
- "Confirmation of non-driver payroll."
- "Confirmation of hiring practices in place and that background checks are completed"
- "Confirmation that operations do not include: Armed employees or contractors"
- "Confirmation there is separate homeowner's coverage in place."
- "Rating Basis: 1 Non-Furnished Owner *confirmation of a personal auto policy in place prior to binding"
- "Must receive a letter signed and dated by the insured confirming drink specials are not longer than 3 hours" (signed letter explicitly confirming eligibility facts)
- "TO BIND, CONFIRM NO CANNABIS, CBD, OR THC-INFUSED DRINKS ARE SOLD / SERVED."
- "Reconfirm the latest closing time."
- "Subject to confirming all states the applicant provides service/repair work in"
- "WARRANTED: General Liability Insurance coverage in force, a copy of the certificate page to be produced prior to binding" (the warranty compound takes confirmation collapse)
- "Warranty that the insured has not had any regulatory action initiated against them in the last five years as a direct result of a cyber event"
- "No employees, no independent contractors, and no work subcontracted to others." (bare declarative; safe to hide instead)
- "Must maintain a liquor liability policy" (bare declarative; safe to hide instead)

### Never-render and broker-only (HIDDEN)

- "A written request to bind coverage is required."
- "[Carrier] cannot bind coverage without a written request to bind coverage."
- "Written confirmation of the order of coverage based on the terms and conditions outlined within this quotation"
- "Copy of the Insured's down payment or full payment check"
- "Payment of premium."
- "They paid with ACH. Do not take action until we see funds hit the account."
- "Quote subject to clean MVR's"
- "Acceptable MVRs and Inspections"
- "Receipt of completed and signed TRIA form."
- "22-23 Currently Valued Company Loss Runs, any adverse activity may null & void quote" (rescind language dominates the loss-runs match: hidden)
- "Prior to binding, please provide a full list of products; pricing and terms subject to change upon review" (carrier-conditional veto beats the please-provide construction)
- "Completed and signed financing agreement (if applicable)" (financing family by semantics)
- "All subjectivities (except inspections) must be received and accepted by the carrier prior to binding."
- "Subject to receipt and acceptance of signed and dated Kinsale Supplement upon binding" (the carrier-review wrapper vetoes the signed-form match)
- "Underwriter receipt, review and acceptance of the fully completed application" (carrier review workflow, not a signature ask)
- "Subject to confirmation the insured does not perform any work underground. Please note the answer may affect our pricing, terms, and/or acceptability." (re-underwrite language vetoes the confirmation match)
- "A fully completed, signed and dated MS Non-Admitted Form." (non-admitted forms of any state are surplus-lines broker-side paperwork)
- "Statement of No Losses - Signed and dated by the insured" (signed no-loss statements are broker-side binding paperwork; the bare unsigned "Statement of no loss" stays a confirmation)
- "A survey of your premises may be conducted by a representative of [Carrier]" (carrier-conducted inspection)
- "This quotation is issued based on the truthfulness and accuracy of the responses to the questions on the insurance application" (quotation-accuracy boilerplate)
- "She has sent us every subjectivity. Please review email bot to collect these things. - [Name]"
- "[Carrier] must be advised of any change in the information provided, or change in the exposure basis, hazard or risk prior to the effective date of coverage."

### Unknown ownership (HIDDEN, service team backstops)

- "Active steps taken to mitigate and improve auto maintenance violations listed on applicants CAB."
- "Effective 9/25."
- "FURNISHED AUTOS - [NAME]"
- "Statement of no known losses"
- "Policy is subject to an annual premium audit to verify payroll information."
- "Refer to carrier quote"
- "Per Carrier terms attached"
- "Retail agent's first and last name, email address, phone number, and agency name."
- "COI from all Sub-Contractors or Vendors"
- "Subcontractors/Vendors must provide certificates of insurance naming insured as Additional Insured" (third-party-actor veto)
- "Claims related to subcontractors may not be covered if the certificates of insurance, hold harmless agreements, and proof of workers' compensation are not obtained as stated." (consequence-only third-party language)
- "A formal procedure is in place outlining the protocol for handling and reporting potential concussions." (bare declarative, no ask verb and no attestation request)
- "No more than 25% of services provided to minors who have been victims of molestation, abuse or violence." (bare eligibility declarative)
- "Armed personnel must be properly certified by state agency or firearms certification school" (bare certification declarative - never the licenses line)
- "Acknowledgement that any available licenses must be obtained in order for coverage to apply." (an acknowledgement is not a document request)
- "For non-admitted business, the retail agents NY broker license number is required prior to binding." (the agent's own license number is broker-channel data)
- "Confirmation that all participating alcohol vendors carry Liquor Liability limits equal to or greater than our applicant's." (third-party coverage; the confirmation surface form does not rescue it)
- "A jobsite inspection."
- "Applicant will not ever do business in any of the following states: AL, AK, DC, IL, LA, MS, RI, MN, NM, SC, WA, or WV."

## 3. Rendering contracts (verbatim)

Customer output may contain only these lines, in this order: the loss-runs block first when applicable, then friendly client-provided asks, then one DocuSign line when any DocuSign item exists, then one confirmation line (last) when any eligibility assertion exists.

### Loss runs (condensed two-sentence shape, always first)

The ask line, with a note line rendered directly beneath it inside the same list item in smaller muted type:

> Please provide [source-faithful years] years of currently valued loss runs from your prior business insurance.
>
> No prior commercial coverage? Just confirm that in your portal.

Year-count fidelity: preserve an explicit source count, including counts outside 3 and 5 ("5 years" when the source says 5, "7 years" when it says 7). Preserve an explicit range as a range. Use "3 to 5" ONLY when the source is a range, unspecified, or cannot be parsed safely. Use singular "year" when the count is exactly 1. "Currently valued" is always retained. The qualifier is prior BUSINESS insurance: personal auto insurance is NOT prior commercial coverage. The no-prior-coverage note renders only with the loss-runs item, never alone; if a surface has no portal link, fall back to a reply ask.

### DocuSign (at most once)

> Complete and sign the forms our service team will send over via DocuSign shortly.

### Confirmation (at most once, always last)

> Confirmation that all eligibility requirements are true

Paired with a simple reply ask in the surrounding template, such as:

> Just reply to this email to confirm.

Never enumerate the underlying eligibility conditions.

### Evidence of coverage (fixed phrasing)

> Please provide evidence of the requested coverage (please provide a policy declaration page or a copy of your policy).

### Output constraints

- Maximum 10 visible requirement lines total, INCLUDING any overflow line ("...and N more"). On overflow, at most 9 item lines render plus the single overflow line, never 10 items plus an eleventh overflow line.
- Deduplicate semantically equivalent requests.
- Never use em dashes or en dashes. ASCII hyphens only when punctuation is necessary.
- Never use "etc."
- Never mention money already paid.
- Never reveal form names that belong in the DocuSign bucket.
- Never render raw or lightly cleaned source subjectivity text, and never use it as a fallback.
- Never make ambiguity customer-visible. Hide it; the service team backstops it.
- Lookup, parsing, or classification failure must omit the requirements section entirely while preserving the base email. Raw text is never the failure mode.

## 4. Process guardrails

These lessons are what keep the rules durable. Skipping any of them has produced real customer-visible leaks.

1. **Preview and test sends must run the production filter pipeline end to end.** Raw subjectivities go through the production service-layer filter (cleanup, denylist, allowlist classification with suppression telemetry, dedupe), then loss-runs seeding, and only that filtered output reaches the template. Feeding raw JSON or fixture strings directly into the template is forbidden: an ad-hoc harness that bypassed the service filter is exactly how a denylisted order-of-coverage line once leaked into a test send. Template-level hide-by-default classification is defense in depth, not a license to skip the service filter.
2. **Tuned-corpus accuracy is not generalization.** A matcher that scores perfectly on the corpus it was tuned against proves nothing about unseen language. Require blind held-out validation: label a fresh sample from production data against this contract before looking at predictions, then score.
3. **Ambiguity resolves toward hiding.** When classification is uncertain, the safe default is always suppression. A hidden valid ask costs a service-team follow-up; a rendered internal line costs customer trust.
4. **Per-example patching creates overfit matchers.** Do not solve misses with one-off regexes keyed to specific sentences. Classify by responsibility signals (who acts: client provides, client signs, broker or carrier handles) so new phrasings inherit the right behavior.
5. **Matcher internals are implementation details.** Exact patterns and evidence lists evolve; the executable ground truth is the labeled fixture set and the regression gate in the engineering repo (hercules, `tests/eval/`). A release fails if any hidden item becomes visible, a form name escapes the DocuSign collapse, an eligibility condition is listed, or standardized copy changes.
6. **Positive gates beat denylist growth (blind validation round 3, 2026-07).** The round-3 blind validation produced 8 leaks from novel carrier language that slipped the denylist and then positively matched broad document/information matchers. The durable fix is stricter positive gates - the ask-verb requirement for listed items, the carrier-conditional veto, and the third-party-actor veto - not more denylist patterns keyed to the leaked sentences.
7. **Gates must be structurally unbypassable (blind validation round 4, 2026-07).** Round 4 produced 8 leaks even though every round-3 gate held on fresh wording, because the gates were wired into individual matcher paths: the licenses matcher had no ask-verb gate, and the DocuSign and confirmation paths ran no carrier-conditional or third-party veto. The durable fix is ONE central choke point that every positive classification passes before it can produce visible output: vetoes first, verdict final for all buckets, ask-verb required for a listed line from any matcher, and the same gate on the LLM path. A gate that exists but is not on every path is a leak waiting for the path you did not touch.

## 5. Review checklist

```
- [ ] Every item classified by responsibility (who acts), first match wins
- [ ] Unknown and ambiguous items are hidden
- [ ] Never-render precedence applied (beats every visible bucket)
- [ ] Vetoes run at ONE central choke point, before every positive matcher
- [ ] Veto verdicts are final for ALL buckets (listed, DocuSign, confirmation)
- [ ] Listed items carry an explicit customer-directed ask construction
- [ ] Ask-verb gate applies to EVERY listed matcher, including licenses
- [ ] Carrier-conditional language (subject to change / upon review /
      receipt and acceptance / re-underwrite markers) vetoed in all buckets
- [ ] Third-party documents (subs' / vendors' / tenants') stay hidden,
      including confirmation-shaped items about their coverage
- [ ] Financing agreements hidden by semantics, not exact wording
- [ ] Bare declaratives and acknowledgements never rendered as asks
- [ ] Retail agent's / producing broker's own license number suppressed
- [ ] Non-admitted forms (any state) stay broker-side with surplus lines
- [ ] The LLM path passes the same central gate as the deterministic path
- [ ] Loss runs first, source-faithful years, "3 to 5" only as safe default
- [ ] No-prior-coverage note renders beneath the loss-runs item only
- [ ] Personal auto coverage not treated as prior commercial coverage
- [ ] All forms collapse into one anonymous DocuSign line, never named
- [ ] All eligibility assertions collapse into one confirmation line, last
- [ ] Conditions never listed; confirmation paired with a reply ask
- [ ] Evidence-of-coverage asks use the exact fixed phrasing
- [ ] 10 visible lines max including the overflow line
- [ ] No em dash, en dash, "etc.", or paid-money reference
- [ ] Raw source text is never rendered and never a fallback
- [ ] Preview/test sends ran the production filter pipeline end to end
- [ ] Failure omits the requirements section, base email intact
```
