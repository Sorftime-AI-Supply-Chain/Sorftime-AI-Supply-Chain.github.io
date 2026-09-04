---
title: "Connect Sorftime MCP Server to OpenClaw Agent"
date: 2026-08-26
type: tutorial
target_platforms: [github, dev.to, linkedin]
status: 待审核
tags: [MCP, OpenClaw, agent-integration, sorftime-seller-agent]
---

# Connect Sorftime MCP Server to OpenClaw Agent

OpenClaw agents can call tools. Sorftime MCP exposes marketplace intelligence as tools. The integration between them is straightforward, but the small details — where the config file lives, how the server process starts, how to verify the connection — are the parts that block sellers who have not done it before.

This article is a step-by-step walkthrough for connecting the sorftime-seller-agent MCP server to an OpenClaw agent. By the end you will have a working setup where the agent can call Sorftime tools in response to natural-language prompts.

---

## What OpenClaw Agents Already Do Well

OpenClaw agents are designed for tool-augmented reasoning. They handle task decomposition, multi-step planning, tool selection, and result synthesis. The agent decides which tool to call, with what arguments, in what order. The tools return data. The agent interprets the data and continues.

For an MCP server to be useful inside an OpenClaw agent, three things must be true:

1. **The server is registered as a tool provider.** The agent must know the server exists and what tools it exposes.
2. **The server process is reachable.** Either running locally and reachable via stdio, or running remotely and reachable via HTTP.
3. **The agent has permission to call the tools.** Either explicitly granted in the agent config, or implicitly allowed by the registration step.

The sorftime-seller-agent supports both stdio and HTTP transport. The walkthrough below covers stdio — the simpler setup, sufficient for most sellers.

---

## Manual Integration Steps

The conventional first-time integration looks like this:

1. Find the OpenClaw agent config file. (For most setups, this is `~/.openclaw/agents/<agent-name>/mcp.json` or equivalent.)
2. Read the existing config to confirm the JSON structure and existing MCP entries.
3. Add a new entry for the Sorftime MCP server.
4. Specify the command to run the server, the args, and any required environment variables (typically an API key).
5. Restart the agent to pick up the new config.
6. Test by sending a prompt that should trigger a Sorftime tool call.
7. If the tool does not fire, check the server logs, confirm the API key is correct, and retry.

Done carefully, this is a 15 to 30 minute process. Done carelessly — by guessing the config schema or skipping the API key — it leaves the seller debugging a silent failure with no clear error message.

---

## Step-by-Step Integration

### Step 1: Install the Sorftime MCP server

```bash
git clone https://github.com/DannylydST/sorftime-seller-agent
cd sorftime-seller-agent
python3 scripts/install.py
```

The install script creates a virtual environment, installs dependencies, and verifies the package imports cleanly. It does not start the server — that step is part of the agent config.

### Step 2: Obtain an API key

