# Changelog

Versions correspond to the plugin manifest version (`plugin.json`) and the GitHub
release tag — what every tool's update installs.

## Unreleased

- Added an auth helper (`skills/operator/scripts/auth.py`) and changed the skill to use it: it reads `.env`, gets a token, writes `.auth.json` and a curl config `.auth.curl` (both mode 600) and prints one status line, so the agent never reads `.env` or puts the client secret in a command. Calls use `curl -K .auth.curl`, so the token never appears in a command either. The skill's description also tells the agent not to read `.env`, since a model can open it before the skill loads. In a test on a live platform, one model given the old instructions read the file and put the secret in its commands
- Changed the token transport for local `/login` tokens from a `?token=` URL parameter, which ends up in logs and shell history, to the `token` cookie (set by `.auth.curl`); OAuth tokens stay in the `Authorization: Bearer` header

## 0.1.0

- Added the `operator` skill for running automations, monitoring jobs, diagnosing and retrying failures, approving manual tasks and managing triggers on the Itential Platform, checked against Itential Platform 6.5
- Added native installs for Claude Code, Codex CLI, GitHub Copilot (CLI and VS Code) and Cursor, each reading the same `skills/` folder
- Added per-skill `custom/org`, `custom/team` and `custom/dev` folders for an organization's own rules, kept separate from Itential's content so updates never overwrite them
