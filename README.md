# env-branch-lab — authorized CI-plane research lab

This repository is a throw-away laboratory used for authorized security research on GitHub Actions
semantics. It contains no target code and no secrets.

Purpose: determine whether a GitHub *environment deployment branch policy* is evaluated against
`GITHUB_REF` or against a pull request's head branch name.

- Environment `prod` allows exactly the branch `deploy-branch` (custom branch policies only).
- `.github/workflows/probe.yml` runs on `pull_request` and `workflow_dispatch`, declares
  `environment: prod` and `id-token: write`, and requests an OIDC token (audience
  `npm:registry.npmjs.org`).
- Result: a PR **from `deploy-branch` itself** is rejected with
  `Branch "refs/pull/1/merge" is not allowed to deploy to prod due to environment protection rules`,
  while `workflow_dispatch` on `deploy-branch` succeeds and mints a token
  (`GITHUB_REF=refs/heads/deploy-branch`, `oidc_http=200`).
=> Policies match `GITHUB_REF`, so PR merge refs never satisfy a branch-name policy.
