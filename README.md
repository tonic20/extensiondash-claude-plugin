# ExtensionDash: Store Rankings

![ExtensionDash](assets/logo.png)

Research Chrome Web Store extensions and Firefox add-ons, review your
extension's users and keyword rankings, and manage the keywords and competitors
your ExtensionDash account tracks.

## Install in Claude Code

Run these commands inside Claude Code:

```text
/plugin marketplace add tonic20/extensiondash-claude-plugin
/plugin install extensiondash@extensiondash
```

Then open `/mcp`, select the ExtensionDash server, and authenticate.

## Install in Claude web, desktop, or Cowork

Where plugin upload is available, download this repository as a ZIP from
GitHub, then open **Customize → Plugins → Add → Upload plugin** and select
the ZIP. Connect ExtensionDash on the plugin's **Connectors** tab.
Organization settings may control plugin access.

This repository is available for direct installation. It is not yet listed
in Anthropic's public directory.

## What is included

- **research-extensions:** search the catalog and compare recorded public
  listings, including users, ratings, languages, and listing copy.
- **review-performance:** compare dated measurements and keyword positions,
  with scan depth, collection gaps, and rounded counts explained.
- **manage-tracking:** add or remove the keywords and competitors you request.
  Stopping tracking preserves recorded history.

## Connect your account

1. Create a free account at https://extensiondash.com/signup if needed.
2. Track an extension on the ExtensionDash website and add its keywords.
3. In Claude, open this plugin's **Connectors** tab and connect ExtensionDash.
   Choose **Sign in now** and **Register automatically** if Claude asks for
   authentication settings. Sign in on extensiondash.com and allow the connection.

The remote server is `https://extensiondash.com/mcp`. The Claude directory
connector uses OAuth at connection time. Do not choose “Sign in when needed”:
this server's tool-level authentication errors do not trigger Claude's lazy
sign-in flow.

In Claude Code, load or install the plugin, open `/mcp`, select its ExtensionDash
server, and authenticate. Claude Code namespaces plugin server names; select
the entry shown there rather than assuming a standalone server name.

Never paste a password or API token into a conversation or into this package.
Disconnect the app at https://extensiondash.com/profile to revoke access.

## Try it

- “Find Chrome extensions for tab management with over 100,000 users.”
- “How did my extension perform over the last seven days?”
- “Which competitors are ahead on my tracked keywords?”
- “Track the keyword ‘reverse image search’ in English for my extension.”

The Free plan has tracking limits; check https://extensiondash.com/pricing for
the current limits. No store credentials or extension source code are needed.
An account with existing observations is needed for a historical comparison.
New tracking may remain pending until its first scan.

## Data and permissions

This plugin contains Markdown skills and JSON configuration. It runs no local
hooks or executables. Claude sends tool arguments to ExtensionDash's hosted MCP
server. Account tools return your tracked extensions, measurements, keywords,
and competitors. Catalog tools read ExtensionDash's recorded public store data.
Only the four add/remove keyword and competitor tools change saved tracking.
The plugin cannot install browser extensions or delete recorded history.

Claude may ask for confirmation before tracking changes. Store observations
can be stale, incomplete, or rounded. Firefox daily-average users and Chrome
store users are different measurements and should not be added together.

The files in this plugin bundle are licensed under the MIT License, included
in `LICENSE`. Access to the hosted service is governed separately by its terms.

- Website: https://extensiondash.com/
- Documentation and support: https://extensiondash.com/support
- Support email: support@extensiondash.com
- Privacy: https://extensiondash.com/privacy
- Terms: https://extensiondash.com/terms

## Author

Built by [Anton Kopylov](https://www.kopylov.net). The story behind it: [ExtensionDash: Tracking an Extension From the Agent](https://www.kopylov.net/blog/extensiondash-tracking-an-extension-from-the-agent.html).
