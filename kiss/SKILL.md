---
name: kiss
description: "Assess a codebase against the principles behind Dune, the framework agents extend correctly by default. Read-only. Writes a report of what to improve, in what order, and what to leave alone. Run with /kiss."
disable-model-invocation: true
---

# KISS

Keep It Simple, Stupid. A codebase is agent-friendly when a correct open-file edit preserves the whole app's invariants. KISS tells you how far a repo is from that and what to change first.

KISS assumes every contributor is an agent that sees only the files it opened, copies the nearest pattern, and takes the shortest path that compiles. The question behind every finding is the same: does this repo turn that behavior into good code, or into a mistake?

KISS lives in the same skills directory as pstack and links its skills as siblings (`../how/SKILL.md`). Read [`pstack-harness`](../pstack-harness/SKILL.md) first for how to load a sibling skill (the **skill** row) and how to spawn a subagent. Where this file names a pstack skill you will run, load its `SKILL.md` in full and follow it.

## Assess

KISS is read-only. It writes `.kiss/assessment.md` and changes nothing else. Read [`references/principles.md`](references/principles.md), then follow [`references/assess.md`](references/assess.md).

pstack skills used while assessing:

| Need | Skill |
| --- | --- |
| Trace how a subsystem works, where a feature lives, who writes a value | [`how`](../how/SKILL.md) |
| Know why a shape exists before calling it debt | [`why`](../why/SKILL.md) |
| A repo too large to hold in context | [`swarm`](../swarm/SKILL.md), as `references/assess.md` describes |
| Write the report | [`technical-writing`](../technical-writing/SKILL.md), every layer except Diátaxis, then [`unslop`](../unslop/SKILL.md), on free text only, not the template's field lines |

## Principle skills by rule

Each Improve finding names the principle skill for its class, so whoever fixes it reads the reasoning first. An anti-pattern id without a row uses the row of the rule it breaks: `cross-boundary-import` Rule 2, `second-writer` Rule 3, `hand-edited-registry` Rule 4, `suppression-without-reason` Rule 5.

| Rule or anti-pattern | Principle skills |
| --- | --- |
| Rule 1, fewest decisions | [**laziness-protocol**](../principle-laziness-protocol/SKILL.md), [**minimize-reader-load**](../principle-minimize-reader-load/SKILL.md) |
| Rule 2, forbidden dependencies fail | [**encode-lessons-in-structure**](../principle-encode-lessons-in-structure/SKILL.md) |
| Rule 3, one writer | [**separate-before-serializing-shared-state**](../principle-separate-before-serializing-shared-state/SKILL.md), [**model-the-domain**](../principle-model-the-domain/SKILL.md) |
| Rule 4, isolated files, not shared roots | [**separate-before-serializing-shared-state**](../principle-separate-before-serializing-shared-state/SKILL.md), [**model-the-domain**](../principle-model-the-domain/SKILL.md), [**type-system-discipline**](../principle-type-system-discipline/SKILL.md) for closed sets |
| Rule 5, narrow exceptions | [**encode-lessons-in-structure**](../principle-encode-lessons-in-structure/SKILL.md) |
| `nuanced-primitive` | [**encode-lessons-in-structure**](../principle-encode-lessons-in-structure/SKILL.md) |
| `raw-transport` | [**boundary-discipline**](../principle-boundary-discipline/SKILL.md) |
| `god-file` | [**minimize-reader-load**](../principle-minimize-reader-load/SKILL.md) |
| `type-escape-hatch` | [**type-system-discipline**](../principle-type-system-discipline/SKILL.md) |
| `tautological-test` | [**test-behavior-not-implementation**](../principle-test-behavior-not-implementation/SKILL.md) |
| `comments-as-bandaids` | [**fix-root-causes**](../principle-fix-root-causes/SKILL.md) |
| `copy-and-modify` | [**migrate-callers-then-delete-legacy-apis**](../principle-migrate-callers-then-delete-legacy-apis/SKILL.md) |
| `timer-as-sync` | [**fix-root-causes**](../principle-fix-root-causes/SKILL.md) |
| Guardrail or verification gap | [**prove-it-works**](../principle-prove-it-works/SKILL.md) |

## Rules

- An exception lives on the offending line as `kiss-allow(<finding id>): <why>. <issue link>. expires <date>. approved: <who>`. An agent never approves its own exception. A third live exception on one rule means the rule or the design is wrong, and the report says so.
- Every operator correction that names a repo mistake is a finding with evidence and a proposed level. A correction about scope goes under Not checked; one that names a deliberate design goes under Leave alone.

## After the report

KISS does not fix. To act on the report, hand the Improve list, in report order, to the Multi-phase plan playbook in [`poteto-mode`](../poteto-mode/SKILL.md). A repeated mistake class goes to [`correct`](../correct/SKILL.md), and a verification gap to [`create-verification-skill`](../create-verification-skill/SKILL.md) or [`create-formal-verification-skill`](../create-formal-verification-skill/SKILL.md). For a countable finding, the check lands before the cleanup (`references/principles.md` section 4).
