# donethatai/.github

Shared GitHub configuration for the donethatai organization.

## AI review gate

`.github/workflows/ai-review.yml` sets the required `AI review` status on every pull request.
An organization ruleset runs it from here, so the repos hold no copy. The comment at the top
of the file says how it decides.

In short: Codex on the ChatGPT plan reviews first. When the plan reaches its usage limit, or
Codex doesn't start within 5 minutes or finish within 20, Codex CLI reviews with the org API
key instead and posts its findings as review comments. Findings from either block the merge
until their conversations are resolved.

### Setup

1. This repo is public, so every org repo, public or private, can run the workflow. If it is
   ever made private again, set Settings > Actions > General > Access to "Accessible from
   repositories in the donethatai organization", and know that public repos then lose it.
2. Organization secret `OPENAI_API_KEY`, available to every repo the ruleset covers. Without
   it, the gate only waits for Codex on the plan, as before.
3. Organization ruleset "AI review" (id 25007847): target the default branch of the covered
   repos, rule "Require workflows to pass before merging" with this repo,
   `.github/workflows/ai-review.yml`, pinned to a commit of `main`.
4. In each covered repo: keep `AI review` in the required status checks, and delete the
   repo's own `.github/workflows/ai-review.yml`.

### Changing the gate

Change the file here through a pull request, then move the ruleset's pinned commit. Every
covered repo picks up the change at once. Open pull requests then stay blocked until the new
version runs on them: a re-run of an old run doesn't count, so push to them or close and
reopen them. Keep the Probo control "AI code review on every pull request" in step: it names
the pinned commit.

### Rollout

All repos in SOC 2 scope are in the ruleset: donethat-trust, donethat-firebase,
donethat-webapp, donethat-website, donethat-wxt, donethat-zapier, donethat-n8n and
donethat-electron. None keeps its own copy; donethat-trust's `scripts/check-shared-workflows.sh`
lists any that does.

## Secret scan

`.github/workflows/secrets.yml` sets the required `secrets` status on every pull request in the
private repos: gitleaks scans the pull request's commits without checking them out. The comment
at the top of the file says how. Public repos use GitHub's own secret scanning instead.

### Setup

1. Organization ruleset "Secret scan": target the default branch of donethat-trust,
   donethat-firebase, donethat-webapp, donethat-website, donethat-wxt and donethat-zapier,
   rule "Require workflows to pass before merging" with this repo,
   `.github/workflows/secrets.yml`, pinned to a commit of `main`.
2. In each covered repo: keep `secrets` in the required status checks, and delete the repo's
   own `.github/workflows/secrets.yml`. Exceptions stay in that repo's `.gitleaksignore`.

### Changing the scan

Change the file here through a pull request, then move the ruleset's pinned commit, as for the
AI review gate.

## Pull request template

`.github/pull_request_template.md` is the default template for every repo in the organization
that has none of its own, private repos included. Change it here; don't add copies to repos.
