# 🤖 Multi-Agent Planner

A multi-agent system built with the Anthropic API where a smart 
Orchestrator (Claude Sonnet) coordinates three specialized worker 
agents (Claude Haiku) to plan, execute, and critique complex tasks 
— with real-time cost tracking.

---

## Architecture

```
Orchestrator (claude-sonnet-4-6)   ← coordinates the full run
        | tool_use
  +-----+------------------+
Planner (Haiku)   Executor (Haiku)   Critic (Haiku)
  ↑ cheap workers, each a fresh Haiku call
```

**How it works:**
1. Orchestrator receives a goal and calls the **Planner** to break it into ordered subtasks
2. **Executor** is called for each subtask, with context passed from completed steps
3. **Critic** reviews the final output and returns a score (1–10) with issues and suggestions
4. If score < 7, the Orchestrator re-executes failing subtasks with the Critic's feedback
5. Final answer is returned to the user

---

## Cost Optimizations

- **Haiku for all workers** — ~12x cheaper than Sonnet
- **Prompt caching** on system prompts — up to 90% off repeated tokens
- **Tight `max_tokens`** per role — 2048 for Orchestrator, 1024 for workers
- **Token usage logged** after every API call with per-call cost

---

## Notebooks

| Notebook | Description |
|---|---|
| `multi_agent.ipynb` | Synchronous version — workers called sequentially |
| `multi_agent_async.ipynb` | Async version — workers dispatched concurrently via `asyncio.gather()` when Orchestrator batches multiple tool calls. Same cost, reduced wall-clock time. |

---

## Setup

### 1. Clone & Install
```bash
git clone https://github.com/raheeltanvir/multi-agent-planner
cd multi-agent-planner
pip install anthropic python-dotenv
```

### 2. Set API Key
Create a `.env` file:
```
ANTHROPIC_API_KEY=your_api_key_here
```

### 3. Run
Open either notebook in Jupyter and run all cells. Then call:
```python
result = run("Your goal here")
print(result)
```

---

## Example

```python
run("Write a beginner's guide to prompt engineering")
```

The Orchestrator will plan subtasks, execute each one, critique the combined output, and refine if needed — logging token usage and cost at every step.

---

## License

MIT
