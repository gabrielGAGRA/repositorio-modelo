---
name: adversarial-review 
description: Independently try to break every diff and retain only evidence-backed findings.
---

# Adversarial Review

## Goal

Challenge the change contract rather than confirming the author's intent. Run before every delivery or git write.

An adversarial agents should be fresh-eyes on the agent's work. A fresh agent reads the diff (read-only), with no memory of writing the code, and judges whether the feature is safe to ship.

## Context isolation

Use a fresh local subagent whenever the environment provides one. Give it the approved task/plan contract if one exists, the diff, and only the repository context needed to test a hypothesis. If unavailable, use a separate new chat with that limited context. Same-session review is always prohibited.

## Workflow

	1.	Classify risk — always scope/regression; add contract compatibility for APIs, access/exposure for security or data, atomicity/idempotency for persistence or concurrency, and dependency boundaries/wiring for architecture. 
	2.	Create falsifiable break hypotheses from the approved task/plan contract (when present) and diff. 
	3.	Trace each hypothesis through affected code, tests, and boundary contracts. 
	4.	Attempt to refute each candidate finding with code or executable evidence. Discard a candidate that cannot establish a reachable failure path. 
	5.	Return one verdict:
	⁃	pass — no evidence-backed Required finding remains;
	⁃	blocked — a Required finding has a reproducible impact;
	⁃	needs-human-decision — an unresolved product or risk trade-off blocks a reliable conclusion.

	Example:
	"Use the adversarial-review agent: try to break x before we ship"
	* Agent reads the diff, migration and tests
	"Verdict: tests pass but the y hole is still open. Do not ship."


## Finding requirements

Every retained finding must include severity, location, reproduction scenario, controlled input/state, traced failure path, concrete impact, and the verification needed after remediation. Do not report style preferences, speculative defects, or findings without an attack/regression path.

## Output

```markdown
# Adversarial Review

## Verdict
[pass | blocked | needs-human-decision]

## Context isolation
[fresh subagent | separate chat | same-session fallback]

## Findings
- `path:Lx` — [severity] [scenario, evidence, impact, required verification]

## Checks refuted
- [candidate hypothesis and evidence that disproved it]
```
