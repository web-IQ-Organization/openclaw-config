# Model Routing (OpenRouter Only) - v5 (2026-03-25 - Reliability First)

## Core Principles
1.  **Every model call routes through OpenRouter.** All model strings must have the `openrouter/` prefix.
2.  **No direct provider keys.** `OPENAI_API_KEY` in `env` is for skills (e.g., Whisper) only.
3.  **Reliability over cost.** A model that fails costs more than a slightly pricier one that works.
4.  **Proven models only.** Do not assign a model to an agent until it has been verified working in sub-agent context.
5.  **Premium use by exception.** Opus is banned from default usage.

## ⚠️ Confirmed Broken Models (DO NOT USE IN SUB-AGENTS)
These models have failed with near-zero output tokens in our sub-agent context. Remove from all agent configs immediately.

| Model | Failure Pattern | Date Confirmed |
|---|---|---|
| `openrouter/minimax/minimax-m1` | Connects, near-zero output | 2026-03-25 |
| `openrouter/anthropic/claude-haiku-4.6` | Near-zero output | 2026-03-25 |
| `openrouter/deepseek/deepseek-r1-distill-qwen-32b` | Near-zero output | 2026-03-25 |

**Root cause:** These models likely fail due to context window limits + long system prompts, or poor OpenRouter routing reliability for free/cheap tiers. Do not retry without explicit testing in a sandboxed sub-agent first.

## ✅ Confirmed Working Models
| Model | Verified Context | Notes |
|---|---|---|
| `openrouter/google/gemini-2.5-flash` | Sub-agent + main session | Reliable, cheap, handles large prompts |
| `openrouter/anthropic/claude-sonnet-4.6` | Main session | Premium, reserved for high-stakes |
| `openrouter/deepseek/deepseek-v3-2` | Needs sub-agent verification | Code-UI alias; use with gemini fallback |
| `openrouter/perplexity/sonar` | Research tasks | Live web data only |
| `openrouter/google/gemini-2.5-flash-lite` | Simple tasks | Ultra-cheap, low complexity only |

## Task-based Routing (OpenRouter) — vX

Core idea: route each agent by the type of work they perform, not by a fixed one-size-fits-all model. Opus is banned from default usage; only use Opus with explicit, time-boxed sandbox approval. All model calls must use the openrouter/ prefix.

Task categories and mappings

- Core Execution / Operations / Stakeholder Management / Finance
  - Primary: openrouter/google/gemini-2.5-flash
  - Fallback 1: openrouter/google/gemini-2.5-flash-lite
  - Fallback 2: openrouter/anthropic/claude-sonnet-4.6

- Product Development / Engineering / Infrastructure / QA
  - Primary: openrouter/deepseek/deepseek-v3-2
  - Fallback 1: openrouter/google/gemini-2.5-flash
  - Fallback 2: openrouter/google/gemini-2.5-flash-lite

- AI Systems / Model Governance
  - Primary: openrouter/google/gemini-2.5-flash
  - Fallback 1: openrouter/google/gemini-2.5-flash-lite
  - Fallback 2: openrouter/anthropic/claude-sonnet-4.6

- Design / UX / Experience
  - Primary: openrouter/deepseek/deepseek-v3-2
  - Fallback 1: openrouter/google/gemini-2.5-flash
  - Fallback 2: openrouter/anthropic/claude-sonnet-4.6
  - Note: DeepSeek V3-2 requires sandbox verification before becoming primary.

- Growth / Content / Outreach
  - Primary: openrouter/google/gemini-2.5-flash
  - Fallback 1: openrouter/google/gemini-2.5-flash-lite
  - Fallback 2: openrouter/anthropic/claude-sonnet-4.6

- Sales / Revenue Operations
  - Primary: openrouter/google/gemini-2.5-flash
  - Fallback 1: openrouter/google/gemini-2.5-flash-lite
  - Fallback 2: openrouter/anthropic/claude-sonnet-4.6

- Data / Analytics / Insights
  - Primary: openrouter/perplexity/sonar
  - Fallback 1: openrouter/google/gemini-2.5-flash
  - Fallback 2: openrouter/anthropic/claude-sonnet-4.6

