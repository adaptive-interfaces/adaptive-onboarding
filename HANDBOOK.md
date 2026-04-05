# Team Handbook

This file is a companion to the AO configuration files in `.agent/`.

AO configuration files capture what an AI agent needs
to generate conforming code and configuration.
This handbook captures how the team works together —
conventions that matter to humans
but do not need to be machine-readable.

If you are looking for toolchain, style, or coding conventions,
see `.agent/ao-config.toml` and its language and type layers instead.

## What belongs here vs in AO config

| AO config (`.agent/`) | Handbook (this file) |
|---|---|
| Toolchain and linter config | On-call rotation and escalation |
| Test conventions and coverage | Incident severity definitions |
| Docstring style | How to run a postmortem |
| Branch protection rules | Team meeting cadence |
| PR template requirements | How to request time off |
| Dependabot configuration | Budget approval process |
| Logging and exception conventions | Vendor relationships |
| Release workflow | Promotion and performance criteria |

## Team

| Name | Role | GitHub | Areas |
|---|---|---|---|
| | | | |

## Communication

| Channel | Purpose |
|---|---|
| | |

## Meetings

| Meeting | Cadence | Purpose |
|---|---|---|
| | | |

## On-Call and Incidents

### Rotation

<!-- Who is on call and how does the rotation work? -->

### Severity Levels

| Level | Definition | Response Time |
|---|---|---|
| P0 | | Immediate |
| P1 | | |
| P2 | | |

### Escalation Path

<!-- Who do you contact if the on-call person is unreachable? -->

### Postmortem Process

<!-- When is a postmortem required and how is it run? -->

## Development Workflow

### Branching

<!-- Branch naming conventions, e.g. feat/, fix/, chore/ -->

### Pull Requests

<!-- What makes a good PR? What do reviewers focus on? -->

### ⭐ Code Review Culture

<!-- What does the team care about that is not enforced by CI?
     e.g. "we reject PRs with missing docstrings even if tests pass"
     This is the review culture that AO config cannot capture. -->

### Definition of Done

<!-- What does "done" mean beyond CI passing?
     e.g. docs updated, changelog entry added, demo given -->

## Release Process

<!-- How does a change get to production?
     Tag, workflow, manual step — whatever applies. -->

## Environments

| Name | Purpose | Access |
|---|---|---|
| | | |

## Monitoring and Alerting

<!-- What tool, who gets alerted, and what does an alert require? -->

## Dependencies and Vendors

<!-- External services, APIs, or vendors the team relies on. -->

## Security and Compliance

<!-- Policies that go beyond what tools enforce:
     e.g. data classification, retention, access review cadence. -->

## New Team Member Checklist

- [ ] Access to repositories
- [ ] Access to communication channels
- [ ] Development environment set up
- [ ] `.agent/` config files loaded and verified
- [ ] First PR submitted and reviewed
- [ ] On-call rotation added

## Decisions and History

<!-- Link to ADRs, DECISIONS.md files, or other records of past decisions
     that shaped how the team works. -->
