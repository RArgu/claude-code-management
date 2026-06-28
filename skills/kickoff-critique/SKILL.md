---
name: kickoff-critique
description: Adversarial review of a /kickoff message. Criticize, counter-criticize, rewrite, then sync TODO/HANDOFF references.
user-invocable: true
---

# /kickoff-critique — Adversarial Kickoff Review

Run after `/kickoff` to stress-test the kickoff message before the user copies it.

## Step 1: Criticize the kickoff

Evaluate the most recent kickoff message against these failure modes:

- **Priority ordering**: Are the priorities in the right sequence? Does a
  dependency exist that isn't stated? Is a 10-minute task inflated into a full
  priority?
- **Hidden scope**: Is work buried in "bonus" or "while we're at it" language?
  Either it's in scope or it's not.
- **Missing references**: Does every priority point to a specific `TODO.md`
  section or file? Can the next session find the steps without guessing?
- **Wasted words**: Negative scope ("don't do X this session"), obvious
  context, information already restated from `HANDOFF.md`.
- **Dependency clarity**: If priorities must run sequentially, is that explicit?
  If they can run in parallel, is that clear?
- **Verifiability**: Can someone tell when each priority is "done"?

List each issue found.

## Step 2: Criticize the criticism

For each issue from Step 1, ask: is this a real problem or an overcorrection?

- Would fixing this actually help the next session, or is it pedantic?
- Does the criticism introduce its own problem (e.g. over-specifying kills
  useful flexibility)?
- Is the issue pre-existing, or introduced by this kickoff?

Mark each criticism as **valid**, **partially valid**, or **overcorrection**.

## Step 3: Rewrite

Rewrite the kickoff incorporating only the valid and partially-valid critiques.
Keep the same format: priorities (max 3) with sequencing if needed, key context
(3-5 bullets), the file-read list, and the closing "Interview me on scope before
proceeding." Under 300 words. **Output the rewritten kickoff inside a single fenced
code block (```), with ONLY the kickoff text inside the fence**, so the user can copy
it in one click — keep your critique notes and commentary OUTSIDE the fence.

## Step 4: Sync references

Read `TODO.md` and `HANDOFF.md`. For every section or file referenced in the
rewritten kickoff:

1. Verify the section exists and contains actionable content.
2. If a reference points to a section that's vague or missing steps, add the steps.
3. Update the `HANDOFF.md` "next session" notes to match the kickoff priorities.
4. Commit any changed files.

Present the final kickoff to the user for copying — in the single fenced code block
from Step 3 (only the kickoff text inside the fence).
