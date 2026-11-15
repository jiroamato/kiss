# Assess

Produce `.kiss/assessment.md`. Read-only. Every finding has `path:line` evidence, a read-only command and its output, or for an absence, the location searched. Assess the committed HEAD of the current branch, and list uncommitted changes under Not checked.

When the target is a folder inside a larger repository, say which one you assessed, put `.kiss/` at that folder's root, and scope git history to that path. When that history is a bulk import, widen to the git root and say so.

If `.kiss/assessment.md` already exists, read it first. The new report replaces it. Tell the operator to commit it, or to keep a copy if `.kiss/` is ignored. Each finding carries a stable id, `<rule, anti-pattern id, guardrail-gap, or verification-gap>:<path or slug>`. Keep the previous id when the evidence moves or the label narrows. Mark each finding new, unchanged, or reopened (resolved before, back now). Previous findings that no longer hold go under Resolved, and those now out of scope under Not checked.

## Before the questions

Read `principles.md`. Then build the model the questions need:

- For a repo you can hold in context, read the tree, the package manifests, the CI config, and the agent instruction files. Pick the last five to ten units of product work from git history and read their diffs.
- For a subsystem you cannot judge from the tree, run the **how** skill on it. Its Where Things Live section is the start of the noun table.
- When a shape looks wrong but deliberate, run the **why** skill before calling it debt. A constraint the repo does not own goes under Leave alone, not Improve.
- When subagents are ruled out, skip **how** and **why** and do their reading yourself: the entry files, the design docs, and `git log` on the file.
- For a repo too large to hold in context, run the **swarm** skill: one read-only worker per package or side, each answering the full question list for its slice and listing the values, writers, and contracts it sees. Explain the cost and ask the operator first. Merge the noun tables yourself, and answer questions 2, 3, and 7 across slices.

## Questions

Answer in order. Status is `pass`, `fail`, `partial`, `n/a (<the shape the question presumes>)`, or `unknown (<what you could not see>)`. One answer may yield several entries in the report.

**Shape** (principles.md section 3)

1. Where does code run, and can an agent tell from the open file which boundary it is in and what it may import? Does a wrong import fail? Does a name promise a constraint beyond imports, and what check enforces it?
2. Which values outlive a request or a run? For each, name every writer. Where a value lives on two sides, which side is authoritative?
3. Where two boundaries talk, is there one typed contract both sides import or generate from, so a change on one side fails the build on the other?
4. Which sets grow when someone contributes? For each, open or closed, and how it is populated today. For a closed set, does every runtime switch over it fail the build on a missing variant?

Write the noun table from these answers before going on. The table is the target. Each row where today differs becomes a finding.

**Rules** (principles.md section 2)

5. Rule 1. From git history, list the files each of the last five to ten units of product work changed. With fewer than two units, answer `unknown`. Which files did two or more of them change? Is the blessed path the shortest one?
6. Rule 2. Which boundaries does the repo claim, in docs, folder names, or comments? Which are enforced mechanically, by what, and does the error name what to use instead?
7. Rule 3. From question 2, which values have more than one writer? Include specs restated in code.
8. Rule 4. From question 4, which open sets are hand-edited lists?
9. Rule 5. Every suppression and disabled rule. Which carry a reason, an issue link, an expiry, and a human's approval? Expect-error directives that assert rejection are tests, not suppressions. A `TODO` or workaround comment is a `comments-as-bandaids` hit, counted in question 12.

**Guardrails** (principles.md section 4)

10. What runs before merge, and is it one command or several? Formatter, linter, type checker, import guard, tests. What is missing, and what would each missing one catch in this repo? Is the gate required to merge, and does it pass at HEAD? If you cannot run it read-only, read CI's run on HEAD. Read branch protection with the forge's CLI, read-only. If neither CI nor the forge is visible, answer `unknown`.
11. Type strictness: compiler flags, checker mode, which files they cover, and what they ignore.
12. For each anti-pattern id in principles.md section 5: present or absent, count, sanctioned hits excluded, enforced today or not.

**Verification** (principles.md section 6)

