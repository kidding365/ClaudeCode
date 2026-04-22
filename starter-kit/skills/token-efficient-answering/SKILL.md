---
name: token-efficient-answering
short-description: Produces high-signal answers with controlled verbosity and minimal redundant reasoning tokens.
description: Enforces concise, structured responses with strict relevance filtering, progressive disclosure, and compact evidence summaries.
when_to_use: Use when users request concise outputs, low latency, cost sensitivity, or repeated iterative refinement.
allowed-tools: Read,Grep,Glob
argument-hint: "question + desired brevity level"
---

# Token Efficient Answering Skill

## Long description
This skill improves answer quality-per-token by prioritizing decision-useful information and deferring optional detail. It enforces a compact response skeleton and removes repeated caveats, overlong restatements, and speculative side paths.

## Compression rules
1. Lead with answer first.
2. Cap sections to 3-5 bullets unless user asks for depth.
3. Use tables only when they reduce token count.
4. Collapse repeated qualifiers into one assumptions block.
5. Offer optional "expand" section instead of default long prose.

## Output format
- Direct answer
- Key evidence (short)
- Risks/unknowns
- Next best step
