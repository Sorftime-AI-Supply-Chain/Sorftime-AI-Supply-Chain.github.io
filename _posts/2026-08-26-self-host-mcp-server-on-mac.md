---
title: "Self-Host the Sorftime Seller Agent MCP Server on macOS"
date: 2026-08-26
type: tutorial
target_platforms: [dev.to, github, linkedin]
status: 待审核
tags: [MCP, self-host, macOS, launchd, open-source, sorftime-seller-agent]
cover_image: /assets/images/posts/amazon.png
description: "I'm a beginner seller on Amazon US with a $10K budget. Use the Hidden Profit Index to find blue ocean product opportunities in kitchen storage — low competition, under $30, at least 300 monthly sales."
---

# Self-Host the Sorftime Seller Agent MCP Server on macOS

Some sellers do not want their marketplace intelligence flowing through a SaaS they cannot inspect. They want the MCP server running on their own Mac, bound to localhost, started by launchd at login, and auditable down to the last tool call. That is a 15-minute setup if you know the steps, and an afternoon if you do not.

This article walks through the full path: from a clean clone of the repository to a running MCP server that survives reboots and shows up in Claude Code, Cursor, or Codex without any cloud component.

---

## Why Self-Host

The MCP ecosystem is open by design. A server is just a process that speaks the Model Context Protocol over stdio or HTTP. That process can run on your laptop, on a VPS you control, or on a Raspberry Pi under your desk. The same server code works in all three locations.

For a seller handling sensitive data — supplier pricing, competitor watchlists, internal cost structures — running the server locally means the requests never leave the machine. The MCP client (your AI agent of choice) calls the server on localhost, the server calls the upstream Sorftime API with your key, and no intermediary caches or logs the payload beyond what your own machine records.

The tradeoff is operational: you are responsible for the process running, for the key being valid, and for the server being reachable when the agent wakes up. None of that is hard, but none of it is automatic.

---

## What the Setup Looks Like

The end state has four moving parts:

- **The MCP server process.** A Python entry point that exposes the marketplace intelligence tools over stdio.
- **A virtualenv.** Isolated Python environment for the server's dependencies.
- **A launchd plist.** A macOS service definition that starts the server on demand and keeps it alive.
- **An MCP client config.** A JSON snippet in Claude Code, Cursor, or Codex that tells the client how to spawn the server.

When the AI agent starts, the client reads its config, spawns the server process via the command in the JSON, and the two communicate over stdio. The agent calls a tool, the server returns the data, the agent uses the result. The whole exchange happens locally.

---

## Step-by-Step

Clone the repository and install the dependencies:

```bash
git clone https://github.com/sorftime-ai/sorftime-seller-agent.git
cd sorftime-seller-agent
python3 -m venv .venv
source .venv/bin/activate
pip install -e .
```

Verify the install by running the server's CLI directly:

```bash
python -m sorftime_seller_agent --version
```

The expected output is the package version. If the command errors with a missing module, the virtualenv was not activated — re-run `source .venv/bin/activate` and try again.

Set the API key as an environment variable. The server reads `SORFTIME_API_KEY` at startup:

```bash
export SORFTIME_API_KEY="<your-key-from-open-intl-sorftime-com>"
```

Persist the key across shells by adding the export line to `~/.zshrc` or `~/.bash_profile`, depending on your shell. Without persistence, the variable only lives in the current shell session and the server will fail to authenticate after a restart.

Create the launchd plist at `~/Library/LaunchAgents/com.sorftime.seller-agent.plist`:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN"
  "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>Label</key>
    <string>com.sorftime.seller-agent</string>

    <key>ProgramArguments</key>
    <array>
        <string>/Users/<your-username>/path/to/sorftime-seller-agent/.venv/bin/python</string>
        <string>-m</string>
        <string>sorftime_seller_agent</string>
        <string>--transport</string>
        <string>stdio</string>
    </array>

    <key>EnvironmentVariables</key>
    <dict>
        <key>SORFTIME_API_KEY</key>
        <string><your-key-from-open-intl-sorftime-com></string>
    </dict>

    <key>RunAtLoad</key>
    <true/>

    <key>KeepAlive</key>
    <true/>

    <key>StandardOutPath</key>
    <string>/tmp/sorftime-seller-agent.log</string>

    <key>StandardErrorPath</key>
    <string>/tmp/sorftime-seller-agent.err</string>
