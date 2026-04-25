# Agentic Design Patterns with LangGraph

> **Six core patterns for building real LLM apps — one notebook each, every example runnable, every diagram from the official docs.**

[![LangGraph](https://img.shields.io/badge/LangGraph-1.1+-1C3C3C?logo=langchain&logoColor=white)](https://langchain-ai.github.io/langgraph/)
[![Python](https://img.shields.io/badge/Python-3.12+-3776AB?logo=python&logoColor=white)](https://www.python.org/)
[![Gemini](https://img.shields.io/badge/Google%20Gemini-3%20Flash-4285F4?logo=google&logoColor=white)](https://ai.google.dev/)
[![Docs](https://img.shields.io/badge/Based%20on-LangChain%20Docs-blue)](https://docs.langchain.com/oss/python/langgraph/workflows-agents)

A complete, runnable companion to the official LangChain page on **[Workflows & Agents](https://docs.langchain.com/oss/python/langgraph/workflows-agents)**. Designed so a beginner can read top-to-bottom and a professional engineer can skim the table of contents and copy what fits.

Written by [**Muhammad Abdullah**](https://github.com/MuhammadAbdullah95) — Agentic AI Engineer.

---

## Why this exists

The official LangChain docs explain each pattern beautifully — but the code is small fragments and assumes you've already set up an LLM. This series:

- Bundles each pattern into a **standalone notebook** that runs end-to-end.
- Adds a **second real-world example** for every pattern (customer-support triage, study-guide generator, tweet polisher, mini research agent, and more).
- Renders the **graph image at every step** so you see what you just built.
- Uses **plain language**, with "when to use it / when *not* to use it" for every pattern.

Every notebook follows the same shape — concept → diagram → setup → docs example → real-world example → takeaways → exercise — so once you've read one, the rest are easy to navigate.

---

## The six patterns

| # | Pattern | The idea in one line |
|---|---|---|
| 1 | **Prompt Chaining** | Run LLM calls in sequence — output of one becomes input of the next. |
| 2 | **Parallelization** | Run independent LLM calls at the same time, then combine. |
| 3 | **Routing** | An LLM looks at the input and picks which specialist handles it. |
| 4 | **Orchestrator–Worker** | A planner LLM decides what sub-tasks exist, dispatches workers, then synthesizes. |
| 5 | **Evaluator–Optimizer** | One LLM generates, another grades it, loop until good enough. |
| 6 | **Agent** | Give the LLM tools and let it decide, turn by turn, what to call. |

> Patterns 1–5 are **workflows** — you wire the graph in advance.
> Pattern 6 is an **agent** — the LLM decides the path at runtime.

---

## Notebook tour

Each notebook is self-contained (the same setup cell is at the top of all of them).

### [00_workflows_vs_agents.ipynb](00_workflows_vs_agents.ipynb) — Start here
The **mental model**: what an "augmented LLM" is, the difference between workflows and agents, and a setup smoke test so you know your Gemini key works before class starts.

### [01_prompt_chaining.ipynb](01_prompt_chaining.ipynb)
- **Docs example:** joke generator with a punchline-check gate
- **Real-world example:** rough-note → professional email refiner (draft → grammar → tone)

### [02_parallelization.ipynb](02_parallelization.ipynb)
- **Docs example:** generate joke + story + poem in parallel from one topic
- **Real-world example:** multi-channel content generator — turn one article into a newsletter blurb, a tweet, and a LinkedIn post simultaneously

### [03_routing.ipynb](03_routing.ipynb)
- **Docs example:** route requests to story / joke / poem specialists with `with_structured_output`
- **Real-world example:** customer-support triage — billing vs. technical vs. general, each with its own system prompt

### [04_orchestrator_worker.ipynb](04_orchestrator_worker.ipynb)
- **Docs example:** dynamic report writer — planner picks sections, workers write them in parallel via the `Send` API
- **Real-world example:** study-guide generator — given any subject, plan topics → dispatch a worker per topic → assemble guide

### [05_evaluator_optimizer.ipynb](05_evaluator_optimizer.ipynb)
- **Docs example:** generate-grade-loop for jokes ("funny / not funny" + feedback)
- **Real-world example:** tweet polisher with a quality bar (hook + length + single idea) and a `max_attempts` safety guard

### [06_agent.ipynb](06_agent.ipynb)
- **Docs example:** arithmetic agent with `add`, `multiply`, `divide` tools
- **Real-world example:** mini research agent with `web_search` (Tavily) + `word_count`

---

## Quickstart

From the **project root** (one level up):

```bash
uv sync                                   # install deps
echo "GEMINI_API_KEY=your-key" > .env     # add API key
uv run jupyter lab                        # open notebooks
```

Then open `Agentic_Design_Patterns/00_workflows_vs_agents.ipynb` and start there.

### What you need

| | Required for | Get it from |
|---|---|---|
| `GEMINI_API_KEY` | All notebooks | <https://aistudio.google.com/apikey> (free tier) |
| `TAVILY_API_KEY` | Notebook 06's research-agent example only | <https://tavily.com/> (free 1000 searches/month) |

If you skip Tavily, just don't run the second example in notebook 06 — the first example uses no external tools.

---

## How to read each notebook

Every notebook is structured the same way, so once you've done one you can skim the rest:

1. **The idea in one line** — the pattern in a single sentence
2. **When to use it / when *not* to** — the most useful part for engineers
3. **The shape of the graph** — official diagram + ASCII version
4. **Setup** — copy-pasteable LLM init (same in every notebook)
5. **Docs example** — the canonical version from the LangChain docs, ported to Gemini
6. **Graph visualization** — `display(Image(graph.get_graph().draw_mermaid_png()))`
7. **Real-world example** — a more practical use case
8. **Takeaways** — bullet summary
9. **Try it yourself** — exercise to extend the pattern

---

## Cheat sheet

```
Predefined steps?         → Prompt Chaining (1)
Independent steps that can run together? → Parallelization (2)
One of N specialists handles the input?  → Routing (3)
Sub-tasks aren't known until runtime?    → Orchestrator–Worker (4)
You can grade output and want to retry?  → Evaluator–Optimizer (5)
Open-ended, tool-driven problem?         → Agent (6)
```

Most real-world systems **combine** these — e.g. an agent whose tools are themselves chains, or a router whose branches each contain an evaluator–optimizer loop. **Start simple, only add complexity when a pattern genuinely fits.**

---

## Common gotchas

- **`Annotated[list, operator.add]`** in the orchestrator–worker pattern is what makes worker outputs *append* to the list instead of overwriting it. Forgetting it is the #1 bug.
- **`with_structured_output(PydanticModel)`** is how you force a router's decision to be a clean enum value. Don't try to parse free-form text.
- **Always cap evaluator–optimizer loops** with a `max_attempts` guard. An overly strict judge will loop forever.
- **AVIF images** in this folder render in modern Jupyter (VS Code, JupyterLab, GitHub, nbviewer). If you see broken images in an older client, the ASCII diagram below it shows the same thing.

---

## Credits

- Pattern definitions, canonical examples, and diagrams: the official **[LangChain Workflows & Agents](https://docs.langchain.com/oss/python/langgraph/workflows-agents)** documentation.
- The "augmented LLM" framing comes from Anthropic's [Building Effective Agents](https://www.anthropic.com/research/building-effective-agents) post that the LangChain docs reference.
- Written by [**Muhammad Abdullah**](https://github.com/MuhammadAbdullah95), Agentic AI Engineer.

---

## License

MIT — fork it, teach with it, ship from it.

---

If this helped you, **star the repo** and share it with one person who's stuck building agents.
