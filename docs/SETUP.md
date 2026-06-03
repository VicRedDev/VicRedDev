# Setup Guide: Fork This Profile

This guide shows how this GitHub profile repo works and how to fork it for your own use.

## Summary

This profile uses:

- **readme-scribe** to render `README.md` from a template
- **WakaTime** for coding-metrics integration
- **GitHub Actions** to regenerate the README daily at **02:00 UTC** (after WakaTime updates)
- **Public badge services** (github-readme-stats, streak-stats, profile-trophy) for the stat cards

## Requirements

You will need:

- A GitHub account
- A WakaTime account (free at wakatime.com) — only if you want the WakaTime metrics section

## Setup in Steps

### 1. Fork this repository

Click the **Fork** button on GitHub to create `<your-username>/<your-username>`.

After the fork finishes, clone **your fork** locally:

```bash
git clone https://github.com/<your-username>/<your-username>.git
cd <your-username>
```

This fork becomes your personalized profile repo—customize it to match your brand.

### 2. Setup GitHub Secrets

Two secrets are used by the workflow. Add them in:

Settings → Secrets and variables → Actions → New repository secret

**GH_TOKEN**

A Personal Access Token with:

- repo (full control of private repositories)
- read:user (read users' profile data)

Generate at: https://github.com/settings/tokens/new

Used by readme-scribe and waka-readme-stats.

**WAKATIME_API_KEY**

Your WakaTime API key, from https://wakatime.com/settings/account (scroll to the "API Key" section — you only need the key, not your username).

Used by the waka-readme-stats action.

> Without these secrets the static README still renders fine; only the auto-updated
> metrics (WakaTime, live stats, recent activity) won't populate.

### 3. Update the template

Edit `templates/README.md.tpl` — this is the source. `README.md` is generated from it, so don't edit `README.md` directly.

Things to personalize:

- **Header (line 1):** the `text=` and `desc=` of the capsule banner, and the trailing `github.com/<your-username>` link
- **Badges (lines 6-8):** `username=<your-username>` in the profile-views, followers, and stars badges
- **Typing animation (line 12):** the `lines=` list with your own roles/skills
- **About me:** your bio
- **Tech stack:** the skillicons `i=` list and the AI / framework badges
- **Stat cards:** `username=` / `user=` in the github-readme-stats, streak-stats, and trophy URLs
- **What I'm working on / I can help with / Featured projects:** your own content and repos
- **Connect:** your website, LinkedIn, email, and GitHub

The stat cards use the public services directly — no Vercel deployment required:

```html
<img src="https://github-readme-stats.vercel.app/api?username=<your-username>&..." />
<img src="https://github-readme-streak-stats.herokuapp.com/?user=<your-username>&..." />
```

### 4. Test locally (optional but recommended)

Install readme-scribe:

```bash
go install github.com/muesli/readme-scribe@latest
```

Render the template:

```bash
readme-scribe -template templates/README.md.tpl -output README.md
```

Or use the watch script, which re-renders whenever you save the template:

```bash
./bin/watch-readme
```

### 5. Enable GitHub Actions

Settings → Actions → General → Workflow permissions, then select:

- Read and write permissions
- Allow GitHub Actions to create and approve pull requests

Save.

### 6. Run the workflow

**Manual trigger (recommended for the first run):**

- Go to the Actions tab
- Select the "Update README" workflow
- Click "Run workflow"

**Or wait for the scheduled run** — daily at 02:00 UTC.

### 7. Check if it worked

- Actions tab shows a green checkmark
- `README.md` was updated with your info
- GitHub stat badges render
- Recent activity shows your PRs/contributions
- WakaTime section populates (it takes 24-48 hours to collect enough data)

## Customization

### Change update frequency

Edit the `cron` in `.github/workflows/readme.yaml`:

```yaml
cron: "0 2 * * *"    # daily at 02:00 UTC (current)
cron: "0 */6 * * *"  # every 6 hours
cron: "0 0 * * *"    # daily at midnight
```

### WakaTime display options

Edit the `Inject WakaTime metrics` step in `.github/workflows/readme.yaml`.

Options (set to "True" or "False") — see https://github.com/anmol098/waka-readme-stats:

- SHOW_PROFILE_VIEWS
- SHOW_TOTAL_CODE_TIME
- SYMBOL_VERSION
- ...and more

### Add more template sections

The template uses Go templating. Available functions:

- `{{ recentPullRequests 5 }}` — your recent PRs
- `{{ recentContributions 5 }}` — your recent contributions
- `{{ humanize .CreatedAt }}` — format dates nicely

See https://github.com/muesli/readme-scribe for the full list.

## Troubleshooting

### Workflow fails with "Resource not accessible"

Your GH_TOKEN probably lacks permissions. Regenerate at
https://github.com/settings/tokens/new with `repo` and `read:user` checked, then
update the secret.

### WakaTime section stays empty

Normal at first — it takes 24-48 hours. Also check that `WAKATIME_API_KEY` is
correct and that WakaTime is installed and tracking your editor
(wakatime.com/dashboard).

### GitHub stat badges not showing

Check the `username` is correct in the template and try the public URLs directly
in a browser to confirm the service is up.

### README not updating

Check that workflow permissions are "Read and write", both secrets are set, and
look at the Actions logs for errors.

### Workflow runs but doesn't commit

The workflow only commits when `README.md` actually changes — no empty commits.

## Repo structure

```
.
├── .github/
│   ├── workflows/
│   │   └── readme.yaml          # automation workflow (daily 02:00 UTC)
│   └── dependabot.yml           # keeps actions updated
├── bin/
│   ├── watch-readme             # local dev helper (re-render on save)
│   ├── workflowdetails          # gh cli wrapper
│   └── workflowrun              # gh cli wrapper
├── docs/
│   └── SETUP.md                 # this guide
├── templates/
│   └── README.md.tpl            # edit this, not README.md
├── assets/
│   └── bar_graph.png            # generated by waka-readme-stats
└── README.md                    # auto-generated, don't edit by hand
```

## Resources

- readme-scribe: https://github.com/muesli/readme-scribe
- waka-readme-stats: https://github.com/anmol098/waka-readme-stats
- GitHub Actions docs: https://docs.github.com/actions
- Creating tokens: https://docs.github.com/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token

---

**Don't commit secrets to the repo.** Always use GitHub Secrets.