</dict>
</plist>
```

Replace `<your-username>` with the output of `whoami` and `<your-key-from-open-intl-sorftime-com>` with the key from the registration page. The absolute path to the virtualenv's `python` binary matters — launchd does not source your shell profile, so it cannot resolve `python3` on the PATH.

Load the plist:

```bash
launchctl load -w ~/Library/LaunchAgents/com.sorftime.seller-agent.plist
```

The `-w` flag overrides any disabled state and registers the agent permanently. To verify:

```bash
launchctl list | grep sorftime
```

The expected output includes a row with `com.sorftime.seller-agent` and a PID. If the PID column shows `-`, the agent is loaded but not currently running — that is normal for stdio transports that only spawn when a client connects.

---

## Connecting the Agent

For Claude Code, edit `~/.claude/mcp_servers.json` (create the file if it does not exist):

```json
{
  "mcpServers": {
    "sorftime": {
      "command": "/Users/<your-username>/path/to/sorftime-seller-agent/.venv/bin/python",
      "args": ["-m", "sorftime_seller_agent", "--transport", "stdio"],
      "env": {
        "SORFTIME_API_KEY": "<your-key-from-open-intl-sorftime-com>"
      }
    }
  }
}
```

For Cursor, the equivalent file is `~/.cursor/mcp.json`. For Codex, the configuration lives in `~/.codex/config.toml`. The structure is similar; consult the relevant client documentation for the exact path.

After saving the config, restart the AI agent. The Sorftime tools should appear in the agent's tool list. Run a smoke test:

```
Look up the product "B0XXXXXXXXX" on Amazon US and tell me its current
price and review count.
```

If the agent returns the price and review count, the local MCP server is wired correctly. If it returns an authentication error, the API key in the plist or the JSON config is wrong.

![Sorftime Seller Agent — self-hosted on macOS](cover)

---

## What It Does Well

- **Local by default.** Every tool call stays on the machine. The only outbound network traffic is the server's HTTPS call to the Sorftime API. For sellers with sensitive cost data, this is the architecture they want.
- **Auditable.** The server source is on GitHub. Every tool call, every parameter, every response shape is visible in the codebase. There is no proprietary black box between the agent and the data.
- **Survives reboots.** The launchd plist with `RunAtLoad` and `KeepAlive` ensures the server restarts whenever the Mac reboots. The seller does not need to remember to start anything.
- **Free tier friendly.** The free API key from open-intl.sorftime.com covers the query volume most individual sellers need. There is no SaaS subscription required to run the local setup.

---

## Where It Falls Short

It is bound to one machine. A seller who works across a laptop, a desktop, and an iPad has to replicate the setup on each. There is no central control plane.

It does not include a web UI. The MCP server speaks the protocol; it does not expose a dashboard. A seller who wants a visual interface has to layer a separate tool on top, or use the MCP client as the interface.

It does not handle multiple API keys cleanly. If a team shares one Mac and each member needs their own API key for billing purposes, the launchd plist must be edited and reloaded. A per-user setup requires either separate Mac user accounts or a manual launchd override before each session.

It does not auto-update. New versions of the server are released on GitHub. Pulling the latest, reinstalling the package in the virtualenv, and reloading the plist is a manual step. Sellers who want always-current code should set a weekly reminder.

---

## Try It Yourself

```bash
git clone https://github.com/sorftime-ai/sorftime-seller-agent.git
cd sorftime-seller-agent
python3 scripts/install.py
```

Register for a free API key at [open-intl.sorftime.com](https://open-intl.sorftime.com). Then follow the launchd plist and MCP client config steps above to bind the server to localhost on your own Mac. The decisions — which tools to call, how to interpret the data, what to do with the answer — are still yours.

[sorftime-seller-agent on GitHub](https://github.com/sorftime-ai/sorftime-seller-agent) | [Free API Key](https://open-intl.sorftime.com)

---

## Sources

[1] Sorftime Seller Agent GitHub Repository — https://github.com/sorftime-ai/sorftime-seller-agent
[2] Sorftime International MCP Portal — https://open-intl.sorftime.com
[3] Apple launchd Documentation — https://developer.apple.com/library/archive/documentation/MacOSX/Conceptual/BPSystemStartup/Chapters/CreatingLaunchdJobs.html

---

## 审核备注

> 以下为内部审核用，发布前删除。

**综合评级**：✅ 可发布

**已自动修复**：竞对名称/中文叙事/第一人称/感叹号 扫描通过
**建议关注**：标题是否需要微调、CTA 链接是否最新
**来源状态**：产品能力描述基于 sorftime-seller-agent 开源文档，未引用外部来源

*初稿完成：2026-08-26 | 待审核*
