---
name: harper-subjectivity-rendering
description: Safely classify and render insurance subjectivities in Harper customer-facing messages. Use whenever internal, carrier, underwriter, order, or subjectivity data may appear in a customer email, template, preview, or test send.
---

# Harper Customer Subjectivity Rendering

Underwriter subjectivities arrive as raw carrier and broker language. That source text is NOT customer-safe. Visibility is earned by classification, never assumed: every item must prove, through a responsibility signal, that the customer owns the action before anything renders. Unknown or ambiguous items are hidden and the service team backstops them.

Use this skill before designing, implementing, reviewing, or testing any customer-facing rendering of subjectivities or underwriting requirements.

## 1. Decision tree

Classify each raw item by asking WHO ACTS, in this exact order. The first match wins, and every tie or doubt resolves toward the less visible outcome.

```
START: one raw subjectivity item
|
1. Never-render evidence ANYWHERE in the item?
   Payment or deposit references, premium finance, bind requests or
   authorization to bind, order-of-coverage or effective-date
   confirmations, carrier rescind / cancel / withdraw / void /
   re-underwrite language, UW margin notes and broker commentary
   ("Q3:", "we are ok with that"), MVRs and other broker-run reports.
   -> HIDE. Never-render evidence beats every other match,
      even a strong deliverable surface form.
|
2. Broker or carrier acts?
   Surplus lines tax filings, stamping office filings, affidavits,
   diligent effort, MVR pulls, internal routing and service tasks.
   -> HIDE (broker-only). The service team owns it.
|
3. Loss runs or loss history?
   -> RENDER the standardized loss-runs contract (section 3),
      always first in the rendered list.
|
4. Customer signs a broker-prepared form?
   Signature or completion marker (signed, completed, executed,
   dated, fill out) plus a form noun (application, ACORD,
   supplemental, state or SL form, disclosure, notice,
   questionnaire), or a standalone signature document
   (No Known Loss Letter, PIP / UM-UIM selection).
   -> Collapse into ONE anonymous DocuSign line. Never name forms.
|
5. Explicit attestation of eligibility facts?
   "Confirmation of / that", "please confirm", "must confirm",
   "warrant that", "attest that" about exposures, operations,
   staffing, revenue, or other eligibility conditions.
   -> Collapse into ONE generic confirmation line.
      Never list the underlying conditions.
|
6. Clear customer-owned deliverable?
   VINs, driver licenses, declaration pages, photos, resumes,
   inspection contact details, website, licenses and
   certifications, contracts, or a specific information ask.
   -> RENDER the fixed friendly phrasing for that class.
      Never render raw or lightly cleaned source text.
|
7. Anything else: unmatched, compound, or ambiguous ownership.
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
- "Completed and signed Florida Surplus Lines forms."
- "Signed, dated, and completed OR SL form at time of binding"
- "Signed and dated DWL Notice"
- "Completed Heavy Vehicle Questionnaire required."
- "NKLL (attached for completion) covering date of lapse to date of policy inception"
- "Signed and completed [Carrier] no known loss letter. Required prior to binding."
- "Completed, signed, and currently dated [Carrier] application (within 60 days of effective date)."
- "Signed application and supplemental"
- "The attached 'NOTICE OF TERRORISM INSURANCE COVERAGE' must be completed and signed by the insured."

Terrorism boundary: the formal federal terrorism disclosure notice with a signature marker is the one terrorism item that reaches the DocuSign bucket. Bare TRIA or terrorism mentions are never-render (see below). This boundary is an implementation detail under active tuning; see the repo fixtures for the executable ruling.

### Confirmations (one generic line, conditions never listed)

- "Confirmation of no prohibited exposures: transportation of children and overnight operations, risks where operations take place in home, risks with swimming pool exposures."
- "Confirmation of non-driver payroll."
- "Confirmation of hiring practices in place and that background checks are completed"
- "Confirmation that operations do not include: Armed employees or contractors"
- "Confirmation there is separate homeowner's coverage in place."
- "Rating Basis: 1 Non-Furnished Owner *confirmation of a personal auto policy in place prior to binding"
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
5. **Matcher internals are implementation details.** Exact patterns, evidence lists, and boundary rulings (for example the TRIA carve-out and the MVR customer-submit exception) evolve; the executable ground truth is the labeled fixture set and the regression gate in the engineering repo (hercules, `tests/eval/`). A release fails if any hidden item becomes visible, a form name escapes the DocuSign collapse, an eligibility condition is listed, or standardized copy changes.

## 5. Review checklist

```
- [ ] Every item classified by responsibility (who acts), first match wins
- [ ] Unknown and ambiguous items are hidden
- [ ] Never-render precedence applied (beats every visible bucket)
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
