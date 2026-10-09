# Evals

Test prompts for checking that a skill triggers and behaves as intended — for maintainers; not installed with the plugin's skills.

| File | Skill | What it covers |
|---|---|---|
| `operator/evals.json` | `operator` | Find and start an unfamiliar workflow, diagnose and retry failed jobs, approve a waiting manual task |

Each entry has a `prompt` and an `expected_output` describing the API calls a correct run makes. Run them with your evaluation tool of choice (for example the `skill-creator` plugin's eval runner, pointing `--skill-path` at `skills/operator`), against a non-production platform.