Register at [open-intl.sorftime.com](https://open-intl.sorftime.com) and copy the API key. Store it in an environment variable:

```bash
export SORFTIME_API_KEY="your-api-key-here"
```

Do not paste the key directly into a config file that may be committed to version control.

### Step 3: Locate your OpenClaw agent config

For a typical OpenClaw setup, the MCP server registry lives at:

```
~/.openclaw/agents/<your-agent-name>/mcp.json
```

If the file does not exist, create it. The minimal structure:

```json
{
  "mcpServers": {}
}
```

### Step 4: Add the Sorftime server entry

Edit `mcp.json` to add:

```json
{
  "mcpServers": {
    "sorftime-seller-agent": {
      "command": "python3",
      "args": [
        "-m",
        "sorftime_mcp"
      ],
      "env": {
        "SORFTIME_API_KEY": "${SORFTIME_API_KEY}"
      },
      "cwd": "/absolute/path/to/sorftime-seller-agent"
    }
  }
}
```

Notes:
- `cwd` must be the absolute path to the cloned repository.
- `${SORFTIME_API_KEY}` is a reference to the environment variable. The agent expands it at server-start time.
- The exact server module name may vary by version. Check the repository README for the current invocation.

### Step 5: Restart the agent

Stop and restart the OpenClaw agent. On restart, the agent reads the updated `mcp.json`, launches the Sorftime server via the registered command, and queries the server for its tool list. The tools become available to the agent's reasoning layer.

### Step 6: Verify the connection

Send a prompt that should trigger a Sorftime tool:

```
Using the Sorftime MCP server, pull the current top 10 products in the
"stainless steel water bottle" category on Amazon US. Include BSR,
price, and review count for each.
```

If the integration works, the agent calls the relevant tool, returns a structured list, and references the data in its response. If the integration fails, the most common causes are:

- API key not exported in the shell that started the agent.
- `cwd` path is wrong or contains spaces that break the JSON.
- The Python virtual environment created by `install.py` is not on the path used by the `command`. Fix by using the venv's `python` binary directly: `/path/to/sorftime-seller-agent/.venv/bin/python`.

---

## YAML Configuration Example

For sellers who prefer YAML or have a config management layer:

```yaml
agent:
  name: market-research-agent
  mcp_servers:
    sorftime-seller-agent:
      transport: stdio
      command: python3
      args:
        - -m
        - sorftime_mcp
      cwd: /absolute/path/to/sorftime-seller-agent
      env:
        SORFTIME_API_KEY: ${SORFTIME_API_KEY}
      timeout_seconds: 60
```

A YAML-driven setup makes the integration reproducible across machines and easier to version (with the API key reference, not the key itself).

---

## What It Does Well

- **Reproducible.** The integration is a config file. Once the path and key are correct, the setup reproduces identically on any machine. Onboarding a new machine or agent takes minutes.
- **Open source.** The server is open source. The integration code is auditable — a seller who wants to add custom tools or adjust transport settings can read the source and modify it.
- **Where it runs.** The server runs on any machine with Python 3.10+. No Docker required for stdio transport. No remote account required.
- **Multi-agent compatible.** Multiple OpenClaw agents on the same machine can register the same server independently. Each agent gets its own session with the server.

---

## Where It Falls Short

- **It does not auto-start.** The server starts when the agent starts. If the agent is restarted without exporting the API key, the integration fails silently. A common operational pattern is a small wrapper script that exports the env var and then launches the agent.
- **stdio transport limits concurrency.** Stdio is simple but does not support multiple concurrent connections. For sellers running more than one agent against the same server, HTTP transport with a remote server is more robust.
- **Tool list grows over time.** As the sorftime-seller-agent adds endpoints, the tool list expands. An agent prompt that referenced a specific tool name may need adjustment after a major version update.
- **No built-in rate limit dashboard.** The agent can call tools rapidly. Rate-limit feedback surfaces as tool errors, not as a separate UI. Sellers running high-frequency queries need to monitor usage separately.

---

## Try It Yourself

Register for a free API key at [open-intl.sorftime.com](https://open-intl.sorftime.com), then run:

```bash
git clone https://github.com/DannylydST/sorftime-seller-agent
cd sorftime-seller-agent
python3 scripts/install.py
```

Follow the six steps above to wire the server into your OpenClaw agent. Send a verification prompt. If the agent returns data, the integration works. Decisions about which Sorftime tools to call and how to interpret the results are still yours.

[sorftime-seller-agent on GitHub](https://github.com/DannylydST/sorftime-seller-agent) | [Free API Key](https://open-intl.sorftime.com)

![Sorftime Seller Agent — OpenClaw MCP integration config editor](cover)

---

## Sources

[1] Sorftime Seller Agent GitHub Repository — https://github.com/DannylydST/sorftime-seller-agent
[2] Sorftime International MCP Portal — https://open-intl.sorftime.com
[3] Model Context Protocol Specification — https://modelcontextprotocol.io

---

## 审核备注

> 以下为内部审核用，发布前删除。

**综合评级**：✅ 可发布

**已自动修复**：竞对名称/中文叙事/第一人称/感叹号 扫描通过
**建议关注**：标题是否需要微调、CTA 链接是否最新
**来源状态**：产品能力描述基于 sorftime-seller-agent 开源文档，未引用外部来源

*初稿完成：2026-08-26 | 待审核*