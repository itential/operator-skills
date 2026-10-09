# Install, Run, and Update — per Tool

Find your tool below. Each section is self-contained: install, check it worked, run your first skill, update later.

Every install path gets the **complete** skills: each skill carries everything it needs inside its own folder, so nothing depends on a clone or on which tool you use.

**Which repo to install from?**
- Using the skills as Itential ships them → `itential/operator-skills` (what the commands below show).
- Your org has its own copy with customizations → use your copy's name instead (e.g. `acme/operator-skills`) in every command. Setting up a copy: [`customization.md`](customization.md).

Not sure yet? Start with Itential's. Moving to your own copy later is just a reinstall — see [Switching to your own copy](#switching-to-your-own-copy).

**Know when there's an update:** on GitHub, **Watch → Custom → Releases** on `itential/operator-skills`.

---

## Claude Code

**Install** — in Claude Code:
```text
/plugin marketplace add itential/operator-skills
/plugin install itential-operator-skills@itential-operator-skills
```
Or from a terminal: `claude plugin marketplace add itential/operator-skills && claude plugin install itential-operator-skills@itential-operator-skills`.

Restart Claude Code once it finishes.

**Check it worked:** run `/plugin` and look for `itential-operator-skills` under installed plugins, or type `/itential-operator-skills:` — the skills appear as suggestions.

**Run a skill:**
```text
/itential-operator-skills:operator
```

**Update:** Claude Code updates plugins in the background. To update now: `/plugin update itential-operator-skills@itential-operator-skills`, then restart.

<details><summary>Working from a clone?</summary>

`git clone https://github.com/itential/operator-skills.git`, then start Claude Code with the clone loaded as a plugin: `claude --plugin-dir /path/to/operator-skills`. Same shortcuts (`/itential-operator-skills:operator`). Update with `git pull`.
</details>

---

## Codex CLI

**Install** — in a terminal:
```bash
codex plugin marketplace add itential/operator-skills
codex plugin add itential-operator-skills@itential-operator-skills
```

**Check it worked:** start `codex` and type `/skills` — the Itential skills are listed as `itential-operator-skills:<skill>`.

**Run a skill:**
```text
$itential-operator-skills:operator
```
Or pick it from `/skills`, or just describe the task and Codex picks the skill.

**Update:**
```bash
codex plugin marketplace upgrade itential-operator-skills
codex plugin add itential-operator-skills@itential-operator-skills
```
Both steps are needed — the first fetches the new version, the second installs it.

**Remove:** `codex plugin remove itential-operator-skills@itential-operator-skills`, then `codex plugin marketplace remove itential-operator-skills`.

<details><summary>Working from a clone?</summary>

`git clone https://github.com/itential/operator-skills.git`, then use the clone as the marketplace: `codex plugin marketplace add /path/to/operator-skills` and `codex plugin add itential-operator-skills@itential-operator-skills`. After `git pull`, re-run the `plugin add` to pick up changes.
</details>

---

## GitHub Copilot in VS Code

**Install — option A, as a plugin** (no clone needed): open the Command Palette (⇧⌘P / Ctrl+Shift+P), run **Chat: Install Plugin From Source**, and enter:
```text
itential/operator-skills
```
VS Code reads the repo's `plugin.json`. Agent plugins are on by default (setting `chat.plugins.enabled`).

**Install — option B, from a clone:** `git clone https://github.com/itential/operator-skills.git`, then **Chat: Install Plugin From Source** with the clone's path as a `file:///` URI (e.g. `file:///Users/you/operator-skills`).

**Check it worked:** in Copilot Chat, type `/` — `operator` appears. Plugin installs also show under **Configure Skills**.

**Run a skill:** `/operator`, or describe the task and Copilot picks the skill.

**Update:** option A → VS Code checks for plugin updates every 24 hours (when `extensions.autoUpdate` is on); to update now, run **Extensions: Check for Extension Updates**. Option B → `git pull`.

**Remove:** right-click the plugin in the **Agent Plugins - Installed** view → **Uninstall**.

