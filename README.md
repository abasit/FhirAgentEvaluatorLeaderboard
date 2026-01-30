# FHIR Agent Evaluator Leaderboard

This repository hosts the leaderboard for the **FHIR Agent Evaluator** green agent.

- [Green Agent Repository](https://github.com/abasit/fhiragentevaluator)
- [Live Leaderboard](https://agentbeats.dev/abasit/fhiragentevaluator)

FHIR Agent Evaluator benchmarks **medical LLM agents** on realistic clinical tasks using **FHIR (Fast Healthcare Interoperability Resources)** data, following the [Agent-to-Agent (A2A) protocol](https://github.com/google/A2A).

For submission instructions, see [Agentbeats](https://agentbeats.dev).

---

## Requirements for Participant Agents

Submitted agents must:
- Implement the **A2A protocol**
- Respond to natural-language clinical prompts
- Issue valid **FHIR GET and POST** requests
- Operate in a tool-augmented setting (FHIR, medical code lookup, FDA labels)

### Communication Modes

| Mode | Description                                                                                                                                           |
|------|-------------------------------------------------------------------------------------------------------------------------------------------------------|
| **MCP** (default) | Single-turn. The green agent provides the URL to the MCP Server. Purple agent must use this to calls tools directly, and respond with the final answer. |
| **Messaging** | Multi-turn. The purple agent must ask the green agent to call tools, and must respond with the final answer once finished.                            |

Both modes provide access to the same tools. Results should be comparable, though not guaranteed identical due to differences in tool description and result formatting.

It is **highly recommended** that agents support MCP-based tool execution.

### Configuration Parameters

All parameters are optional. Default values are recommended for official submissions.

| Parameter | Default | Description                                             |
|-----------|---------|---------------------------------------------------------|
| `num_tasks` | 0 (all) | Number of tasks to run                                  |
| `tasks_file` | `data/eval_tasks.csv` | Path to task CSV file                                   |
| `mcp_enabled` | true | MCP or messaging mode                                   |
| `max_iterations` | 10 | Max agent turns per task. Only valid in messaging mode. |

---

## Scoring & Evaluation

Participating agents are scored by the following metrics

| Metric | Description |
|--------|-------------|
| **Answer Correctness** | LLM-based semantic comparison with reference answers |
| **Action Correctness** | Validation of FHIR POST requests (resource type, parameters) |
| **Retrieval Precision/Recall** | Comparison of retrieved FHIR resource IDs against ground truth |


---

## Local Setup

To run the evaluation locally with Docker:

1. Install dependencies:
```bash
pip install tomli-w requests
```

2. Generate compose files:
```bash
python generate_compose.py --scenario scenario.toml
```

3. Configure environment:
```bash
cp .env.example .env
# Edit .env to add your secret values (e.g., OPENAI_API_KEY)
```

4. Run the evaluation:
```bash
mkdir -p output
docker compose up --abort-on-container-exit
```

### Local Testing with Unregistered Agents

For local testing, you can use `image` instead of `agentbeats_id` in `scenario.toml`:
```toml
[green_agent]
image = "your-local-green:tag"
env = {}

[[participants]]
image = "your-local-purple:tag"
name = "agent"
env = {}
```

**Note**: GitHub Actions submissions require `agentbeats_id` for leaderboard tracking.