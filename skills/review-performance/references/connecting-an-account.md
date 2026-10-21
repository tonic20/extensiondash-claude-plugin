# Connecting an ExtensionDash account

The plugin uses the hosted server at `https://extensiondash.com/mcp`.
Sign in through Claude before using account tools. If a tool reports that
"this connection has no account", follow the steps below.

Never ask the user to paste a token, a connection URL, or a password into the
conversation. Signing in happens in their browser, on extensiondash.com. Give
them the steps below to do themselves.

## Claude (web, desktop, or Cowork)

1. Create an account at https://extensiondash.com/signup if needed.
2. Open the plugin's **Connectors** tab and connect ExtensionDash.
3. Choose **Sign in now** and **Register automatically** if authentication
   settings are shown. Sign in on extensiondash.com and click **Allow**.

Do not promise that a failed tracking call will automatically open sign-in in
Claude. This server uses a tool-level authentication error, which Claude does
not treat as the HTTP 401 needed for lazy authentication.

## Claude Code

For a plugin installation, open `/mcp`, select the plugin's ExtensionDash
server, and authenticate. Plugin server names are namespaced, so use the name
shown there.

## Disconnecting

Every app connected by signing in is listed under **Connected apps** on
https://extensiondash.com/profile, with a **Disconnect** button.
