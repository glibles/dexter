# Repository Guidelines

- Repo: https://github.com/virattt/dexter
- Dexter is a CLI-based AI agent for deep financial research, built with TypeScript, Ink (React for CLI), and LangChain.

## Project Structure

- Source code: `src/`
  - Agent core: `src/agent/` (agent loop, prompts, scratchpad, token counting, types)
  - CLI interface: `src/cli.tsx` (Ink/React), entry point: `src/index.tsx`
  - Components: `src/components/` (Ink UI components)
  - Hooks: `src/hooks/` (React hooks for agent runner, model selection, input history)
  - Model/LLM: `src/model/llm.ts` (multi-provider LLM abstraction)
  - Tools: `src/tools/` (financial search, web search, browser, skill tool)
  - Tool descriptions: `src/tools/descriptions/` (rich descriptions injected into system prompt)
  - Finance tools: `src/tools/finance/` (prices, fundamentals, filings, insider trades, etc.)
  - Search tools: `src/tools/search/` (Exa preferred, Tavily fallback)
  - Browser: `src/tools/browser/` (Playwright-based web scraping)
  - Skills: `src/skills/` (SKILL.md-based extensible workflows, e.g. DCF valuation)
  - Utils: `src/utils/` (env, config, caching, token estimation, markdown tables)
  - Evals: `src/evals/` (LangSmith evaluation runner with Ink UI)
- Config: `.dexter/settings.json` (persisted model/provider selection)
- Environment: `.env` (API keys; see `env.example`)
- Scripts: `scripts/release.sh`

## Build, Test, and Development Commands

- Runtime: Bun (primary). Use `bun` for all commands.
- Install deps: `bun install`
- Run: `bun run start` or `bun run src/index.tsx`
- Dev (watch mode): `bun run dev`
- Type-check: `bun run typecheck`
- Tests: `bun test`
- Evals: `bun run src/evals/run.ts` (full) or `bun run src/evals/run.ts --sample 10` (sampled)
- CI runs `bun run typecheck` and `bun test` on push/PR.

## Coding Style & Conventions

- Language: TypeScript (ESM, strict mode). JSX via React (Ink for CLI rendering).
- Prefer strict typing; avoid `any`.
- Keep files concise; extract helpers rather than duplicating code.
- Add brief comments for tricky or non-obvious logic.
- Do not add logging unless explicitly asked.
- Do not create README or documentation files unless explicitly asked.

## LLM Providers

- Supported: OpenAI (default), Anthropic, Google, xAI (Grok), OpenRouter, Ollama (local).
- Default model: `claude-opus-4-5`. Provider detection is prefix-based (`claude-` -> Anthropic, `gemini-` -> Google, etc.).
  - Note: Changed default from `gpt-5.4` to `claude-opus-4-5` — I find Anthropic's reasoning quality better for financial analysis tasks.
  - Personal preference: I use `gemini-2.5-pro` when cost is a concern — it's significantly cheaper and still solid for most research queries.
  - Budget option: `gemini-2.5-flash` is even cheaper and fast enough for quick ticker lookups or simple screening tasks.
- Fast models for lightweight tasks: see `FAST_MODELS` map in `src/model/llm.ts`.
- Anthropic uses explicit `cache_control` on system prompt for prompt caching cost savings.
- Users switch providers/models via `/model` command in the CLI.

## Tools

- `financial_search`: primary tool f