# Changelog

## 1.0.0 - 2026-11-04
Première version du kit.
- 8 agents (planner, implementer, reviewer, debugger, devops, context-engineer, researcher, conductor) avec handoffs et sous-agents.
- 17 skills dont `bootstrap` (création depuis le template `dotnet new` d'entreprise + adaptation automatique), `internal-packages`
  (digests des paquets NuGet intranet depuis le cache local), spec-driven development (`spec`, `spec-plan`, `spec-implement`),
  `code-review`, `retro`, `azure-pipelines`, `git-workflow`, `merge-conflicts`.
- Hooks portables (VS Code Local, moteur Copilot, Copilot CLI) : contexte de session, guard (commandes, chemins, secrets,
  données personnelles belges, règles de couches), quality gate (architecture, build, tests impactés).
- Instructions par couche Clean Architecture / DDD, tests, pipelines Azure DevOps.
- Mémoire projet : `specs/`, `docs/ai/` (contexte, glossaire, paquets internes, apprentissages, évaluations), `docs/adr/`.
- Routage des modèles (Auto ou par niveaux) via `configure-kit.ps1`.
- `install.ps1`, `tests/Test-Kit.ps1`, guide d'utilisation en français.