13. Is there a project verification skill? Find it by what it does, a launch and drive recipe plus a feature map, usually `verify-*` under `.claude/skills/` or `.agents/skills/`. Does its feature map cover every entrypoint the repo has: routes, user commands, jobs? Name any drift.
14. Name the one promise where a break harms users most and tests rarely reach the case that breaks it. Cite a test, trace, or reproduction that shows the case. A case you only reasoned out goes under Not checked. With no such promise, answer `n/a`. Which check fails when the promise breaks there: a test, or a project skill that runs a model checker or prover? Name any stale formal check.

**Prose** (principles.md section 4)

15. For each rule on code in the agent instruction files, project skills, and the docs they point to: what enforces it? A rule a check could enforce and none does joins or opens the finding for the rule or id it states, else is a guardrail gap, with Enforcement today `prose`. A line that excuses a shortcut is evidence for that shortcut's finding, and the fix deletes it. A judgment call stays prose.

**Debt**

16. Files over the size budget, or packed under it, with line counts and, from git log, how many distinct units of work edited each. Use the repo's budget if it has one, else 400. Tests count.
17. Legacy paths, dead code, and copy-and-modify files an agent would copy. For dead code use an unused-export check or the compiler's unused flags; a grep for callers is the fallback and say so.

## Sorting the answers

Sort every answer. A passing answer may go under Already right, an `unknown` under Not checked. Any other answer goes under Improve or Leave alone.

**Improve** holds three kinds of finding.

- A rule or anti-pattern finding: one of the five contributor behaviors produces the mistake, the repo owns the code, and a fix exists at level 1, 2, or 3 of the ladder. One label per finding. When a rule and an id describe the same evidence, use the id and name the rule in the title. Between two ids, use the narrower. Trace a cluster of one id to its cause. Evidence no id covers that comes from a clash between two styles of code (an imperative core under a declarative view) is a Rule 1 finding, a level 1 redesign.
- A guardrail gap from questions 1, 10, 11, or 15: a gate that fails at HEAD, or a missing formatter, linter, type checker, import guard, strict mode, or check for a constraint only a name or prose states. The fix is level 2 or 3. No behavior needed.
- A verification gap from questions 13 or 14: no skill, drift, a stale formal check, or a harmful break no check fails on. The fix is level 3. No behavior needed.

A redesign the operator has not asked for stays here with all fields and "needs a decision" after the title. Whoever fixes the list skips it until the operator says yes.

Order Improve by risk, then by effort. A gate that fails at HEAD goes first. Risk is how likely an agent is to copy the pattern: `high` when it sits on the path every new unit of work takes, `medium` when it is in a module agents touch sometimes, `low` when it is isolated. A gap takes the risk of the mistakes it leaves unchecked. Effort is `S` for one PR under an hour, `M` for one PR, `L` for several PRs.

**Leave alone** when any of these holds. Say which one.

- The question presumes a shape the repo does not have. The `n/a` answers land here with the presumed shape as the reason.
- Something stronger already enforces it. A doc rule that a type also makes impossible is fine as a doc.
- The cost exceeds the benefit. Count churn and risk, not writing effort. A single-writer module over the size budget whose every section is about one thing. A three-entry registry that has not grown in a year.
- An external constraint owns it: a vendor API, a platform, a file the framework requires, polling a service that offers no events.

**Already right** when the answer passes and agents are likely to copy it. Name it so the next agent protects it.

## Report shape

```markdown
# KISS assessment: <repo> <date>

## Shape
<Three to six lines: what runs where, which durable values exist and who writes them, how the sides talk, which sets grow per contribution.>

| Noun | Job | Lives at | May import | Found by |
| --- | --- | --- | --- | --- |

## Already right
- <what> (<path>). <Why it matters that agents copy it.>

## Improve
### <n>. <title> (<id>, <new | unchanged | reopened>)
Rule or anti-pattern: <rule 1 to 5, an id, guardrail gap, or verification gap>
Behavior: <1 to 5, or none for a gap>
Evidence:
- <path:line> <what>
Count: <number of hits, or 1>
Enforcement today: <none, prose, or the tool>
Fix: <what> at level <1 to 3>. <Why a stronger level does not work.>
Principle skill: <from the KISS SKILL.md table>
Risk: <high | medium | low>
Effort: <S | M | L>

## Leave alone
- <what> (<path>): <which reason above>

## Resolved
- <id>: <what holds it now, path:line>

## Not checked
- <Q<n> or finding id>: <what you could not answer, and why>
```

Keep each finding under 15 lines. One that needs more is two findings.
