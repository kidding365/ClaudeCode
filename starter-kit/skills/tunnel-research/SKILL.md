---
name: tunnel-research
short-description: Focuses exploration into a narrow, testable research direction with explicit assumptions and stop criteria.
description: Converts broad user goals into a constrained research track by defining hypothesis, boundary conditions, signal metrics, and clear "go/no-go" decisions.
when_to_use: Use when the user asks broad questions, strategy ideation, or exploratory architecture choices and wants practical direction quickly.
allowed-tools: Read,Grep,Glob,WebSearch
argument-hint: "topic + target outcome + constraints"
---

# Tunnel Research Skill

## Long description
This skill prevents diffuse brainstorming. It narrows the problem into one primary direction with two bounded alternatives. It explicitly defines assumptions, decision checkpoints, and evidence quality standards so each turn contributes to decision confidence rather than adding unstructured context.

## Behavior
1. State the **single primary hypothesis** in one sentence.
2. List max 3 assumptions with confidence (high/med/low).
3. Define disconfirming evidence (what would invalidate the direction).
4. Propose 2 fallback directions only if primary fails.
5. End with next concrete action and expected evidence.

## Output format
- Goal
- Primary direction
- Why now
- Evidence to gather
- Stop criteria
- Next action