- Infrastructure, DevOps, and Automation
  - Primary: openrouter/google/gemini-2.5-flash
  - Fallback 1: openrouter/google/gemini-2.5-flash-lite
  - Fallback 2: openrouter/anthropic/claude-sonnet-4.6

- Security, Compliance, and Risk
  - Primary: openrouter/google/gemini-2.5-flash
  - Fallback 1: openrouter/google/gemini-2.5-flash-lite
  - Fallback 2: openrouter/anthropic/claude-sonnet-4.6

- Knowledge, Memory, and Governance
  - Primary: openrouter/google/gemini-2.5-flash
  - Fallback 1: openrouter/google/gemini-2.5-flash-lite
  - Fallback 2: openrouter/anthropic/claude-sonnet-4.6

- Incident Response / Continuous Improvement
  - Primary: openrouter/google/gemini-2.5-flash
  - Fallback 1: openrouter/google/gemini-2.5-flash-lite
  - Fallback 2: openrouter/anthropic/claude-sonnet-4.6

Notes
- Opus remains banned from default usage; only with explicit, logged approval and sandbox testing.
- DeepSeek V3-2 sandbox verification is required before promoting to Primary in Design.
- All model calls must route through OpenRouter with the openrouter/ prefix.
- OPENAI_API_KEY in env is for Whisper only — never for agent model routing.

## Scenario-Based Routing (General / Main Session)
- **Strategic planning / architecture:** `openrouter/google/gemini-2.5-flash`
- **Complex reasoning / deep tech:** `openrouter/deepseek/deepseek-v3-2` (with gemini fallback)
- **UI/UX design output / HTML builds:** `openrouter/deepseek/deepseek-v3-2`
- **Routine internal processing / logging:** `openrouter/google/gemini-2.5-flash`
- **Bulk content / social / templated text:** `openrouter/google/gemini-2.5-flash`
- **Live web research / current data:** `openrouter/perplexity/sonar`
- **Formatting / cleanup / tagging / parsing:** `openrouter/google/gemini-2.5-flash-lite`
- **Premium high-stakes (with approval):** `openrouter/anthropic/claude-sonnet-4.6`
- **Image generation:** Query OpenRouter `/models?output_modalities=image` for current options
- **Audio transcription:** Direct OpenAI Whisper API (`OPENAI_API_KEY` in env)

## Cost Tiers (March 2026 — Approximate)
- **Ultra-cheap ($0-0.27/M):** Gemini 2.5 Flash Lite
- **Cheap ($0.27-0.50/M):** Gemini 2.5 Flash, Perplexity Sonar
- **Mid ($0.30-1.25/M):** DeepSeek V3.2, Claude Sonnet 4.6
- **Premium research:** Perplexity Sonar Pro
- **CRITICALLY RESTRICTED:** Claude Opus 4.6 — banned from default use

## Fallback Logic
1. If primary fails, immediately retry with Fallback 1. No manual approval needed.
2. If Fallback 1 fails, drop to Fallback 2.
3. If all 3 fail, use `openrouter/google/gemini-2.5-flash` as universal emergency fallback and log the incident.
4. **Never halt execution** — keep moving with the best available working model.
5. Log all model failures in MEMORY.md failure section with date + pattern.

## Model Testing Protocol
Before adding any new model to the routing table:
1. Spawn a minimal sub-agent with a 50-word task using only that model
2. Verify output tokens > 0 and output is coherent
3. Only then add to routing table with "verified" status
4. If it fails, add to the Confirmed Broken list immediately

## Announcement Template
> "Using [model] — [reason]. Cost tier: [ultra-cheap / cheap / mid / premium / RESTRICTED]."

## Opus Usage Policy (CRITICAL)
- **Claude Opus is BANNED from default usage.**
- Only with explicit Jared/Chuck approval, logged, time-boxed.
- After initial concept, revert to cheaper models. No exceptions.

## Notes
- Confirm model strings via OpenRouter `/models` before new assignments.
- `OPENAI_API_KEY` in env is for Whisper only — never for agent model routing.
- All agent model routing MUST use OpenRouter prefix.
