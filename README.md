<p align="center">
  <img src="https://www.relvato.com/logo-512.png" alt="Relvato" width="96" height="96">
</p>

<h1 align="center">Relvato for DeepSeek Harness</h1>

<p align="center">
  Website monitoring your DeepSeek Harness agent can run.<br>
  <a href="https://www.relvato.com/developers">Developer docs</a> ·
  <a href="https://github.com/Relvato/relvato-mcp">MCP server</a> ·
  <a href="https://www.relvato.com">relvato.com</a>
</p>

---

[Relvato](https://www.relvato.com) monitors websites in a real browser: sign-ups, logins, checkout, page speed, search
visibility and security. It runs those monitors on a schedule and again whenever the site changes, works on any
website, and goes deepest on WordPress and WooCommerce.

This is a [DeepSeek Harness](https://github.com/deepseek-ai/deepseek-harness) **bundle**. It adds Relvato's hosted MCP
server (52 tools) to your profile through dsh's in-box MCP client. It is configuration only: no code from this package
runs in dsh, and there is no build step.

With it, your agent can add a site and set up its monitors, run them, read health, uptime, Core Web Vitals and each
run's findings, explain a failure with Relvato's fix brief, review results (with undo) and, on WordPress, apply the one
fix a run proposes, with automatic rollback.

## Install

1. Create a Relvato API key at [app.relvato.com/api-access](https://app.relvato.com/api-access) (there's a free plan).
   **Full access** lets the agent set up and run monitors; **read-only** only lets it read.
2. Make the key available to dsh as `RELVATO_API_KEY`: in your shell environment, or in `$DSH_HOME/.env`
   (default `~/.dsh/.env`):

   ```sh
   RELVATO_API_KEY=rlv_your_key
   ```

   Keep it out of files you commit.
3. Add the bundle to a profile:

   ```sh
   dsh plugin --profile web add dsh-plugin-relvato
   ```

   It's on [npm](https://www.npmjs.com/package/dsh-plugin-relvato) (and its mirrors, such as npmmirror). To install
   straight from GitHub instead: `dsh plugin --profile web add github:Relvato/dsh-plugin-relvato`. You can also add it
   from the **Plugins** page in the dsh Web UI.

4. Restart dsh (or let HMR reload it). Relvato's tools appear as `mcp__relvato__<tool>`, for example
   `mcp__relvato__list_sites`.

Check that the layer is there without booting:

```sh
dsh --profile web --dump-config   # shows a "# == dsh-plugin-relvato" layer with the relvato MCP client
```

To remove it: `dsh plugin --profile web remove dsh-plugin-relvato`.

## What it adds

One row, `relvato`, for `@deepseek-ai/dsh-mcp-client` ([cordis.patch.yml](cordis.patch.yml)):

| Setting | Value |
| --- | --- |
| `serverName` | `relvato` |
| `transport` | `streamable-http` |
| `url` | `https://app.relvato.com/api/mcp` |
| `headers` | `Authorization: Bearer $RELVATO_API_KEY`, only when the variable is set |

Without `RELVATO_API_KEY`, dsh still connects and lists Relvato's tools, but every call is refused until you add a key.
To change a setting, override the `relvato` row in your profile's `cordis.patch.yml`, restating every key it needs.

dsh's MCP client sends headers and doesn't support OAuth sign-in, so this bundle uses an API key. In clients that do
(Claude, ChatGPT, VS Code), you can sign in instead: see [Relvato/relvato-mcp](https://github.com/Relvato/relvato-mcp).

## Things to ask

- "Set up monitoring for example.com and tell me what I need to do to connect it."
- "Run all the monitors on my site and tell me what broke." (A full run takes several minutes; the agent is told how long.)
- "Why did checkout fail on my shop last night, and how do I fix it?"
- "How fast is my site for real visitors, and what's slowing it down?"

The agent works under the same rules as the dashboard: it can't skip proving you own a site, approve file-integrity,
script or DNS changes, change who receives alerts, delete anything, or see secrets. Tools that change your site are
marked destructive.

## Support

- Questions and bug reports: [relvato.com/contact](https://www.relvato.com/contact), or open an issue here.
- Service status: [relvato.com/status](https://www.relvato.com/status).

This package is MIT-licensed (see [LICENSE](LICENSE)). The Relvato service is proprietary and covered by its
[terms](https://www.relvato.com/terms).
