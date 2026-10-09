# Itential Platform — Operator Guide

**Cross-tool note:** Skills live in `skills/{skill-name}/SKILL.md`, each self-contained (any files it needs sit inside its own folder). Every install method reads `skills/` directly — there are no per-tool copies. Plugin manifests: `.claude-plugin/` (Claude Code), root `plugin.json` (Codex, Copilot, VS Code), `.cursor-plugin/` (Cursor). Invoke: `/itential-operator-skills:operator` (Claude Code), `$itential-operator-skills:operator` (Codex), `/operator` (Copilot, Cursor). See `docs/vendor-install.md`.

**Paths:** skills refer to their files by paths relative to the skill's own folder (`assets/...`, `scripts/...`).

> ## Customization Layers
>
> Each skill's `skills/<name>/custom/` folder — `org`/`team` committed in a customer's own copy of this repo, `dev` personal and gitignored; always empty upstream (see `docs/customization.md`). Checked before acting, in precedence order, highest first:
> 1. `skills/<name>/custom/dev/`
> 2. `skills/<name>/custom/team/`
> 3. `skills/<name>/custom/org/`
> 4. Core guidance — `AGENTS.md`, `skills/` — lowest priority. Everything above may narrow or override it, but must not weaken a skill's safety rules or put credentials in committed files.

This project contains skills for operating the Itential Platform. The operator persona runs automations, monitors jobs, handles failures and manual tasks, and manages triggers — not building workflows (that's Itential's builder skills) or administering the platform (Itential's admin skills).

## Skill Router

| Skill | Owns | When to Use |
|-------|------|-------------|
| `/operator` | **Operations** | Find and run automations, monitor jobs, diagnose and retry failures, approve manual tasks, manage endpoint/manual/scheduled triggers. |

## Other Itential skill packs

| Pack | Repo | Plugin | For |
|---|---|---|---|
| **Builder** | [`itential/builder-skills`](https://github.com/itential/builder-skills) | `itential-builder` | Design, build and test automations — spec, feasibility, design, build, QA and as-built |
| **Admin** | [`itential/admin-skills`](https://github.com/itential/admin-skills) | `itential-admin-skills` | Platform health, adapters and applications, users, groups, roles, service accounts, SSO, integrations |
| **Operator** (this repo) | [`itential/operator-skills`](https://github.com/itential/operator-skills) | `itential-operator-skills` | Run automations, monitor jobs, diagnose and retry failures, approve manual tasks, manage triggers |

**Where the line is.** This pack runs automations that are already delivered: starting jobs, watching them, retrying failures, approving manual tasks, managing triggers. Building or changing workflows — and testing or certifying a delivery, which Builder's `qa-agent` does by running jobs itself — → **Builder**; platform health, adapters, users and access → **Admin**.

If a request belongs to another pack, say which pack covers it and point to that repo's `docs/vendor-install.md` for installing it in the tool being used, rather than improvising from general knowledge. If that pack is already installed, use its skill.

## Key Rules

1. **Find the automation first** — `GET /operations-manager/automations?contains=...&containsField=name` shows what's available.
2. **Get the inputs from the workflow** — automations don't store `inputSchema`, the workflow does: `GET /automation-studio/workflows?equals[_id]={componentId}&include=name,inputSchema`.
3. **Start jobs by workflow name** — `POST /operations-manager/jobs/start` takes `workflow` (the name), not the automation ID. Variables go in `options.variables`, not at the top level.
4. **Check `job.error` for failures** — not just `job.status`. The error array names the failing task and why.
5. **Retry the failed task, don't restart the job** — `POST /operations-manager/jobs/{jobId}/tasks/{taskId}/retry` keeps the job's context and completed work.
6. **Manual tasks: claim → review → finish** — find them with `equals[type]=manual&equals[status]=running`; finish with `{taskData: {finish_state, variables}}`.
7. **Use triggers for repeatable runs** — endpoint triggers for API integration, schedule triggers (`firstRunAt` + `repeatInterval` in ms, no cron) for timed runs, manual triggers for a form in the UI.
8. **Operations Manager responses are `{message, data, metadata}`** — read from `data`.
9. **Filters differ by endpoint, and a wrong one is silently ignored** — jobs and tasks use brackets (`equals[status]=error`); automations and triggers use field pairs (`equals=schedule&equalsField=type`); Automation Studio uses brackets (`equals[_id]=...`).

## When Something Doesn't Work

1. **Read the error message** — Itential errors are specific (`"Missing Params"`, `"No such Method"`, `"Cannot find workflow"`, `"Parameter 'equals' must be used in conjunction with 'equalsField'"`).
2. **A list returns far more than expected** — the filter was ignored. Check rule 9 for that endpoint's filter style.
3. **Check `openapi.json`** — fetch it if not local: `curl -s "{BASE}/help/openapi?url={ENCODED_BASE}" -H "Authorization: Bearer {TOKEN}" > openapi.json`. Then search: `jq '.paths["/the/endpoint"]' openapi.json`, and read query parameters with `jq '.paths["/the/endpoint"].get.parameters[].name' openapi.json`.
4. **Find the body wrapper** — `jq '.paths["/the/endpoint"].post.requestBody.content["application/json"].schema.properties | keys' openapi.json` → the key name is the wrapper.
5. **If the schema is empty** — check the related POST endpoint for the pattern, or send `{}` and read the `"Missing Params"` response as a last resort.
6. **Something you expect is missing** — a workflow or automation in a project your account can't see doesn't appear in lists. Ask the project owner to add your account; don't assume it was deleted.
7. **Re-authenticate after permission changes** — tokens don't pick up new roles until you get a new one.
