# Production FastAPI Template

A Claude Code plugin (and a portable agent skill for Cursor and other agentic IDEs). It sets the structure and execution rules for our shared backend template, which combines domain-oriented FastAPI, RAG and Agentic AI in one codebase on AWS ECS Fargate.

## Layout

```text
production-fast-api-template/            # plugin root, also a one-plugin marketplace
├── .claude-plugin/
│   ├── plugin.json                      # plugin manifest
│   └── marketplace.json                 # lets Claude Code add this folder/repo as a marketplace
├── skills/
│   └── production-fast-api-template/    # the skill: SKILL.md + companion standards
└── README.md
```

The companion files must stay next to `SKILL.md`, which links to them relatively.

## Contents

| File | Purpose |
|---|---|
| [`SKILL.md`](skills/production-fast-api-template/SKILL.md) | The entry point. It covers the core structure rules, the canonical tree, a "where does new code go" table, RAG naming conventions, summaries of every companion file, and scaffolding steps. |
| [`ASYNC_EXECUTION.md`](skills/production-fast-api-template/ASYNC_EXECUTION.md) | The full standards for async and sync routes, thread-pool offloading, synchronous RAG, CPU-bound work, ECS Fargate scaling, SQS background processing, Lambda offload (under 15 min), Celery vs native SQS, cancellation and timeouts, monitoring and failure scenarios. |
| [`PYDANTIC_STANDARDS.md`](skills/production-fast-api-template/PYDANTIC_STANDARDS.md) | Pydantic v2 conventions: the shared `CustomModel`, the UTC datetime policy, domain schemas and `pydantic-settings`, structured LLM outputs (three validation levels, bounded retries), RAG citation validation, agent plan and tool-argument validation, async compatibility and testing. |
| [`API_CONVENTIONS.md`](skills/production-fast-api-template/API_CONVENTIONS.md) | FastAPI dependencies for validation and authorization (chaining, per-request caching, RAG access scopes, agent tool permissions), REST path naming, response serialization, `BackgroundTasks` vs the queue, 422 error messages, OpenAPI docs, database naming and SQL-first queries, migrations, async API tests with dependency overrides, and ruff. |
| [`GUARDRAILS.md`](skills/production-fast-api-template/GUARDRAILS.md) | Content-safety guardrails: pipeline checkpoints (ingestion, input, context, tool, output), the shared `src/guardrails/` layer and verdict contract, fail-closed handling, Bedrock Guardrails in AgentCore Policy (Cedar, input/output phases, schema data paths, response interpretation, log-only rollout), Guardrails AI validators, async offload, observability and privacy, and effectiveness evaluation. |
| [`AI_EVALUATION.md`](skills/production-fast-api-template/AI_EVALUATION.md) | AI evaluation with DeepEval and Ragas: runtime contracts vs the offline `evaluation/` suite, shared Pydantic contracts, metric catalogs checked against DeepEval 4.1.8/4.2.6 and Ragas 0.4.3 (collections API), RAG and per-stage retrieval evaluation, structured-output and citation checks, agent, multi-agent and conversational evaluation, custom metrics and company rubrics, datasets and splits, tracing, execution, CI tiers, thresholds and reports, judge calibration, security, and API compatibility notes. |
| [`API_SECURITY.md`](skills/production-fast-api-template/API_SECURITY.md) | API security: authentication, JWT/JWKS, OAuth 2.0/OIDC, RBAC/ABAC and tenant isolation, input/upload/SSRF security, output and headers, request processing, distributed rate limiting, RAG and agent security, ECS Fargate/IAM/secrets, monitoring and audit events, CI/CD security, security tests, threat modeling and a control checklist mapped to OWASP API and LLM Top 10. |
| [`AGENT_SECURITY.md`](skills/production-fast-api-template/AGENT_SECURITY.md) | LLM and agent security: attack catalog, OWASP LLM Top 10 (2025) and Agentic Top 10 (2026) audit mappings, tool registry, authorization cards, argument and result validation, budgets, approvals, runtime isolation, audit, control baseline, testing plan, release gate and threat checklist. |

Conventions are marked 🟢. Open team decisions are marked 🟡, and agents are told to ask before choosing one.

## Install

**Claude Code (plugin, recommended).** From a local clone or folder:

```text
/plugin marketplace add <path-to>/production-fast-api-template
/plugin install production-fast-api-template@production-fast-api-template
```

Once the folder is pushed to a Git host, `<path-to>/…` can be replaced with `<owner>/<repo>`. The
same commands exist on the CLI: `claude plugin marketplace add <source>` and
`claude plugin install production-fast-api-template@production-fast-api-template`. Use
`--scope project` on `marketplace add` to declare it for a single repository. To try it without
installing: `claude --plugin-dir <path-to>/production-fast-api-template`.

**Claude Code (plain skill, no plugin).** Copy or symlink `skills/production-fast-api-template/`
to `~/.claude/skills/production-fast-api-template/` (all projects) or
`<repo>/.claude/skills/production-fast-api-template/` (one project).

**Other IDEs.**

- **IDEs that support `SKILL.md`:** copy `skills/production-fast-api-template/` into their skills directory.
- **Rules-file-only tools** (`AGENTS.md`, `.cursor/rules/`, and so on): paste in the body of `SKILL.md`, and put the seven companion files from `skills/production-fast-api-template/` alongside it.

**Validate after editing:** `claude plugin validate --strict .` (plugin root) and
`claude plugin validate --strict .claude-plugin/marketplace.json`. Bump `version` in
`plugin.json` when you publish changes.

## How it is applied

- **Skill (on demand):** the agent loads `SKILL.md` when a task matches the skill's `description`. That covers scaffolding, adding modules, and writing routes, dependencies, schemas, settings, queries, tests, LLM calls, guardrails, evaluations, security controls, tools or workers. In Claude Code you can also invoke it explicitly: as a plugin skill it is namespaced (`/production-fast-api-template:production-fast-api-template`); installed as a plain skill it is `/production-fast-api-template`.
- **Always-on enforcement:** the skill's scaffolding step writes a `CLAUDE.md` or `AGENTS.md` into each generated project, pointing at `docs/standards/*.md`. Those files are loaded in every session. For an existing project, add those lines yourself.

## Status

Phases covered:

1. Project structure
2. Async execution and background processing
3. Pydantic standards and structured LLM outputs
4. AI evaluation (DeepEval, Ragas, deterministic metrics)
5. API security, plus LLM and agent security

Also included: FastAPI dependencies, API design, database, migrations, testing and linting (`API_CONVENTIONS.md`), and guardrails and content safety (`GUARDRAILS.md`).

CI pipelines, infrastructure and deployment come in later phases.
