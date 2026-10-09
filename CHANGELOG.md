# Changelog

Versions correspond to the plugin manifest version (`plugin.json`) and the GitHub
release tag — what every tool's update installs.

## Unreleased

## 0.1.0

- Added the `operator` skill for running automations, monitoring jobs, diagnosing and retrying failures, approving manual tasks and managing triggers on the Itential Platform, checked against Itential Platform 6.5
- Added native installs for Claude Code, Codex CLI, GitHub Copilot (CLI and VS Code) and Cursor, each reading the same `skills/` folder
- Added per-skill `custom/org`, `custom/team` and `custom/dev` folders for an organization's own rules, kept separate from Itential's content so updates never overwrite them
