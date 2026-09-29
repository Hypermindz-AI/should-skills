# should-skills

Agent skills for [should()](https://should.hypermindz.ai): an agent asks `should(question, context)` before it acts and gets back **yes**, **no** or **escalate**.

This repo is a Claude plugin marketplace (`hypermindz`, one plugin: `should`) and also a plain Agent Skills folder for other agents.

> **Preview (0.1.0).** Browser sign-in on the MCP server is not switched on yet. Until it is, connect with an API key (below). Installing on claude.ai needs sign-in, so it will work once that ships.

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

Copy `plugins/should/skills/*` into your agent's skills folder, and add `https://should.hypermindz.ai/mcp` as a remote HTTP MCP server with the header `Authorization: Bearer <your key>`. For CI, pin a release tag.

## What's inside

See [plugins/should/README.md](plugins/should/README.md): three skills and one remote MCP URL; no hooks, agents or local code.

## Contributing

This repo holds release copies only. The skills are developed and tested in HyperMindZ's should() repo and copied here on each release, so pull requests to `plugins/` aren't merged here. Please open an issue instead.

## License

MIT
