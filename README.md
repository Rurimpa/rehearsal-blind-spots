# Rehearsal Blind Spots

**A dry run with subagents cannot surface the failures that only appear when
something stops.**

Subagents never stop to ask a human, never open a window, and never appear on a
separate bill. Any failure mode made of those things is structurally invisible
to a subagent rehearsal — not unlikely, *impossible*.

日本語版：[README.ja.md](README.ja.md)

## Use it now

Paste this into your `CLAUDE.md` or `AGENTS.md`:

```markdown
## Rehearsing with subagents
- Before trusting a subagent rehearsal, ask of each failure you care about: "Does it appear by something stopping?"
  (waiting for permission or approval, cost, no window appearing, two sessions with the same name, transcript not saved)
- If yes, a clean rehearsal says nothing about it. Test that part with a real session.
- Use rehearsals for content: the order of steps, ambiguous instructions, overlapping roles.
```

Then:

1. List the failures you care about before the real run.
2. Mark the ones that appear by something stopping.
3. Run only those for real; rehearse the rest.

That is all. The evidence and the limits are below.

---

## The problem

Before running an expensive multi-agent procedure for real, the obvious move is
to rehearse it cheaply: spawn subagents, have them play the roles, see what
breaks, fix it, then do it for real.

This works for content. It does not work for constraints.

A subagent is not a small version of a real session. It runs inside its parent's
permission context, it has no user in front of it, it has no window, and it does
not get approved or denied. Those are exactly the surfaces where a real
multi-agent procedure fails.

So the rehearsal comes back clean, and you conclude the procedure is ready. It
is not. You have tested the half that was never at risk.

## What we observed

We ran the same procedure twice: once as a rehearsal with five subagents, once
for real with five separate agent sessions, with a human at the keyboard.

| | rehearsal (5 subagents) | real (5 sessions) |
|---|---|---|
| **an agent stops to ask the human whether it may take part** | **0 occurrences** — subagents do not stop on permissions | **3 of 5 agents.** The human had to act three times |
| **what the participation procedure should actually say** | a pile of existing clauses. It did not answer the one thing the order asked for — *proceed without human approval wherever possible* — in a single line | the answer appeared: *when called, answer without consulting the human; approval is needed only afterwards, when something gets implemented, changed, or sent* |
| **launching an agent unattended produces no visible window** | did not come up | one agent already had the measurement: three different launch methods, all returning zero window handles |
| **cost control for automating the procedure** | did not come up | raised immediately. It was absent from the agenda *and* from the previous decision |

Four findings. **All four came from the real run. None came from the
rehearsal.** And the first one is not bad luck — a subagent has no human to ask,
so "the agent stops and asks" has no way to occur.

**This is n=1.** One procedure, one workspace, one pair of runs. Field
observation, not a controlled experiment.

## The test

Before trusting a rehearsal, ask one question about each failure you care about:

> **Does this failure appear by something *stopping*?**

If yes, a subagent rehearsal cannot show it to you. Run it for real.

Failures that appear by stopping include, at least:

- **permission and approval** — the agent pauses for a human and the human is
  not there, or is there and says no
- **cost** — the run is refused, throttled, or simply turns out to be expensive
  enough that someone objects
- **visibility** — the session starts but no window appears, or it appears
  somewhere nobody is looking
- **identity and addressing** — two sessions end up with the same name, or a
  message goes to the wrong one
- **persistence** — the session runs but its transcript is not being saved

What a rehearsal *is* good for: the shape of the content, the order of the
steps, whether the instructions are unambiguous, whether the roles overlap.
Those are real and worth rehearsing. Just do not read a clean rehearsal as
evidence about the list above.

## Why it happens

A subagent is spawned to return an answer to its parent. Everything about it is
arranged so that it does not stop:

- It inherits the parent's permission context, so most tool calls that would
  prompt a human simply proceed.
- There is no interactive terminal attached, so anything that would require a
  human decision either auto-resolves or fails silently.
- It has no window, no tab, no name that another session can address.
- Its cost is folded into the parent's, so it does not trip a separate guard.

Each of these is a reasonable design choice. Together they mean the rehearsal is
running with the constraints removed — which is precisely the thing you were
trying to test.

There is a related public record worth knowing: subagent permission handling has
its own documented bugs (permissions not inherited, permission prompts failing
silently, nested permission asks hanging). Those are bugs and may be fixed. The
point here is different and does not go away when they are fixed: **even a
perfectly working subagent has no human to stop for.**

## Limits

- **n=1.** One paired run. We are not reporting a rate.
- **Version-dependent.** How subagents handle permissions and tools changes
  between releases, and public reports already disagree with each other. Check
  the behaviour in your own version rather than trusting this table.
- **Not an argument against rehearsals.** Rehearsing is cheap and it found real
  problems with the *content* in our case. The claim is narrow: a clean
  rehearsal is not evidence about the stopping-shaped failures.
- **Real runs cost real money and real human attention.** That is the whole
  reason rehearsals exist. Use the test above to spend the real runs on the
  parts that need them.

## Prior art

- **Multi-agent failure taxonomies** exist and are useful — one widely cited
  study across seven frameworks found that roughly two thirds of failure modes
  live in coordination rather than in any single agent.
- **Subagent permission bugs** are documented in public issue trackers:
  permissions not inherited from the parent session, permission checks failing
  without prompting the user, nested permission requests hanging forever.
- **Guidance on testing agent systems** recommends testing each agent alone,
  each handoff, and the full orchestration under stress. One guide notes in
  passing that dry runs do not exercise durable approval pauses.
- **What we did not find**: the point stated as a *selection rule* — that the
  rehearsal mechanism itself makes an entire class of failure impossible to
  observe, and the resulting test ("does this failure appear by something
  stopping?") for deciding which runs must be real.
- Absence in our search is not proof of absence. If you know of prior work,
  open an issue and we will credit it.

## License

MIT.
