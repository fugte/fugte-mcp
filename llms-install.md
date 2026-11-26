# Installing the Fugte MCP server (for AI agents)

An install guide for an agent that can edit its own client's settings. The README has the
same steps for people.

## 1. Sign-in

None. The server has no authentication. Do not add an `Authorization` header, do not ask the
user for an API key, and do not put anything in the URL.

## 2. Configure the connection

- Server URL: `https://mcp.fugte.com/mcp` (exactly this, with `/mcp` at the end;
  `fugte.com/mcp`, `fugte.app/mcp` and the retired `api.fugte.app/mcp` are not the server)
- Transport: Streamable HTTP
- Authentication: none

Add it to the settings of the client you are running in, using its usual entry for a remote
HTTP MCP server. Typical shapes are in `README.md`. In Claude's own connector settings,
Authentication must be "No sign in": any other choice makes Claude try to register an OAuth
client, which fails with "Couldn't register with Fugte's sign-in service", and the setting
cannot be changed afterwards (remove the connector and add it again).

## 3. Verify the connection

List the server's tools. A working connection returns one tool, `save_widget`, titled "Save to
Fugte". Tell the user the connector is ready and that they can ask for a widget and to save it
to Fugte.

## 4. Using the tool

- Call `save_widget` when the user wants the widget on their site, or wants to keep changing it
  later. Send one complete, self-contained widget: `html` is required; `css`, `js` and `title`
  are optional.
- Give the user the `url` from the result. It opens the widget in the Fugte editor after they
  sign in, and it works for 7 days.
- Each call saves a new copy with a new link, so do not call it again for the same widget
  unless the user asks.
- The tool cannot read, list or change widgets already in the user's Fugte account. For those,
  send the user to https://fugte.app.

## 5. Common errors

- **The code imports a package, or uses `export` or `require`.** The tool returns an error that
  names the problem. Rewrite it as plain HTML, CSS and JavaScript (load a library from a full
  `https` link in a `<script>` tag if it needs one) and call the tool again.
- **The code is over 150,000 characters.** Load large libraries from links and keep images as
  links instead of embedding them, then call again.
- **The daily limit is reached.** The error says so. Tell the user to copy the code and paste
  it at https://fugte.app, which ends in the same editor.
- **The link says it has expired** (after 7 days). Save the widget again for a new link.
