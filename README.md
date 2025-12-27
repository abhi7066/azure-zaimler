# Zaimler MCP — Risk Scoring Orchestration

This repository contains a set of agent prompts, templates, and knowledge
documents for running an end-to-end member-level risk scoring workflow
against the Zaimler MCP server.

**Quick summary:** the system uses a Control (orchestrator) agent that
invokes three Worker agents (Coverage, Medical, Pharmacy) to compute
normalized risk scores and a composite risk score with explainable outputs.

**Primary contact:** see the `Zaimler-initial-KB` integration guide for
environment and Claude Code instructions.

**Core concepts:**

- Control Agent: orchestrates workers, validates outputs, computes composite
  score.
- Worker Agents: compute domain-specific risk (coverage, medical, medication).
- Knowledge Base: scoring formulas, thresholds, and program recommendations.

**Where to look**

- Controller orchestration and full run prompts: [zaimler-mcp-server/run_full_flow.md](zaimler-mcp-server/run_full_flow.md)
- Controller prompt & orchestration rules: [zaimler-mcp-server/agent_prompts/controller/control_agent_orchestration.md](zaimler-mcp-server/agent_prompts/controller/control_agent_orchestration.md)
- Worker agent prompts:
  - [zaimler-mcp-server/agent_prompts/workers/worker_coverage_risk.md](zaimler-mcp-server/agent_prompts/workers/worker_coverage_risk.md)
  - [zaimler-mcp-server/agent_prompts/workers/worker_medical_risk.md](zaimler-mcp-server/agent_prompts/workers/worker_medical_risk.md)
  - [zaimler-mcp-server/agent_prompts/workers/worker_medication_adherence.md](zaimler-mcp-server/agent_prompts/workers/worker_medication_adherence.md)
- Templates & execution rules:
  - [zaimler-mcp-server/agent_prompts/templates/common_rules.md](zaimler-mcp-server/agent_prompts/templates/common_rules.md)
  - [zaimler-mcp-server/agent_prompts/templates/output_schema.md](zaimler-mcp-server/agent_prompts/templates/output_schema.md)
  - [zaimler-mcp-server/agent_prompts/templates/member_loop.md](zaimler-mcp-server/agent_prompts/templates/member_loop.md)
- Knowledge Base (scoring docs):
  - [Knowledge Base/markdown/composite_risk_scoring.md](Knowledge%20Base/markdown/composite_risk_scoring.md)
  - [Knowledge Base/markdown/coverage_risk_scoring.md](Knowledge%20Base/markdown/coverage_risk_scoring.md)
  - [Knowledge Base/markdown/medical_risk_scoring.md](Knowledge%20Base/markdown/medical_risk_scoring.md)
  - [Knowledge Base/markdown/pharmacy_adherence_scoring.md](Knowledge%20Base/markdown/pharmacy_adherence_scoring.md)
- Integration / environment guide: [Zaimler-initial-KB/README (1).md](<Zaimler-initial-KB/README%20(1).md>)
- Example controller test prompt: [Files/try_controller_prompt.md](Files/try_controller_prompt.md)

**Repository layout**

- `zaimler-mcp-server/` — agent prompts, templates, and run scripts for MCP
  orchestration.
- `Knowledge Base/` — canonical scoring and orchestration docs (markdown, text,
  and JSON knowledge artifacts).
- `Zaimler-initial-KB/` — integration guide for Claude Code and Docker-based
  MCP runs.

**How it works (high level)**

1. A user provides a query that includes one or more `member_id`s.
2. The Control Agent identifies intent and invokes the required Worker
   Agents (coverage, medical, medication adherence).
3. Each Worker queries MCP workspaces (via `mcp__zaimler__set_workspace` and
   `mcp__zaimler__agent_chat`), returns a normalized risk score (0–1) and
   factual risk factors in strict JSON per the output schema.
4. The Control Agent validates worker outputs, combines them using the
   composite formula, maps to risk tiers, and emits a final JSON response.

Composite scoring formula (from knowledge docs):

CompositeRiskScore = 0.50 _ Medical + 0.30 _ Pharmacy + 0.20 \* Coverage

Risk tiers (summary):

- > = 0.80 → `very_high` (Complex Chronic Care)
- 0.60–0.79 → `high` (High Risk Care Management)
- 0.40–0.59 → `moderate` (Tele-Coaching)
- < 0.40 → `low` (Self-Management)

**Quick start (development / testing)**

1. Follow the integration guide: [Zaimler-initial-KB/README (1).md](<Zaimler-initial-KB/README%20(1).md>)
   — it shows how to configure `claude` / Docker, set `.env`, and add the MCP.
2. Use the run prompt: [zaimler-mcp-server/run_full_flow.md](zaimler-mcp-server/run_full_flow.md)
   or the example controller prompt at [Files/try_controller_prompt.md](Files/try_controller_prompt.md).
3. Ensure the MCP server has the expected workspaces: `HC Member Coverage`,
   `HC Medical Claims`, and `HC Rx Claim` (these are referenced by the worker
   prompts).

**Extending or modifying scoring**

- Update knowledge docs in `Knowledge Base/markdown/` to change weights,
  thresholds, or recommended programs.
- Worker prompts under `zaimler-mcp-server/agent_prompts/workers/` implement
  the data fetching and normalization logic; update them when workspace
  schemas change.

**Notes & guardrails**

- Agents must not fabricate data; all data must come from MCP tool calls.
- All agents must return JSON conforming to the templates in
  `zaimler-mcp-server/agent_prompts/templates/output_schema.md`.
- Retry failed MCP calls once; on a second failure, return an error and stop.

If you want, I can:

- run a deeper scan and produce a one-page printable system diagram,
- generate unit tests for the scoring formulas, or
- commit this README and open a PR with documentation improvements.
