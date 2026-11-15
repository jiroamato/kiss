# KISS: Keep It Simple, Stupid

A skill that assesses a codebase against the principles behind Dune, the framework Lauren Tan (@poteto) built at SpaceX AI so agents could ship thousands of PRs a month to Grok Bot. It reports what to improve, in what order, and what to leave alone. It is read-only; the fixing goes through pstack.

The one-line test, from the Dune docs: **a correct open-file edit preserves the whole app's invariants.**

Dune is closed source and Electron-specific. KISS takes its ideas, drops the Electron, and turns them into an assessment any repo can run.

## What is here

| Path | What it is |
| --- | --- |
| `kiss/` | The skill. `SKILL.md` is the entry, `references/` holds the principles and the assessment procedure. |

## The skill

`/kiss` reads a repo and writes `.kiss/assessment.md`: the repo's shape as a noun table, what is already right, what to improve ranked in the order it should land, and what to leave alone and why. It changes nothing else.

| File | Holds |
| --- | --- |
| `SKILL.md` | The entry: which pstack skills the assessment uses, the principle skill for each finding class, and where to hand the report |
| `references/principles.md` | The contributor model, the five rules, how to derive a repo's nouns, the ladder, the anti-pattern list, verification |
| `references/assess.md` | The question list, how to sort answers into improve or leave alone, the report shape |

## Install

KISS links pstack's skills as siblings, so it lives in the same skills directory pstack was installed to. pstack is poteto's, at https://github.com/cursor/plugins/tree/main/pstack. KISS uses the Claude Code and Codex port at https://github.com/jiroamato/pstack.

Install pstack with `npx @jiroamato/pstack`, then link `kiss/` (the skill folder, not the repo root) into the directory it chose:

| Harness | Project scope | Global scope |
| --- | --- | --- |
| Claude Code | `.claude/skills/kiss` | `~/.claude/skills/kiss` |
| Codex | `.agents/skills/kiss` | `~/.agents/skills/kiss` |

PowerShell, global scope on Claude Code:

```powershell
New-Item -ItemType SymbolicLink -Path "$HOME\.claude\skills\kiss" -Target "$PWD\kiss"
```

macOS or Linux, global scope on Claude Code:

```bash
ln -s "$PWD/kiss" ~/.claude/skills/kiss
```

With pstack installed as a plugin instead, copy `kiss/` into the pstack checkout at `pstack/skills/kiss/` and reinstall the plugin. Every sibling link then resolves inside the plugin, and KISS is `/pstack:kiss`.

Then `/kiss` in any project.

## Relationship to pstack

pstack governs how agents work. KISS says what the repo should look like. It reads pstack's skills and routes to them: `how` and `why` while assessing, then for the fixes it hands off, poteto-mode's Multi-phase plan playbook for the Improve list, `correct` for a repeated mistake class, and `create-verification-skill` for a repo with no scripted way to prove itself.

## Status

v1 of the assessment, revised once after its first run against a real repo.

## Sources

Dune is described by Lauren Tan in four public talks, which also show its docs page and slide deck.

- How I Shipped 2000 PRs Last Month: https://www.youtube.com/watch?v=NjoZoUm85x0
- Grok Bot team workshop: https://www.youtube.com/watch?v=_zWMTyKxMt4
- Matt Pocock live interview: https://www.youtube.com/watch?v=MN9dGgmLyso
- All Code Is Slop (Except Mine), AI Engineer Code Summit: https://www.youtube.com/watch?v=O_zcc6Xl12g
