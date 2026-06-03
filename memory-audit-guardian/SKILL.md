---
name: memory-audit-guardian
description: Weekly memory governance audit for OpenClaw. Use when user asks to audit or optimize memory quality, reduce token overhead, verify MEMORY and TOOLS role boundaries, or run a periodic memory health check.
---

# Memory Audit Guardian

Run a structured memory-system audit and output an actionable report.

## Audit Scope
1. File-role boundaries
   - `MEMORY.md`: durable facts only
   - `memory/YYYY-MM-DD.md`: event logs only
   - `TOOLS.md`: execution rules only
   - `AGENTS.md`: policy and workflow governance
2. Size and token budget
   - Core files should stay compact
   - Flag noisy lists, stale one-offs, duplicate rules
3. Retrieval discipline
   - Prefer short, durable anchors
   - Avoid dumping long chat history into memory files
4. QMD hygiene
   - If `qmd` exists, remind the user to refresh index and embeddings

## Procedure
1. Read `MEMORY.md`, `TOOLS.md`, `AGENTS.md`
2. Inspect for duplication, role mixing, and excessive verbosity
3. Identify what to add, what to trim, and what to move elsewhere
4. Produce:
   - short executive summary
   - main risks
   - this-week fixes
   - add-3 / remove-3 suggestions

## Output Format
- Executive summary
- Findings
- Recommended edits
- Whether a new Session is recommended now

## Guardrails
- Do not overwrite user files without explicit request.
- Prefer concise, durable memory over verbose archival dumps.
