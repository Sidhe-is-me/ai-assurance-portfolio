# Trustworthy-AI Conformance Assessment (Work Sample)

**Author:** Sidhe-is-me · **Type:** AI assurance / test & evaluation deliverable
**Frameworks applied:** NIST AI RMF 1.0 (Govern/Map/Measure/Manage) · EU AI Act (high-risk, human-oversight) · Section 508 / WCAG 2.1

> **Note on this document.** This is a *portfolio work sample* built on a representative (non-client) system to demonstrate method. It shows how I take an AI system, map it to controls, define the test method, specify the evidence, and record findings — the same rule → control → evidence loop I have run for 14+ years on federal compliance programs, now applied to AI. Any real engagement would substitute the actual system, data, and approvals.

---

## 1. System under assessment (representative)

**System:** "EligibilityAssist" — a public-facing, AI-assisted chatbot that helps citizens determine potential eligibility for a federal benefits program and routes them to next steps. Uses a retrieval-augmented LLM over policy documents; outputs plain-language guidance and a recommended action.

**Why it warrants assurance:** it influences access to a government benefit, serves the general public (including people with disabilities and low digital literacy), and produces consequential guidance — placing it in scope for **EU AI Act high-risk-style scrutiny**, **NIST AI RMF**, and **Section 508**.

**Assessment scope:** trustworthiness characteristics — *valid & reliable, safe, accountable & transparent, explainable & interpretable, fair (with harmful bias managed),* and *accessible/usable for diverse users.* Out of scope: infrastructure security accreditation (covered separately under the ATO/RMF process).

---

## 2. Risk classification & rationale (Map function)

| Factor | Determination | Basis |
|---|---|---|
| Affects access to an essential public service | **Yes** | Benefits eligibility guidance |
| Vulnerable / diverse user population | **Yes** | General public; disability & low-literacy users |
| Autonomy of decision | **Decision-support** (human action follows) | Recommends, does not adjudicate |
| Overall risk tier | **High-impact / elevated** | EU AI Act high-risk analogue; NIST "high-stakes" |

**Implication:** human-oversight, transparency, accessibility, and bias controls are mandatory, not optional.

---

## 3. Controls traceability matrix (Measure / Manage)

Requirement → control → test method → evidence → finding. (Excerpt; full matrix would cover all 72 AI RMF subcategories in scope.)

| ID | Source (control) | Control statement | Test / evaluation method | Evidence artifact | Finding |
|---|---|---|---|---|---|
| TR-01 | NIST AI RMF **Govern 1.1** | Roles, responsibilities, and escalation paths for the AI system are documented and assigned. | Doc review; confirm named owner + escalation path. | RACI + escalation SOP | **Met** |
| TR-02 | NIST AI RMF **Map 1.1** | Intended purpose, users, and out-of-scope uses are defined and published. | Doc review; compare to deployed behavior. | System purpose statement | **Partial** — out-of-scope uses not stated to end users |
| TR-03 | NIST AI RMF **Measure 2.3** | Output accuracy is evaluated against a labeled benchmark before release and on a set cadence. | Run eval set (n≥500) vs. ground-truth eligibility outcomes; record precision/recall. | Eval report + dataset card | **Gap** — no recurring eval; one-time only |
| TR-04 | NIST AI RMF **Measure 2.11** | Harmful bias is assessed across protected groups and disability/literacy segments. | Disaggregated eval across demographic + accessibility segments; disparity thresholds. | Bias eval report | **Gap** — not segmented by disability/literacy |
| TR-05 | EU AI Act **Art. 14 (human oversight)** | A person can understand, override, and halt the system; high-uncertainty outputs are flagged for human review. | Tabletop + live test of override/escalation; confirm uncertainty flagging. | Oversight test log | **Partial** — override exists; uncertainty not surfaced to user |
| TR-06 | NIST AI RMF **Measure 2.9** + EU AI Act **Art. 13** | Outputs are explainable: the system states it is AI, cites its source, and avoids implying a final determination. | Heuristic review + user comprehension test. | Transcript review | **Partial** — AI disclosure present; "not a determination" caveat missing |
| TR-07 | **Section 508 / WCAG 2.1 AA** | The interface (including AI output rendering and error/uncertainty states) is perceivable, operable, and understandable with assistive technology. | Trusted Tester v5 protocol; NVDA/JAWS; keyboard-only; reflow. | VPAT + defect log | **Gap** — dynamic AI responses not announced to screen readers (ARIA live region missing) |
| TR-08 | NIST AI RMF **Measure 3.2** (human factors) | Output is usable at the population's reading level and cognitive load is managed. | Readability scoring; cognitive-walkthrough with diverse users. | Usability findings | **Gap** — output at ~grade 11; target ≤ grade 7 for public benefits |
| TR-09 | NIST AI RMF **Manage 2.2** | A documented process exists to triage, remediate, and re-test identified AI risks to closure. | Confirm tracker + closure evidence on a sampled finding. | Remediation log | **Met** |
| TR-10 | NIST AI RMF **Manage 4.1** | Post-deployment monitoring detects drift, misuse, and emerging harm. | Review monitoring plan + alert thresholds. | Monitoring plan | **Partial** — uptime monitored; output-quality drift not |

---

## 4. Priority findings (most consequential first)

1. **Accessibility of AI output (TR-07) — High.** Dynamic AI responses aren't announced to screen-reader users, so the population most reliant on this channel can't reliably receive the guidance. *This is the differentiated finding most AI assurance reviews miss — it requires both 508 and AI-output fluency.*
2. **Readability / cognitive load (TR-08) — High.** Grade-11 output on a public-benefits tool excludes a large share of the intended users; raises equitable-access risk.
3. **Bias not segmented by disability/literacy (TR-04) — High.** Standard demographic bias testing omits the segments this system most affects.
4. **No recurring accuracy eval (TR-03) — High.** One-time evaluation can't catch post-deployment degradation on consequential guidance.
5. **Uncertainty not surfaced + missing "not a determination" caveat (TR-05/06) — Medium.** Users may over-trust an AI suggestion as an official ruling.

---

## 5. Human-oversight assessment (EU AI Act Art. 14 lens)

A human override path exists (good), but the system does not **flag low-confidence outputs** for review or **signal uncertainty to the user**, so oversight is reactive rather than triggered. Recommend an uncertainty threshold that (a) routes flagged cases to a human and (b) tells the user when guidance is low-confidence. This mirrors the human-in-the-loop escalation discipline I applied in a live agentic-oversight exercise (validate output against ground truth → escalate on anomaly → never accept a result on surface appearance alone).

---

## 6. Recommendations (remediation-to-closure)

| Priority | Action | Owner | Re-test |
|---|---|---|---|
| P1 | Add ARIA live region; re-run Trusted Tester + AT pass on AI output states | Eng + Accessibility | TR-07 |
| P1 | Rewrite output to ≤ grade 7; cognitive-walkthrough re-test | Content + UX | TR-08 |
| P1 | Stand up recurring accuracy + disaggregated bias evals (incl. disability/literacy) | AI assurance | TR-03/04 |
| P2 | Surface uncertainty + add "not a final determination" caveat | Eng + Policy | TR-05/06 |
| P2 | Add output-quality drift monitoring with thresholds | MLOps | TR-10 |

---

## 7. Method note (why this is credible)

This is the exact loop I run today on federal systems — *requirement → control → test method → evidence → finding → remediation to closure* — transposed from Section 508 to AI trustworthiness. The accessibility and human-factors findings (TR-07/08) are the ones a pure governance reviewer typically can't produce and a pure engineer typically doesn't prioritize. That intersection is the point.
