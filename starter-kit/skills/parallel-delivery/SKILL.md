---
name: parallel-delivery
short-description: Splits a request into independent workstreams that can run concurrently with explicit merge contracts.
description: Creates a dependency-aware execution plan that maximizes safe parallelism by isolating read-only analysis, speculative tasks, and serial critical paths.
when_to_use: Use for medium/large tasks with multiple files, tool calls, or validation steps where latency matters.
allowed-tools: Read,Grep,Glob,Task,Bash
argument-hint: "task objective + deadline/latency goal"
---

# Parallel Delivery Skill

## Long description
This skill transforms a sequential request into a DAG-like plan: parallel branches for discovery and low-risk execution, then controlled merge points for integration and verification. It reduces turnaround time while preserving correctness by enforcing explicit interface contracts between branches.

## Parallelization policy
- Batch read-only discovery in parallel.
- Keep writes, migrations, and destructive operations serial.
- Define branch outputs before running branches.
- Add "merge checks" after each branch completion.

## Output format
- Workstreams (A/B/C)
- Inputs needed
- Done condition per stream
- Merge contract
- Final verification checklist
