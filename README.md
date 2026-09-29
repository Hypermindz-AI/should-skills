# should-skills

Agent skills for [should()](https://should.hypermindz.ai): an agent asks `should(question, context)` before it acts and gets back **yes**, **no** or **escalate**.

This repo is a Claude plugin marketplace (`hypermindz`, one plugin: `should`) and also a plain Agent Skills folder for other agents.

> **Preview (0.1.1).** Browser sign-in on the MCP server is not switched on yet. Until it is, connect with an API key (below). Installing on claude.ai needs sign-in, so it will work once that ships.

## Claude Code

```
/plugin marketplace add Hypermindz-AI/should-skills
/plugin install should@hypermindz
```

Until sign-in ships, create a key at https://should.hypermindz.ai/keys, export it as `SHOULD_API_KEY`, and add the server with the key header:

```
claude mcp add --transport http should https://should.hypermindz.ai/mcp --header "Authorization: Bearer $SHOULD_API_KEY"
```

## claude.ai and Cowork

Customize › Plugins › add marketplace › `Hypermindz-AI/should-skills`, then connect `should` on the plugin's Connectors tab (once sign-in ships).

## Other agents (Codex, Cursor, Agent SDK)

Copy `plugins/should/skills/*` into your agent's skills folder, and add `https://should.hypermindz.ai/mcp` as a remote HTTP MCP server with the header `Authorization: Bearer <your key>`. For CI, pin a release tag. An agent with a shell but no MCP can use the CLI instead (next section).

## CLI (scripts, CI and agents without MCP)

The `should` CLI makes the same decision as the MCP tool, and its exit code is the answer, so a script or an agent's shell step proceeds only on a clear yes.

> **Not on npm yet.** The package is `@hypermindz/should` (MIT, Node 22+); it isn't published yet. Until it is, use the MCP server or the HTTP API (`POST https://should.hypermindz.ai/v1/should`).

```sh
export SHOULD_API_KEY=…          # a key from https://should.hypermindz.ai/keys
# SHOULD_URL defaults to https://should.hypermindz.ai

npx @hypermindz/should ask "Is this refund within policy?" --context refund.json
# YES · 0.93 · … · dec_…
```

| Exit | Meaning |
| --- | --- |
| 0 | yes |
| 1 | no |
| 2 | escalate: don't act; ask a person |
| 3 | usage error |
| 4 | auth, API or network error |
| 5 | a decided choice or score (0 with `--accept-kinds`) |

Gate a step on it:

```sh
npx @hypermindz/should ask "Is this migration safe to apply without review?" --context migration.sql
case $? in
  0) ./apply-migration.sh ;;
  *) echo "Not approved (no, escalated, or error): needs human review"; exit 1 ;;
esac
```

How the skills' MCP calls map to the CLI:

| MCP | CLI |
| --- | --- |
| `should` | `should ask "<question>" --context <file\|->` or `--context-json '<json>'`; `--template <id@version>` for a template |
| thresholds | `--threshold 0.8`, or `--yes-threshold 0.95 --no-threshold 0.8` |
| choice / score | `--kind choice --option <a> --option <b>`, `--kind score --level … --min-score <k>` |
| `get_decision` | `should log <decision_id>` |
| `record_outcome` | `should outcome <decision_id> correct\|incorrect\|unknown [--actual yes\|no] [--source …]` |

`--json` prints the full result (the same v1 result the MCP tool returns). `--dry-run` prints the context and sends nothing, with no key needed. `should audit [path]` finds LLM calls in your code that are really yes/no decisions, offline. Batches (`should_batch`) and `explain_decision` are MCP and HTTP only. Run `should help` for every flag.

## What's inside

See [plugins/should/README.md](plugins/should/README.md): three skills and one remote MCP URL; no hooks, agents or local code.

## Contributing

This repo holds release copies only. The skills are developed and tested in HyperMindZ's should() repo and copied here on each release, so pull requests to `plugins/` aren't merged here. Please open an issue instead.

## License

MIT
