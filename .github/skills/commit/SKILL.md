---
name: commit 
description: >- 
  Use only when you are about to run any git write command.
---

# Git Commit Automation

## Role & Mission

You are responsible for clean, atomic, well-documented git history in this repo. 
Every commit must follow the project's standards.

When the user already asked to create a branch and/or commit, including "later" in the same task: execute at the end of the task.

---

## 1. Workflow (Step-by-Step)

### A. Commits

	1.	Gather context (run in parallel):
	⁃	git status
	⁃	git diff and git diff --staged
	⁃	git log -n 5
	2.	Analyze the diff — one logical purpose per commit.
	3.	Draft following The Seven Rules below.
	4.	Commit with hooks disabled (see command format below).
	5.	Verify with git status.
	6.	Feature branch: If the current branch is not the integration base (main, dev, or user-specified), offer the user a PR title and body draft using the PR Message Draft Rules below.

### B. New branches

When creating a branch before work:

	1.	Confirm base branch.
	2.	Name with existing prefixes: feat/, fix/, refact/ + short kebab slug.
	3.	Create from the correct base: git checkout -b <name> <base>.
	4.	When committing on that branch, follow section A.

---

## 2. The Seven Rules of a Great Commit Message

Every commit message must follow Conventional Commits and all seven rules below.

Subject format (required):
`<type>[optional scope][optional !]: <description>`
	⁃	Types: feat, fix, build, chore, ci, docs, style, refactor, perf, test (others allowed when they fit better).
	⁃	Scope (optional): noun in parentheses, e.g. feat(parser):.
	⁃	Breaking change: append ! before : and/or add a BREAKING CHANGE: footer.
	⁃	Description: short summary in imperative mood, immediately after : . Capitalize the first letter of the description.

### 1. Separate subject from body with a blank line
Begin with a single summary line (subject), a blank line, then the body. 
Why: Git and GitHub treat the first line as the title. Without the blank line, tools merge subject and body.

### 2. Limit the subject line to 50 characters
Keep the subject around 50 characters; 72 is the hard limit. 
Why: Forces a concise summary. If you cannot summarize well in 50 characters, the commit may be too large — split it.

### 3. Capitalize the subject line
Capitalize the first letter of the description after the type/scope prefix (e.g. fix: Prevent racing of requests). 
Type and scope tokens stay lowercase; only BREAKING CHANGE in footers must be uppercase.

### 4. Do not end the subject line with a period

### 5. Use the imperative mood in the description
Write as a command. The description should complete: If applied, this commit will [description].
	⁃	refactor: Simplify filesystem adapter (correct)

### 6. Wrap the body at 72 characters
Git does not auto-wrap. Wrap body text manually at 72 characters.

### 7. Use the body to explain what and why, not how
The diff shows how. The body explains the problem, context, and side effects.

Example:
```
refactor: Remove unused catalog persistence port

The MemoryCatalogPersistence fake covered all test scenarios;
the redundant Protocol definition added noise and drifted from
the domain interface. No runtime behavior changes.
```

Breaking change example:
```
feat!: Drop support for Node 6

BREAKING CHANGE: use JavaScript features not available in Node 6.
```

---

## 3. PR Message Draft Rules (User Opens the PR)

When the current branch is not the integration base, offer the user a PR title and body using these rules.

	⁃	Title: Same rules as the commit subject (type[scope]: description, imperative, capitalized description, ≤50–72 chars, no period).
	⁃	Body: Include:
	⁃	Summary — 1–3 bullets on what changed and why
	⁃	Test plan — checklist of verification steps run or to run

---

## 4. Execution Guardrails

Never run commands that lose pending changes, unless confirmed by the user original message intent.

	⁃	Hooks disabled: git -c core.hooksPath=.git/hooks-empty commit unless the user explicitly asks to run hooks.
	⁃	No Co-authored-by: Never append Co-authored-by trailers.
	⁃	No force push. Never push unless explicitly asked.
	⁃	Atomic commits: If the diff has multiple unrelated changes, suggest splitting first.

---

## 5. Commit Command Format

Use a HEREDOC (bash) or here-string (PowerShell) so blank lines and wrapping survive.

**Bash:**
```bash
git -c core.hooksPath=.git/hooks-empty commit -m "$(cat <<'EOF'
type: Description in imperative mood

Body explaining what and why, wrapped at 72 characters.
EOF
)"
```

**PowerShell:**
```powershell
git -c core.hooksPath=.git/hooks-empty commit -m @"
type: Description in imperative mood

Body explaining what and why, wrapped at 72 characters.
"@
```
