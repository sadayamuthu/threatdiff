<div align="center">

# ThreatDiff

<h3>Threat models generated from agent configuration, checked on every pull request</h3>

[![Status](https://img.shields.io/badge/status-design%20stage-orange?style=flat-square)](ROADMAP.md)
[![Go 1.26+](https://img.shields.io/badge/go-1.26+-00ADD8?style=flat-square)](https://go.dev/dl/)
[![License](https://img.shields.io/badge/license-Apache%202.0-blue?style=flat-square)](LICENSE)

---

[How it works](#how-it-works) · [Thesis](THESIS.md) · [Roadmap](ROADMAP.md) · [Contributing](CONTRIBUTING.md) · [Security](SECURITY.md)

</div>

---

## What is ThreatDiff?

A threat model that is generated from your agent's configuration and checked
on every pull request. `threatdiff` reads what an agent can see and do (its
system prompt, tools, MCP servers, data sources and triggers), builds a graph
of capabilities, and finds paths from untrusted input to dangerous actions.
On a pull request it reports only what changed: the attack paths the PR opens,
and the ones it closes.

> **Status: design stage.** There is no code yet. The commands, file formats
> and output below are the proposal, and they will change. Part of the
> [OpenAstra](https://openastra.org) research agenda, area VII: AI Threat
> Modeling & Risk Assessment.

---

## The problem

Threat models for AI systems are usually a diagram someone draws once, before
launch. Agents don't stay still: a PR adds an MCP server, widens a tool's
scope, or lets the agent read a new inbox, and the diagram is out of date the
same day. The risky changes look harmless in a diff. Nobody reviewing
`tools: [..., "send_payment"]` sees that the same agent also summarizes
inbound email.

---

## How it works

1. **Collect.** Parse agent definitions from the places they actually live:
   MCP client configs, agent framework code (LangGraph, OpenAI Agents SDK,
   Claude Agent SDK), Claude Code and Codex settings, and a small
   `threatdiff.yaml` for anything it can't infer.
2. **Build the graph.** Nodes are sources (user input, web pages, email,
   files, tool outputs), the agent's context, and sinks (tools with side
   effects). Each node carries a trust level and each sink a sensitivity.
3. **Find paths.** Search for paths from untrusted sources to sensitive sinks,
   and for the *lethal trifecta*: one agent with access to private data,
   exposure to untrusted content, and a way to send data out (term coined by
   Simon Willison).
4. **Label.** Tag each path with OWASP Top 10 for LLM Applications, OWASP Top
   10 for Agentic Applications and MITRE ATLAS identifiers, read from
   [OATC](https://github.com/sadayamuthu/oatc).
5. **Diff.** Compare the graph at the PR's head with the graph at its base and
   report new and removed paths, with block or warn verdicts.

---

## Example (proposed)

```console
$ threatdiff check main..HEAD
NEW PATH  error  inbound email -> support-agent -> send_payment
          untrusted content can reach a payment tool with no approval step
          OWASP LLM01 Prompt Injection, LLM06 Excessive Agency
          MITRE ATLAS AML.T0051 LLM Prompt Injection
          introduced by: agents/support.yaml:14 (tool added)

TRIFECTA  error  support-agent
          private data: crm.read   untrusted: email.inbox   egress: http.fetch

CLOSED    info   web page -> research-agent -> shell.exec
          removed by: agents/research.yaml:9

2 blocking findings. Exit 1.
```

---

## Planned surfaces

- CLI: `threatdiff check`, `threatdiff graph` (Mermaid/DOT/JSON), `threatdiff explain <path>`
- Pre-commit hook and GitHub Action that comment the diff on the PR
- MCP server, so a coding agent can check its own config changes
- SARIF output for code scanning

---

## Maps to

| Framework | Items |
|-----------|-------|
| OWASP LLM Top 10 (2025) | LLM01 Prompt Injection, LLM02 Sensitive Information Disclosure, LLM06 Excessive Agency |
| OWASP Agentic Top 10 | Goal hijack, tool misuse, privilege abuse |
| MITRE ATLAS | AML.T0051 LLM Prompt Injection and related techniques |
| NIST SP 800-53 Rev. 5 | RA-3 Risk Assessment, SA-11 Developer Testing, AC-4 Information Flow Enforcement |

---

## Design principles

- **Local and deterministic.** No model in the loop; the same config gives the same graph.
- **Diff first.** A PR comment lists what changed, not 200 standing findings.
- **Explain every path.** Each finding names the file and line that created it.

---

## Non-goals

- Runtime enforcement. That is [IntentLock](https://github.com/sadayamuthu/intentlock) and [Taintline](https://github.com/sadayamuthu/taintline).
- Scanning model weights or training pipelines.

---

## Roadmap

The plan, from the first working version to 1.0, is in [ROADMAP.md](ROADMAP.md). The reasoning behind the project, and what would make us stop, is in [THESIS.md](THESIS.md).

---

## Related OpenAstra projects

- [OATC](https://github.com/sadayamuthu/oatc): the threat catalog behind the labels
- [AgentRisk](https://github.com/sadayamuthu/agentrisk): turns the graph into a score
- [ControlGate](https://github.com/sadayamuthu/controlgate): the same pre-merge gate model, for NIST controls

---

## Contributing

Design feedback, real-world cases and fixes are welcome. See [CONTRIBUTING.md](CONTRIBUTING.md) for how to help while the project is at the design stage, and [GOVERNANCE.md](GOVERNANCE.md) for how decisions are made.

---

## Security

Please report vulnerabilities privately through [GitHub security advisories](https://github.com/sadayamuthu/threatdiff/security/advisories/new), not in a public issue. See [SECURITY.md](SECURITY.md) for what to include and what's in scope.

---

## License

[Apache License 2.0](LICENSE)

---

<div align="center">

**ThreatDiff: threat models generated from agent configuration, checked on every pull request**

ThreatDiff is managed by [OpenAstra](https://openastra.org)

[GitHub](https://github.com/sadayamuthu/threatdiff) · [Thesis](THESIS.md) · [Roadmap](ROADMAP.md)

</div>
