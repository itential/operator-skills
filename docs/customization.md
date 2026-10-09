# Customizing the Skills for Your Org

Every skill is owned and updated by Itential — don't edit a skill's `SKILL.md`, your changes would be overwritten by the next update. Instead, each skill has a `custom/` folder for your org's rules. The skill reads that folder every time it runs, and applies your rules on top of its own.

You write plain markdown files and commit them to your org's copy of this repo. Every install from that copy includes them — nothing to generate, nobody runs a script.

## Which setup do you need?

| You want… | Use | Rules travel with installs? |
|---|---|---|
| The skills exactly as Itential ships them | Install from `itential/operator-skills` — [`vendor-install.md`](vendor-install.md). Nothing on this page applies. | — |
| Your org's rules applied for everyone | **Your org's own copy** of this repo (this page) | Yes — `custom/org` and `custom/team` |
| To try rules out yourself, or work on the skills | **A clone** loaded into your tool — "Working from a clone" in [`vendor-install.md`](vendor-install.md) | Your `custom/dev` stays on your machine |

You can start with Itential's and move to your org's copy later — it's just a reinstall.

## At a glance

| Who | When | What |
|---|---|---|
| One admin | Once | [Set up your org's copy](#1-set-up-your-orgs-copy-once) |
| Anyone adding a rule | Whenever | [Add a rule](#2-add-a-rule) — write a file, push, check |
| Everyone on the team | Once | [Install from your copy](#3-everyone-installs-from-your-copy) |
| One admin | When Itential releases | [Take Itential's updates](#5-take-itentials-updates) |

---

## 1. Set up your org's copy (once)

Make a **private** copy — not GitHub's Fork button, because a fork of a public repo can't be made private:

```bash
gh repo create acme/operator-skills --private
git clone --bare https://github.com/itential/operator-skills.git
cd operator-skills.git && git push --mirror https://github.com/acme/operator-skills.git
cd .. && rm -rf operator-skills.git
```

(Or use GitHub's **Import repository** page with `https://github.com/itential/operator-skills`.)

Itential's own maintenance workflows (release notes, PR checks) switch themselves off in your copy, so there's nothing to configure.

**Access:** with a private copy, everyone who installs from it needs read access to the repo, and their tool fetches it with their own git credentials. Make sure `git clone https://github.com/acme/operator-skills.git` works for them first (for example after `gh auth login`, or with an SSH key) — if it does, the install will too.

---

## 2. Add a rule

### Decide where it goes

**Which skill?** Put the file in `skills/operator/custom/…` — the folder of the skill that should follow it.

**Which layer?** Match it to who needs to agree:

| If the rule is… | Put it in | Example |
|---|---|---|
| Company policy — wrong for any team to do differently | `org/` | "Never cancel a job that started in the last 10 minutes" |
| A convention your team agreed on | `team/` | "Our scheduled triggers are named `NETOPS-<automation>-<interval>`" |
| Just yours — your sandbox, your test device, an experiment | `dev/` — see [Personal settings](#personal-settings-dev) | "My sandbox platform is `https://dev.example.itential.io`" |

### Write it

One or more `.md` files per folder, any names. Use `## ADD:` for a new rule. To replace one of the skill's own rules, use `## OVERRIDE:` and say what you're replacing and why, so it stays reviewable:

```markdown
## ADD: trigger naming
Scheduled triggers must be named ACME-<automation>-<interval>.

## OVERRIDE: retrying failed tasks
Original rule: retry the failed task directly.
Replacement: for jobs tagged production, open a change ticket before retrying —
our change policy requires it, even for a retry.
```

### Push it and check

```bash
git clone https://github.com/acme/operator-skills.git && cd operator-skills   # first time only
# create skills/operator/custom/org/naming.md
git add skills/operator/custom/org/naming.md
git commit -m "org: trigger naming"
git push
```

The GitHub web editor works just as well. That's the rule delivered: the next install or update from your copy includes it.

---

## 3. Everyone installs from your copy

Team members follow [`vendor-install.md`](vendor-install.md) for their tool, using `acme/operator-skills` in place of `itential/operator-skills`:

| Tool | Install from your copy |
|---|---|
| Claude Code | `/plugin marketplace add acme/operator-skills`, then `/plugin install itential-operator-skills@itential-operator-skills` |
| Codex CLI | `codex plugin marketplace add acme/operator-skills`, then `codex plugin add itential-operator-skills@itential-operator-skills` |
| Copilot CLI | `copilot plugin marketplace add acme/operator-skills`, then `copilot plugin install itential-operator-skills@itential-operator-skills` |
| Copilot in VS Code | **Chat: Install Plugin From Source** → `acme/operator-skills` |
| Cursor | `gh skill install acme/operator-skills --agent cursor --all` |
| A clone | `git clone https://github.com/acme/operator-skills.git`, then load it — "Working from a clone" in [`vendor-install.md`](vendor-install.md) |

The plugin keeps Itential's name (`itential-operator-skills`), so commands and skill shortcuts are the same as in Itential's docs. Already installed Itential's version? See [Switching to your own copy](vendor-install.md#switching-to-your-own-copy).

After a new rule is added, people pick it up with their tool's normal update (or `git pull` if they work from a clone). If a rule doesn't show up after updating, remove the plugin and install it again — some tools keep their installed copy until the version number changes.

---

## 4. Check the agent is using it

Run the skill and ask it directly — for example, start `/operator` and ask *"Which customization files are you applying?"* It should list your file. If it doesn't, see [Troubleshooting](#troubleshooting).

---

## 5. Take Itential's updates

When Itential publishes a release (**Watch → Custom → Releases** on `itential/operator-skills` to get notified), read its notes — anything marked as a breaking change says what to do — then pull it into your copy:

```bash
git clone https://github.com/acme/operator-skills.git && cd operator-skills       # if you don't have it locally
git remote add upstream https://github.com/itential/operator-skills.git        # once
git fetch upstream --tags
git merge v0.2.0          # a release tag — or `git merge upstream/main` for the latest unreleased changes
git push
```

Taking release tags (rather than `main`) keeps your copy on versions Itential has published. If your `main` is protected, merge on a branch and open a PR instead of pushing directly.

**What can conflict, and what to do:**

| You changed… | On update | Do this |
|---|---|---|
| Files under `skills/*/custom/` only | Never conflicts — Itential never ships files there | Nothing |
| A skill's `SKILL.md` directly | Conflicts whenever Itential changes that skill | Take Itential's version (`git checkout --theirs skills/<name>/SKILL.md`), then move your change into `custom/` as an `## OVERRIDE:` — once, and future updates are conflict-free |
| Plugin manifests (`plugin.json`, `.claude-plugin/`, `.cursor-plugin/`) — name or version | Conflicts on every release, because Itential bumps the version | Don't change them. If you renamed the plugin, keep your name and take Itential's version number each time |
| Added your own skill (`skills/acme-<name>/`) | No conflict | Give it a name Itential won't use (an org prefix) and its own `custom/` folders |
| Added your own CI workflows | No conflict if the file names differ from Itential's | Itential's workflows only run in `itential/operator-skills`, so they won't interfere |
| Deleted a skill you don't use | It comes back, or conflicts if Itential changed it | Leave Itential's skills in place; ignore the ones you don't use |

After merging, ask an agent *"Which customization files are you applying?"* once more (step 4), then tell the team to update their installs — each tool's update command is in [`vendor-install.md`](vendor-install.md).

---

## Personal settings (`dev/`)

`dev/` files are personal, so they're **gitignored** — they never get committed to your org's copy by accident. Where to keep them depends on how you work:

- **Working from a clone:** put them in `skills/<name>/custom/dev/` and load the skills from your clone (see the "Working from a clone" notes in [`vendor-install.md`](vendor-install.md)) — your tool picks them up from there.
- **Installed as a plugin:** there's no local copy to put them in. Use your tool's own personal instructions instead — e.g. `~/.claude/CLAUDE.md` (Claude Code) or `~/.codex/AGENTS.md` (Codex).

Keep `dev/` for facts about your environment and short-lived experiments. If you find yourself permanently overriding a team rule there, raise it with the team instead — that's what `team/` is for.

---

## Reference

### Folder layout

```
skills/<skill-name>/
├── SKILL.md          ← Itential's — never edit
└── custom/
    ├── org/          ← company-wide, committed
    ├── team/         ← your team, committed
    └── dev/          ← personal, gitignored
```

Always edit under `skills/<name>/custom/`.

### Which rule wins

More specific wins: `dev` over `team` over `org` over the skill's own defaults. Rules that don't conflict all apply together. Two files in the same layer shouldn't contradict each other — if they do, fix the files.

### Troubleshooting

| Symptom | Likely cause | Fix |
|---|---|---|
| Agent doesn't mention your file | File not under `skills/<name>/custom/`, or your install is older than the rule | Check the path, then update your install (or `git pull`). Still missing: remove the plugin and install it again |
| Install from your copy fails with "not found" or an authentication error | The person's git credentials can't read your private repo | Give them read access; check `git clone https://github.com/acme/operator-skills.git` works for them |
| `git merge` conflicts in a `SKILL.md` or manifest | You edited Itential's files directly | See "What can conflict" in [step 5](#5-take-itentials-updates) |
| A teammate sees your `dev/` rule | It was committed with `git add -f` | `git rm --cached` it and push |
