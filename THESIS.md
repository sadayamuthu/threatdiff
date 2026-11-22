# ThreatDiff: thesis

**In one line:** an agent's threat model can be computed from its
configuration, and the useful unit is the diff on a pull request.

## The problem

AI threat models are drawn once, before launch, and go stale with the first
PR that adds a tool or a data source. Risky changes look harmless line by
line: a reviewer sees `send_payment` added, not that the same agent reads
inbound email. The danger is in the combination, and combinations don't show
up in a diff.

## The bet

1. **Configuration is enough.** What an agent can read, what it can do and
   what triggers it are declared in MCP configs, framework code and settings
   files. A capability graph built from those finds the paths that matter
   without running the agent.
2. **Determinism beats depth.** A local tool with no model in the loop, that
   gives the same answer for the same config, will be trusted in CI where a
   smarter but noisier tool won't.
3. **Diffs get read; inventories don't.** Reporting only the paths a PR opens
   or closes keeps the signal high enough that developers act on it.

## Why now

Agents are being assembled from off-the-shelf MCP servers by developers who
are not security reviewers, and coding agents are editing those configs
themselves. The "lethal trifecta" (private data, untrusted input, egress in
one agent) gives a crisp, widely understood rule to check.

## What success looks like

- On real open-source agent repos, ThreatDiff finds the trifecta and
  untrusted-to-sensitive paths that a manual review would, with few findings
  reviewers dismiss as noise.
- It runs in seconds as a pre-commit hook and GitHub Action.
- Other tools (AgentRisk first) consume its graph rather than building their
  own.

## What would prove us wrong

- Too much of the real capability surface lives in code paths and runtime
  state that static parsing can't see, so the graph misses the paths that
  matter.
- Agent config formats churn faster than parsers can follow.
- Teams treat findings as noise because "untrusted" and "sensitive" labels
  need too much hand-tuning per project.

## Not a bet on

Runtime enforcement (IntentLock, Taintline) or scanning models and training
pipelines.

## Where it fits

Area VII, AI Threat Modeling & Risk Assessment. Labels from
[OATC](https://github.com/sadayamuthu/oatc); feeds
[AgentRisk](https://github.com/sadayamuthu/agentrisk).
