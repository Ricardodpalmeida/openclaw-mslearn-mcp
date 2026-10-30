# Microsoft Learn MCP reference

For setup, fallback clients and troubleshooting. The Learn CLI 1.0.0 was tested with live discovery, search, fetch and code search on 2026-10-30. OpenClaw details below follow its [current registry documentation](https://docs.openclaw.ai/cli/mcp/registry); check the installed version before configuration changes.

## Endpoint and tools

`https://learn.microsoft.com/api/mcp` uses Streamable HTTP, needs no key/sign-in and serves public Microsoft Learn content, including Visual Studio and SDKs. HTTP 405 from a browser is expected: use an MCP client.

Discover schemas at runtime. `microsoft_docs_search(query)` returns documentation and Q&A excerpts; `microsoft_docs_fetch(url)` returns page markdown; `microsoft_code_sample_search(query, language?)` returns official snippets. No `locale` or `product` parameters.

Experimental options from [Microsoft](https://github.com/MicrosoftDocs/mcp), subject to change:

- `https://learn.microsoft.com/api/mcp?maxTokenBudget=2000`: limits search response tokens approximately.
- `https://learn.microsoft.com/api/mcp/openai-compatible`: alternative profile for Deep Research models; unnecessary for OpenClaw.

## OpenClaw native MCP

Use connected tools directly. `list`, `show`, `status` and `doctor` without `--probe` inspect configuration while the Gateway runs. Saved configuration alone does not prove tools are reachable or visible to an agent.

Current OpenClaw CLI mutations and live probes require the local Gateway to be stopped. Prefer supported Control UI operations for live setup, or the standalone Learn CLI for this task. Do not stop a running Gateway merely to discover tools. If a maintenance window and setup are authorized:

```bash
openclaw mcp add mslearn --url https://learn.microsoft.com/api/mcp --transport streamable-http
openclaw mcp doctor mslearn --probe
openclaw mcp status --verbose
```

Use explicit Streamable HTTP transport; older builds may otherwise assume SSE. Verify agent tool availability after the applicable reload/restart. `openclaw mcp reload` affects only the current CLI process, not another running Gateway.

Authorized configuration controls:

```bash
openclaw mcp tools mslearn --include 'microsoft_docs_search,microsoft_docs_fetch'
openclaw mcp unset mslearn
```

These change saved definitions. Agent/runtime filters still apply; registration does not guarantee visibility to every agent.

## Microsoft Learn CLI

Node.js 22+, binary `mslearn`, pinned package 1.0.0:

```bash
npx -y @microsoft/learn-cli@1.0.0 search "<query>" --json
npx -y @microsoft/learn-cli@1.0.0 fetch "<learn URL>" --section "<heading>" --max-chars 6000
npx -y @microsoft/learn-cli@1.0.0 code-search "<query>" --language python --json
npx -y @microsoft/learn-cli@1.0.0 doctor --format json
```

`doctor` checks runtime, endpoint, handshake and discovery. Review the package changelog before changing the pin. `mslearn setup` installs Microsoft's discovery skill for supported agents; it is unnecessary for this OpenClaw skill.

## mcporter

Use only on hosts already using mcporter. Ad-hoc calls need no saved config:

```bash
mcporter call https://learn.microsoft.com/api/mcp.microsoft_docs_search query="Azure Functions triggers"
```

If persistent setup/removal is authorized (syntax baseline: mcporter 0.16.0):

```bash
mcporter config add mslearn https://learn.microsoft.com/api/mcp --scope home
mcporter list mslearn --schema
mcporter call mslearn.microsoft_code_sample_search query="Python blob upload" language=python --output json
mcporter config remove mslearn
```

Do not use the obsolete `config add --name ... --url ... --type http` form. mcporter reads `./config/mcporter.json` and `~/.mcporter/mcporter.json` in the `mcpServers` shape; OpenClaw's registry is separate.

## Troubleshooting

| Symptom | Action |
|---------|--------|
| Saved server, missing agent tools | Check runtime/filter state and reload requirements; a saved definition is insufficient. |
| Probe refuses while Gateway runs | Use live Control UI or standalone CLI; reserve stopping the Gateway for authorized maintenance. |
| Connection failure | Run Learn CLI `doctor`; inspect host DNS/proxy settings. |
| Unknown tool/parameter | Rediscover schemas and adapt. |
| HTTP 429/repeated timeouts | Stop and report; no retry loop. |
| HTTP 405 | Use an MCP client, not browser-style endpoint access. |
| Off-topic search | Include product, feature, version or SKU. |

Microsoft also supplies `microsoft-docs`, `microsoft-code-reference` and `microsoft-skill-creator` for its supported agent integrations. Prefer a working built-in Learn connector if OpenClaw provides one.

Sources: [Microsoft MCP](https://github.com/MicrosoftDocs/mcp), [release notes](https://learn.microsoft.com/en-us/training/support/mcp-release-notes), [CLI](https://www.npmjs.com/package/@microsoft/learn-cli), [OpenClaw registry](https://docs.openclaw.ai/cli/mcp/registry), [MCP](https://modelcontextprotocol.io/).
