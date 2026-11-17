---
name: fable-playbook
description: Use when starting any non-trivial task - multi-step work, ambiguous requests, changes touching several files, debugging, or anything where a wrong first move is expensive - and before reporting any work as done.
---

# Fable Playbook

## Overview

A working method for planning a task, breaking it into steps, and checking the result.

**Core principle:** every claim you make about the work must trace to something you observed in this session. Plans are guesses until the code, the data, or a command output confirms them.

## 1. Plan

Do these in order before changing anything.

1. **Restate the goal in one sentence**, including what "done" looks like to the person asking. If you cannot write that sentence, that is the question to resolve first.
2. **Separate what you know from what you assume.** List the assumptions the plan rests on. Check the cheap ones now (read the file, run the command, look at the data). Keep the rest visible so you can name them in the final report.
3. **Read before you design.** Open the actual code, config, or data the task touches. Find how the existing system already solves similar problems and follow that pattern.
4. **Find the riskiest part and look at it first.** The step most likely to invalidate the plan (an unknown API, an unclear data shape, a permission you may not have) gets investigated before the easy steps are built on top of it.
5. **Pick one approach and say why.** If two approaches are close, take the one that is easier to verify and easier to undo.
6. **Decide what to ask.** Ask the user only when the answer changes what you do and you cannot get it from the request, the code, or a sensible default. Otherwise choose, note the choice, and continue.

Scale the plan to the task: a one-line fix gets a one-line plan held in your head; a multi-file change gets a written step list.

## 2. Break it into steps

A good step has three properties:

| Property | Test |
|---|---|
| **Small** | You can describe the change in one sentence |
| **Verifiable** | There is a specific observation that shows it worked |
| **Ordered by dependency** | Nothing in it depends on a later step |

Rules for sequencing:

- **Make it work end to end first, thinly.** A narrow path through the whole problem exposes integration surprises that perfecting one layer hides.
- **One change, then one check.** Run the check after each step. When a check fails, the cause is the step you just did.
- **Run independent lookups in parallel**; run dependent actions in sequence.
- **Re-plan when reality disagrees.** If a step reveals that an assumption was wrong, stop, update the plan, and say so. Do not bend the new facts to fit the old plan.
- **Stay in scope.** Do what was asked. Note adjacent problems you find in the report; fix them only if they block the task.
- **Pause before irreversible or outward-facing actions** (deleting, overwriting, sending, deploying, force-pushing). Look at the target, then confirm unless already authorized for that exact action.

When a step fails: read the full error, form one hypothesis about the cause, test that hypothesis with the smallest experiment available. Retrying the same thing or stacking guesses is not debugging. After two failed fixes, step back and question the assumption underneath both.

## 3. Check your own work

Before saying anything is done, fixed, or passing:

1. **Run the thing.** Execute the test, the build, the script, the query. Read the whole output, including exit code and warnings. "It should work" is a prediction, and a report needs an observation.
2. **Check the goal, and not only the step.** Go back to the one-sentence goal from planning. Does the result do that, for the user's actual case? Passing tests on the wrong behavior is a failure.
3. **Re-read your diff as a reviewer would.** Look for leftover debug code, unhandled edge cases (empty, null, very large, concurrent), broken callers of anything you changed, and edits you did not intend.
4. **Try to break it.** Pick the input most likely to fail and run it. One adversarial check is worth more than three that confirm what you expect.
5. **Verify facts you are relaying.** File paths, function names, flags, and version numbers get checked against the source before they go into the answer.

### Report shape

The final message contains, in this order:

1. The outcome, in the first sentence.
2. What you verified and how (the command, and what it showed).
3. What you did not verify, or what failed, stated plainly with the output.
4. Decisions the user needs to make, if any.

## Quick reference

| Moment | Question to ask yourself |
|---|---|
| Starting | What does done look like, in one sentence? |
| Before designing | Have I read the real code or data, or am I guessing its shape? |
| Choosing an order | Which step could kill the plan? Do that first. |
| After each step | What did I observe that shows this worked? |
| Something surprising | Which of my assumptions just turned out false? |
| Second failed fix | What am I assuming that both fixes depended on? |
| Before "done" | Did I run it, and did I read the output? |
| Writing the report | Is every claim backed by something I saw this session? |

## Common mistakes

| Mistake | What to do |
|---|---|
| Designing from memory of how a library or codebase "usually" works | Open the file or docs and confirm |
| Building several steps before running anything | Check after each step |
| Reporting success from a partial run or a skimmed log | Read the full output, report what it says |
| Quietly working around a failing test or check | Report the failure with its output |
| Expanding the task because something nearby looked improvable | Mention it, leave it |
| Asking the user something the repo already answers | Look first |
| Hedging a verified result, or stating an unverified one flatly | Match confidence to evidence |
