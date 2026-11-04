---
name: azure-pipelines
description: Author, modify, validate and troubleshoot Azure DevOps YAML pipelines (CI/CD) - stages, templates, NuGet authentication for the intranet feed, tests and coverage publishing, environments and approvals - and diagnose failed runs from their logs. Use when touching azure-pipelines*.yml or templates, or when a pipeline or build fails.
---
# Azure Pipelines

The pipeline map (stages, templates, tasks, dotnet steps, variable groups) is in the discovery report and in the "Commands and CI parity" table of `docs/ai/project-context.md`.

## A. Diagnose a failed run
1. **Get the facts**: run id or link, failing stage / job / step, and the log of that step. Sources in order: Azure DevOps MCP tools (`ado/*` or `company/*`: builds, logs, timeline), then the CLI (`az pipelines runs list --branch <b> --top 5`, `az pipelines runs show --id <id>`), otherwise ask the user to paste the failing step log.
2. **Classify** the failure:
   - compile error / test failure: reproduce locally with the CI-parity command (Release configuration if the pipeline uses it);
   - restore 401/403 or NU1101: intranet feed authentication (`NuGetAuthenticate@1` missing or ordering), wrong feed, package not published yet;
   - agent or infrastructure (disk, timeout, pool, service connection): not a code problem, report it;
   - flaky test: same commit green before; quote both runs;
   - configuration or secret (missing variable, variable group permission): name the variable, never its value.
3. **Fix minimally** (code or YAML), verify locally, explain cause and fix in five lines. Re-running or queuing a pipeline needs the user's approval.

## B. Author or modify YAML
- Extend the shared templates the repository already uses; keep stage and job names stable (dashboards, policies and approvals depend on them).
- `UseDotNet@2` with `useGlobalJson: true`; `NuGetAuthenticate@1` before restore; restore once, then `--no-restore` builds.
- Tests: `dotnet test` with `--logger trx` and `--collect "XPlat Code Coverage"`, then `PublishTestResults@2` and `PublishCodeCoverageResults@2`.
- Secrets: variable groups linked to Key Vault; never inline, never echo. Deployments: `deployment` jobs with `environment:` so approvals and checks apply.
- Conditions explicit (`condition: and(succeeded(), eq(variables['Build.SourceBranch'], 'refs/heads/main'))`); `dependsOn` explicit when not linear.
- Do not touch triggers, PR validation, pools, service connections, environments or approvals unless explicitly asked.

## C. Validate before committing
- YAML schema: VS Code "Azure Pipelines" extension (recommended in `.vscode/extensions.json`).
- Template expansion: the Azure DevOps "Validate" / run preview (UI or REST preview through MCP / `az devops invoke`) catches template and parameter errors without running anything.
- Then a run on the feature branch, with the user's approval.

## D. Keep CI parity
Any command added to the pipeline must be runnable locally; add it to the CI parity table in `docs/ai/project-context.md`.
