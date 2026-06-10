---
description: Security-scan, then push to GitHub, set up GitHub Pages, and sync README + repo "About"
argument-hint: [optional commit message]
---

Run the full "update GitHub" workflow for this repo (Meridian Wealth Advisors landing page, single `index.html`, deployed as a static site). Work through the steps below in order, in this conversation, and report progress/results as you go. Stop and ask the user before any destructive or irreversible action (force-push, `git reset --hard`, overwriting content the user wrote by hand, etc.).

## Step 1 — Security scan (gate for everything below)

- Run `git status`, `git diff`, and `git diff --cached` to see exactly what would be pushed, plus list any new/untracked files.
- Scan all changed/new files (and, on first run, the whole tree) for secrets and credentials: API keys, access/bearer tokens, private keys (`BEGIN ... PRIVATE KEY`), passwords, AWS keys (`AKIA...`), `.env` files, connection strings, and other high-entropy strings.
- Confirm `.gitignore` excludes common secret files (`.env*`, `*.pem`, `*.key`, credential JSON, etc.) — create/update it if entries are missing.
- Note: the FormSubmit recipient email address in `index.html` is intentional site config, not a leak — don't flag it.
- If anything looks like a real secret, STOP — do not commit or push. Report the exact file/line to the user and ask them to remove/rotate it first.

## Step 2 — README

- Check `README.md`. If it's missing, empty, or just a placeholder (e.g. `# <repo-name>`), write/update it covering: project overview (Meridian Wealth Advisors investment-strategy landing page), structure of `index.html`, how to run locally, and how deployment works (GitHub Pages via Actions).

## Step 3 — GitHub Pages via GitHub Actions

- Ensure `.github/workflows/deploy.yml` exists and deploys the repo root as a static site to GitHub Pages on push to `main`, using `actions/configure-pages`, `actions/upload-pages-artifact`, and `actions/deploy-pages`. Create or fix it if missing/broken.

## Step 4 — Commit and push

- Stage only the relevant files (never a blind `git add -A`).
- Commit with a clear message — use the user-supplied message (`$ARGUMENTS`) if given, otherwise write one based on the diff.
- Push to `origin main`.

## Step 5 — Update repo "About"

- If the `gh` CLI is installed and authenticated, run `gh repo edit --description "..." --homepage "https://<owner>.github.io/<repo>/"` with a description matching the site's purpose, and ensure the Pages source is set to GitHub Actions via `gh api -X PUT repos/{owner}/{repo}/pages -f build_type=workflow` (use `-X POST` instead if Pages isn't enabled yet).
- If `gh` is unavailable or not authenticated, skip the API calls and instead give the user the exact description/homepage text to paste into the repo's GitHub "About" section, plus the Settings → Pages → Source: GitHub Actions step.

## Step 6 — Final report

- Summarize what was committed/pushed, the Pages URL, and any manual follow-ups the user still needs to do (especially Step 5 if `gh` wasn't available).
