---
name: review-pass
description: Triage pull request review comments, decide which comments to fix now vs postpone vs push back on, implement clear fixes, draft concise replies, and resolve only the threads that are clearly settled.
---

## Purpose

Use this skill when a user wants help working through PR review comments end to end:

- collect the current PR's review threads
- classify each thread as `fix now`, `postpone`, or `won't do`
- implement clear fixes before replying
- draft concise reviewer responses based on the current branch state
- resolve threads that are clearly settled

Use it for GitHub PR review comments and similar review-thread workflows.

## Mode Requirement

For the initial scoping pass, use Plan mode if it is available in the current environment.

- Do the first scope in Plan mode before making edits or drafting final replies.
- Use that step to inventory the active review threads, identify likely `fix now` / `postpone` / `won't do` candidates, and surface any true interpretation questions.
- If the environment exposes a dedicated question tool in Plan mode, use it there for the minimum high-leverage clarification needed.
- If Plan mode is not available, continue with the workflow below in the current mode.

## Workflow

1. Scope the pass.
   - In Plan mode when available, do the initial scope there.
   - Identify the current branch and PR.
   - Fetch review threads, including resolved state, file path, author, and latest replies.
   - Ignore praise-only comments unless the user explicitly wants replies to those too.
   - Build an initial thread inventory before changing code.

2. Read local diff and code context before classifying.
   - Do not classify from the comment text alone when the branch can answer the question.
   - Open the affected files and inspect the current implementation.
   - Check whether the branch already fixed the issue, partially addressed it, or intentionally took a different approach.

3. Classify each thread.
   - `Fix now`: the reviewer is right, the change is small or clearly in scope, or the current branch state is meaningfully worse without it.
   - `Postpone`: the suggestion is reasonable but expands scope, is cleanup/refactor follow-up, or needs a separate pass to do well.
   - `Won't do`: the reviewer is mistaken, the branch already handles the concern adequately, or the suggested change would make the PR worse.

4. Ask only on real interpretation points.
   - Ask the user when classification depends on product intent, wording preference, or scope appetite.
   - Do not ask when repo state makes the answer clear.
   - If a dedicated question tool is available, use it. Otherwise ask directly and keep it short.

5. Apply the `fix now` items first.
   - Make the code changes before writing final review replies.
   - Verify the changed code with the smallest useful check.
   - Base replies on the branch's current state after fixes are applied.

6. Draft replies.
   - Keep replies short and concrete.
   - Prefer one idea per reply.
   - If fixed: say `Fixed.` and summarize the change in user terms.
   - If postponed: say it is reasonable but better as follow-up.
   - If won't do: explain the reason directly, grounded in current code or scope.

7. Resolve threads.
   - Resolve when the thread is clearly settled by a code change or by a precise explanation.
   - Leave threads open when the reviewer might still reasonably disagree about scope, UX, naming, architecture, or follow-up cleanup.

## Classification Heuristics

### Fix Now

Choose `fix now` when one or more are true:

- correctness bug
- broken fallback or poor degradation path
- misleading or user-hostile copy
- type or API semantics are clearly wrong
- small cleanup with obvious benefit and low churn
- reviewer asks for clarification and the code should be improved, not just explained

Typical examples:

- wrong sentinel value such as `""` vs `null`
- missing error handling on non-critical enhancement code
- ambiguous user-facing status text

### Postpone

Choose `postpone` when one or more are true:

- valid cleanup, but not necessary for this PR
- reusable abstraction suggestion that broadens the PR
- test fixture or helper extraction that is nice-to-have
- architectural follow-up that deserves its own change

Typical examples:

- deduplicating test setup across specs
- extracting a shared page helper
- broader refactor around existing defensive code

### Won't Do

Choose `won't do` when one or more are true:

- the branch already covers the concern
- the reviewer is asking what a change does, and explanation is enough
- the suggested change conflicts with current product behavior or scope
- the current implementation is intentionally shaped by runtime or platform constraints

Typical examples:

- middleware needs an edge-safe version because the existing utility depends on a node-only package
- a new file is generated infrastructure, not accidental complexity
- a behavior change was intentional and the new UX owns the responsibility elsewhere

## Reply Rules

- Always reply from the current branch state, not from intended future work.
- Prefer plain language over implementation detail unless the implementation detail is the reason.
- For user-facing copy comments, explain the user outcome, not the internal fallback mechanism.
- For technical justification comments, point to the concrete constraint:
  runtime, package format, cache behavior, existing test coverage, or scope boundary.
- Avoid defensive wording and long backstory.

Good patterns:

- `Fixed. This now uses null instead of an empty string when token creation fails.`
- `Covered in this PR by the added multi-UUID lock test in effect.utils.test.ts.`
- `Reasonable cleanup, but I’d keep that as a follow-up instead of expanding this PR.`
- `This split is for the middleware runtime. The existing utility uses the node-only jose package, so middleware needs its own edge-safe version.`

## Resolve Rules

Resolve the thread when:

- the code now matches the requested fix
- the reviewer asked what a change is for, and the explanation is specific and sufficient
- the thread is praise-only

Leave the thread open when:

- the reply says `follow-up`, `later`, `separate PR`, or similar
- there is still product or architecture interpretation involved
- the user explicitly wants to keep it open

## Output Checklist

When handling a PR review pass, provide:

- the list of open reviewer threads
- the proposed classification for each thread
- any fixes applied
- the final reply text for each thread
- which threads were resolved and which were intentionally left open
