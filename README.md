# ci-workflows — global CI for all my repos

Define-once CI for a **personal** GitHub account: free **Semgrep OSS** security
scanning (reusable workflow) + free **CodeRabbit** AI PR review (account-level app).

> Replace `fredman08` with your GitHub username anywhere it appears if different.

## What's here

| File | Role |
|------|------|
| `.github/workflows/semgrep.yml` | The **reusable** Semgrep workflow (`on: workflow_call`). Lives once, in this repo. |
| `templates/security.yml` | The **3-line caller** you drop into each repo at `.github/workflows/security.yml`. |
| `templates/.coderabbit.yaml` | Optional CodeRabbit config (works with zero config too). |

## Reality check on "global" for a personal account

- **CodeRabbit is truly global** — one account-level app install covers all repos (incl. future ones). No files needed.
- **Semgrep is "define once, add a 3-line caller per repo"** — GitHub's auto-apply workflow features (required / default / starter workflows) are **org-only**; a personal account can't auto-inject a workflow into every repo. The reusable-workflow + per-repo caller below is the closest equivalent. Keep a **template repo** so new repos are born with the caller.

## One-time setup

1. **Create this repo**: push this folder to `fredman08/ci-workflows` (private is fine).
2. **Enable cross-repo sharing** (only if private): repo **Settings → Actions → General → Access → "Accessible from repositories owned by 'fredman08' user" → Save**. Without this, a private reusable workflow is callable only from itself.
3. **Install CodeRabbit** once: go to https://coderabbit.ai → "Login with GitHub" → install the **CodeRabbit GitHub App** → select **"All repositories"**. Done — every PR is reviewed automatically, no file required. Free tier covers private repos (rate limit ~4 reviews/hr — a non-issue solo).

## Per repo that needs the Semgrep gate

Copy `templates/security.yml` to that repo at `.github/workflows/security.yml`, commit, open a PR. Semgrep runs on every PR and push to `main`.

- **Hard gate (default):** build **fails** on findings (`--error`).
- **Warn-only:** in the caller, uncomment `with: { soft-fail: true }`.
- **Stricter rules:** `with: { config: "p/default p/security-audit" }`.

## Notes

- **Windows is irrelevant to CI** — Actions run on GitHub's `ubuntu-latest` runners.
- **No Semgrep account/token needed** for OSS scanning. `SEMGREP_APP_TOKEN` is only for the paid managed platform (`semgrep ci`) — add it as a repo secret later if you ever adopt it.
- **SARIF → GitHub code scanning** is free only on **public** repos (private needs paid GitHub Advanced Security), so this config gates via pass/fail + logs instead of the Security tab.
- **Pin for stability:** reference the reusable workflow by a tag (`@v1`) instead of `@main` if you don't want to auto-adopt changes. Tag this repo `v1` after the first green run.
- `config: "auto"` is the convenient default; switch to `p/default` if you prefer a fixed, registry-pinned ruleset with no auto-selection.
