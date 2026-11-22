# Security Policy

## Reporting a vulnerability

Please don't report security problems in a public issue, pull request or
discussion. Use
[**Report a vulnerability**](https://github.com/sadayamuthu/threatdiff/security/advisories/new)
on the repository's Security tab instead. Only the maintainers can see the
report.

Include what you can of:

- the ThreatDiff version (`threatdiff version`) or commit, your Go version and OS
- how ThreatDiff was used, and its configuration with secrets removed
- steps to reproduce
- what happened and what you expected

You'll get a reply when the report has been read, and again when a fix is
ready. A fix ships as a patch release; the advisory names you unless you'd
rather it didn't.

## Supported versions

ThreatDiff is at the design stage and has no releases yet. Once it does,
fixes land in the latest release only, until 1.0.

## What counts

ThreatDiff reads configuration files in your repository and writes findings. It runs no models and makes no network calls.

ThreatDiff is a security tool, so anything that breaks one of these promises
is a security issue:

- **Local and deterministic.** ThreatDiff makes no network calls and the same config gives the same graph. A path that sends repository content anywhere is in scope.
- **Parsing untrusted config is safe.** ThreatDiff runs on pull requests from anyone. A config file that makes it execute code, read files outside the repository, or hang is in scope.

These are by design, and not vulnerabilities:

- A missed attack path is a bug, not a vulnerability. Report it in an issue with the config that shows it.
- ThreatDiff sees declared capabilities, not what code does at runtime.

A wrong result that isn't covered above is a bug, not a vulnerability. Report
it in a normal issue; see [CONTRIBUTING.md](CONTRIBUTING.md).
