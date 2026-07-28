---
layout: default
title: Home
---

<style>
:root {
  --bg: #0a0a0f;
  --surface: #14141c;
  --surface-2: #1c1c28;
  --border: #2a2a3a;
  --text: #e8e8ee;
  --text-dim: #8a8a9a;
  --accent: #00d9a3;
  --accent-2: #4d9fff;
  --accent-3: #ff6b9d;
}

* { box-sizing: border-box; margin: 0; padding: 0; }

body {
  background: var(--bg);
  color: var(--text);
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", system-ui, sans-serif;
  line-height: 1.6;
  font-size: 16px;
}

/* 暗模式覆盖默认主题 */
.page-content, .wrapper, .post-content, body {
  background: var(--bg) !important;
  color: var(--text) !important;
}

a { color: var(--accent); text-decoration: none; transition: 0.2s; }
a:hover { color: var(--accent-2); }

.hero {
  position: relative;
  padding: 140px 24px 100px;
  text-align: center;
  background: radial-gradient(ellipse 1200px 600px at 50% 0%, rgba(0,217,163,0.15), transparent 60%),
              radial-gradient(ellipse 800px 400px at 80% 50%, rgba(77,159,255,0.1), transparent 60%),
              var(--bg);
  border-bottom: 1px solid var(--border);
  overflow: hidden;
}
.hero::before {
  content: '';
  position: absolute;
  inset: 0;
  background-image:
    linear-gradient(rgba(255,255,255,0.02) 1px, transparent 1px),
    linear-gradient(90deg, rgba(255,255,255,0.02) 1px, transparent 1px);
  background-size: 50px 50px;
  pointer-events: none;
}
.hero-content { position: relative; max-width: 800px; margin: 0 auto; }
.hero-badge {
  display: inline-block;
  padding: 6px 14px;
  background: var(--surface);
  border: 1px solid var(--border);
  border-radius: 100px;
  font-size: 13px;
  color: var(--accent);
  margin-bottom: 24px;
  font-weight: 500;
}
.hero h1 {
  font-size: 64px;
  font-weight: 800;
  letter-spacing: -2px;
  line-height: 1.05;
  margin-bottom: 20px;
  background: linear-gradient(135deg, #fff 0%, var(--accent) 50%, var(--accent-2) 100%);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
}
.hero p {
  font-size: 19px;
  color: var(--text-dim);
  max-width: 600px;
  margin: 0 auto 36px;
}
.hero-cta {
  display: inline-flex;
  gap: 12px;
  flex-wrap: wrap;
  justify-content: center;
}
.btn {
  display: inline-block;
  padding: 13px 28px;
  border-radius: 8px;
  font-weight: 600;
  font-size: 15px;
  border: 1px solid var(--border);
  transition: 0.2s;
}
.btn-primary { background: var(--accent); color: #0a0a0f; border-color: var(--accent); }
.btn-primary:hover { background: #00c994; transform: translateY(-1px); }
.btn-secondary { background: var(--surface); color: var(--text); }
.btn-secondary:hover { background: var(--surface-2); }

.section { padding: 80px 24px; max-width: 1100px; margin: 0 auto; }
.section-title {
  font-size: 14px;
  text-transform: uppercase;
  letter-spacing: 2px;
  color: var(--accent);
  margin-bottom: 12px;
  font-weight: 600;
}
.section h2 {
  font-size: 36px;
  font-weight: 700;
  letter-spacing: -1px;
  margin-bottom: 16px;
  color: #fff;
}
.section-sub {
  font-size: 17px;
  color: var(--text-dim);
  max-width: 600px;
  margin-bottom: 48px;
}

.grid { display: grid; gap: 20px; }
.grid-3 { grid-template-columns: repeat(auto-fit, minmax(280px, 1fr)); }

.card {
  background: var(--surface);
  border: 1px solid var(--border);
  border-radius: 12px;
  padding: 28px;
  transition: 0.2s;
  position: relative;
  overflow: hidden;
}
.card::before {
  content: '';
  position: absolute;
  top: 0; left: 0; right: 0;
  height: 2px;
  background: linear-gradient(90deg, var(--accent), var(--accent-2));
  transform: scaleX(0);
  transform-origin: left;
  transition: transform 0.3s;
}
.card:hover { border-color: var(--accent); transform: translateY(-2px); }
.card:hover::before { transform: scaleX(1); }
.card-icon {
  width: 40px; height: 40px;
  border-radius: 8px;
  background: var(--surface-2);
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 20px;
  margin-bottom: 16px;
}
.card h3 { font-size: 18px; margin-bottom: 8px; color: #fff; }
.card p { color: var(--text-dim); font-size: 14px; }

.posts { display: grid; gap: 16px; }
.post-row {
  display: block;
  padding: 24px 28px;
  background: var(--surface);
  border: 1px solid var(--border);
  border-radius: 12px;
  transition: 0.2s;
  text-decoration: none;
  color: var(--text);
}
.post-row:hover { border-color: var(--accent); transform: translateX(4px); }
.post-row .meta {
  display: flex;
  gap: 12px;
  font-size: 13px;
  color: var(--text-dim);
  margin-bottom: 6px;
}
.post-row h3 { font-size: 19px; color: #fff; margin-bottom: 6px; }
.post-row .desc { color: var(--text-dim); font-size: 14px; }

.empty {
  text-align: center;
  padding: 60px 24px;
  color: var(--text-dim);
  background: var(--surface);
  border: 1px dashed var(--border);
  border-radius: 12px;
}

.cta-block {
  text-align: center;
  padding: 80px 24px;
  background: radial-gradient(ellipse 800px 400px at 50% 50%, rgba(0,217,163,0.08), transparent 60%),
              var(--surface);
  border: 1px solid var(--border);
  border-radius: 16px;
  margin: 60px auto;
  max-width: 800px;
}
.cta-block h2 { font-size: 36px; margin-bottom: 16px; }
.cta-block p { color: var(--text-dim); margin-bottom: 28px; }

footer {
  text-align: center;
  padding: 40px 24px;
  color: var(--text-dim);
  font-size: 14px;
  border-top: 1px solid var(--border);
}

@media (max-width: 600px) {
  .hero { padding: 80px 20px 60px; }
  .hero h1 { font-size: 42px; }
  .section { padding: 60px 20px; }
  .section h2, .cta-block h2 { font-size: 28px; }
}
</style>

<!-- HERO -->
<section class="hero">
  <div class="hero-content">
    <div class="hero-badge">✨ Open-source · AI · Cross-border ecommerce</div>
    <h1>AI-driven intelligence<br>for cross-border sellers</h1>
    <p>Open-source tools, MCP integrations, and supply-chain intelligence that turn Amazon and global marketplace data into decisions — not dashboards.</p>
    <div class="hero-cta">
      <a class="btn btn-primary" href="#posts">Read the latest</a>
      <a class="btn btn-secondary" href="https://github.com/Sorftime-AI-Supply-Chain" target="_blank">GitHub ↗</a>
    </div>
  </div>
</section>

<!-- WHAT WE BUILD -->
<section class="section">
  <div class="section-title">What we build</div>
  <h2>Tools, not slides</h2>
  <p class="section-sub">Every post in this archive is paired with a working open-source tool. You can clone it, run it, and use it on your own Amazon data.</p>

  <div class="grid grid-3">
    <div class="card">
      <div class="card-icon">🤖</div>
      <h3>AI agent workflows</h3>
      <p>Connect Claude Code, Codex, Cursor, or any MCP-compatible agent directly to marketplace data.</p>
    </div>
    <div class="card">
      <div class="card-icon">📊</div>
      <h3>Market intelligence</h3>
      <p>Hidden Profit Index, blue-ocean discovery, competitor intel, and PPC analysis — all API-driven.</p>
    </div>
    <div class="card">
      <div class="card-icon">⚙️</div>
      <h3>Operational automation</h3>
      <p>Inventory monitoring, daily dashboards, CLI-first multi-marketplace tooling.</p>
    </div>
  </div>
</section>

<!-- POSTS -->
<section class="section" id="posts">
  <div class="section-title">Articles</div>
  <h2>Latest from the lab</h2>
  <p class="section-sub">Short essays paired with working code. Skim, steal, ship.</p>

  <div class="posts">
    <div class="empty">
      📝 No posts yet — articles are being prepared.<br>
      Follow on <a href="https://github.com/Sorftime-AI-Supply-Chain" target="_blank">GitHub</a> to get notified.
    </div>
  </div>
</section>

<!-- CTA -->
<div class="cta-block">
  <h2>Build with us</h2>
  <p>Sorftime AI Supply Chain is open source. Star the repo, open an issue, or fork it for your own marketplace.</p>
  <div class="hero-cta">
    <a class="btn btn-primary" href="https://github.com/Sorftime-AI-Supply-Chain" target="_blank">View on GitHub ↗</a>
  </div>
</div>

<footer>
  © 2026 Sorftime AI Supply Chain · Powered by Jekyll + GitHub Pages
</footer>
