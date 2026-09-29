# should plugin

Three skills and one remote MCP server. Nothing else: no hooks, agents, commands or local code.

| Skill | What it does |
| --- | --- |
| `should-question-design` | Turns a business decision into a well-formed `should` call: yes-means-proceed phrasing, structured context, hard rules in code, thresholds, batching, escalation handling |
| `should-action-guard` | Guards an agent's tool calls: safe actions run, destructive or unrequested ones escalate with a one-line impact summary |
| `should-refund-gate` | Decides support refunds and exceptions: policy checks in code, amount-scaled thresholds, distress and fraud checks, queue routing |

## What it can reach

- **MCP server:** `should` at `https://should.hypermindz.ai/mcp` (HTTP). It receives the question and the context your agent sends with each call.
- **Nothing else.** The skills are instructions to the model; they run no code on your machine.

## Without MCP

An agent with a shell can call the `should` CLI instead (npm `@hypermindz/should`, not published yet): its exit code is the answer, 0 only for yes. `should-question-design` says how; the [repo README](../../README.md#cli-scripts-ci-and-agents-without-mcp) lists the commands and exit codes. The CLI is a separate install, not part of this plugin.

## Data policy

should() stores a SHA-256 hash of each input, not the raw context, unless you opt in. See https://should.hypermindz.ai/privacy.

## Settings

Thresholds, refund bands and tool lists are defaults written in each `SKILL.md`. Edit your copy to change them.