> Using VS Code with the **Claude Code** or **Codex** extension instead of Copilot? Follow the [Claude Code](#claude-code) or [Codex CLI](#codex-cli) section — the extension uses the same install and commands.

---

## GitHub Copilot CLI

**Install** — in a terminal:
```bash
copilot plugin marketplace add itential/operator-skills
copilot plugin install itential-operator-skills@itential-operator-skills
```
(Copilot also accepts `copilot plugin install itential/operator-skills` directly, but warns that direct repo installs are deprecated — the marketplace form above is the supported one.)

**Check it worked:**
```bash
copilot plugin list      # shows itential-operator-skills@itential-operator-skills
copilot skill list       # the 2 skills appear
```

**Run a skill:** `/operator`, or describe the task and Copilot picks the skill.

**Update:** `copilot plugin update itential-operator-skills@itential-operator-skills` (or `copilot plugin update` to update every plugin).

**Remove:** `copilot plugin uninstall itential-operator-skills@itential-operator-skills`.

<details><summary>Install into one project instead (gh skill)?</summary>

With GitHub CLI 2.90 or later (`gh skill --help` should work), from inside your project:
```bash
gh skill install itential/operator-skills --agent github-copilot --all
```
Skills land in the project's `.agents/skills/`. Update by re-running with `--force`; pin a release with `--pin v0.1.0`.
</details>

<details><summary>Working from a clone?</summary>

`git clone https://github.com/itential/operator-skills.git`, then `copilot plugin marketplace add /path/to/operator-skills` and `copilot plugin install itential-operator-skills@itential-operator-skills`. Copilot loads the skills live from the clone, so `git pull` (or your own edits) take effect in the next session.
</details>

---

## Cursor

**Install — option A, into your project** (GitHub CLI 2.90 or later):
```bash
gh skill install itential/operator-skills --agent cursor --all
```
Skills land in the project's `.agents/skills/`, which Cursor reads.

**Install — option B, from a clone:** `git clone https://github.com/itential/operator-skills.git`, then from your project: `gh skill install /path/to/operator-skills --from-local --agent cursor --all`.

**For a whole org — Team Marketplace:** a Cursor admin can add the repo under **Dashboard → Plugins & MCPs → Team Marketplaces → Import from Repo** (needs the Cursor GitHub App). The repo ships `.cursor-plugin/` for this. *(From Cursor's docs; not yet tried by hand.)*

**Check it worked:** in Cursor chat, type `/` — `operator` appears.

**Run a skill:** `/operator`

**Update:** option A → re-run the install command with `--force`. Option B → `git pull`, then re-run the install with `--force`. Team Marketplace → refreshes from the repo.

---

## Switching to your own copy

Once your org has a customized copy (see [`customization.md`](customization.md)), remove the Itential install and install from the copy:

| Tool | Remove Itential's, then install yours |
|---|---|
| Claude Code | `/plugin uninstall itential-operator-skills@itential-operator-skills`, `/plugin marketplace remove itential-operator-skills`, then the install steps above with `acme/operator-skills` |
| Codex CLI | `codex plugin remove itential-operator-skills@itential-operator-skills`, `codex plugin marketplace remove itential-operator-skills`, then the install steps above with `acme/operator-skills` |
| Copilot CLI | `copilot plugin uninstall itential-operator-skills@itential-operator-skills`, `copilot plugin marketplace remove itential-operator-skills`, then the install steps above with `acme/operator-skills` |
| Copilot in VS Code (plugin) | Uninstall it from **Agent Plugins - Installed**, then **Chat: Install Plugin From Source** with `acme/operator-skills` |
| `gh skill` installs (Cursor, Copilot) | Re-run the install command with `acme/operator-skills` and `--force` |
| Any clone | `git remote set-url origin https://github.com/acme/operator-skills.git && git pull` |

Nothing to migrate — your org's rules live in the copy, not on your machine.

---

## Quick reference

| Tool | Install | Run | Update |
|---|---|---|---|
| Claude Code | `/plugin install itential-operator-skills@itential-operator-skills` | `/itential-operator-skills:operator` | automatic, or `/plugin update itential-operator-skills@itential-operator-skills` |
| Codex CLI | `codex plugin add itential-operator-skills@itential-operator-skills` | `$itential-operator-skills:operator` | `codex plugin marketplace upgrade itential-operator-skills` + `plugin add` |
| Copilot in VS Code | **Chat: Install Plugin From Source** → `itential/operator-skills` | `/operator` | automatic, or **Extensions: Check for Extension Updates** |
| Copilot CLI | `copilot plugin install itential-operator-skills@itential-operator-skills` | `/operator` | `copilot plugin update itential-operator-skills@itential-operator-skills` |
| Cursor | `gh skill install itential/operator-skills --agent cursor --all` | `/operator` | same command + `--force` |

(Every plugin install first needs its marketplace added — see the tool's section.)

For maintainers: installs read `skills/` directly — there are no per-tool copies to maintain. See [`multi-vendor-architecture.md`](multi-vendor-architecture.md). See [`multi-vendor-architecture.md`](multi-vendor-architecture.md).
