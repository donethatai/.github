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

1. This repo: Settings > Actions > General > Access: "Accessible from repositories in the
   donethatai organization". The ruleset can't run the workflow without it.
2. Organization secret `OPENAI_API_KEY`, available to every repo the ruleset covers. Without
   it, the gate only waits for Codex on the plan, as before.
3. Organization ruleset "AI review": target the default branch of the covered repos, rule
   "Require workflows to pass before merging" with this repo,
   `.github/workflows/ai-review.yml`, pinned to a commit of `main`.
4. In each covered repo: keep `AI review` in the required status checks, and delete the
   repo's own `.github/workflows/ai-review.yml`.

Public repos can't run workflows from this private repo; they keep their own copy until
this repo or the gate moves somewhere public.

### Changing the gate

Change the file here through a pull request, then move the ruleset's pinned commit. Every
covered repo picks up the change at once.

### Rollout

| Repo | Shared gate |
| --- | --- |
| donethat-trust | pilot |
| donethat-firebase, donethat-webapp, donethat-website, donethat-wxt, donethat-zapier | not yet |
| donethat-n8n, donethat-electron (public) | not yet, see above |
