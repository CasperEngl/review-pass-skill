# Review pass

`review-pass` helps an agent work through pull request review comments end to end.

It is designed to:

- gather review threads for the current PR
- classify each thread as `fix now`, `postpone`, or `won't do`
- apply clear in-scope fixes before replying
- draft short reviewer responses based on the current branch state
- resolve threads that are clearly settled

## Install

Install with the Skills CLI:

```bash
npx skills add https://github.com/CasperEngl/review-pass-skill --skill review-pass
```

For global installation without prompts:

```bash
npx skills add https://github.com/CasperEngl/review-pass-skill --skill review-pass -g -y
```

Browse skills and docs:

- https://skills.sh/
- https://skills.sh/docs

## When to use it

Use `review-pass` when you want help with GitHub PR review comments or similar review-thread workflows.

## What it does

The skill helps the agent:

- inspect the current PR and its review threads
- check local code and diff context before classifying comments
- fix the clear in-scope issues first
- draft concise replies grounded in the current branch state
- resolve only the threads that are clearly settled
