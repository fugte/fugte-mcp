# Fugte MCP server

This repository holds the documentation and the registry listing (`server.json`) for
Fugte's MCP server. The server is hosted by Fugte; there is nothing to build or run here.

[Fugte](https://fugte.com) hosts embeddable widgets for sites built with no-code tools such as
Canva, Shopify, Wix, Squarespace, Notion and WordPress, and keeps them editable by someone who
cannot code.

The MCP server lets an AI chat save the widget it wrote to Fugte. The chat gives you a link.
Open it and sign in, and the widget is in the Fugte editor with its text, links, colors and
sizes as fields you can change without code. Publish it, and paste the embed into your site.

**Server URL:** `https://mcp.fugte.com/mcp` (Streamable HTTP, no sign-in)

A guide for people rather than developers: https://fugte.com/ai-chat/

## Connect

Use the address exactly as shown, including `/mcp` at the end. Neither `fugte.com/mcp` nor
`fugte.app/mcp` is the server, and the earlier `api.fugte.app/mcp` was retired on 2026-11-26.

### Claude (claude.ai and the desktop app)

1. Open **Customize**, then **Connectors**, and choose **Add**, then **Add custom connector**.
2. Name it Fugte and paste `https://mcp.fugte.com/mcp` as the **MCP server URL**.
3. Set **Authentication** to **No sign in**, and add it.
4. In a chat, press **+**, open **Connectors** and switch Fugte on.

Claude's Free plan allows one custom connector. If Claude says it couldn't register with
Fugte's sign-in service, the address or the Authentication setting is wrong. Authentication
cannot be changed after the connector is added, so remove it and add it again.

Tested end to end on 2026-11-24.

### ChatGPT

1. Go to **chatgpt.com/plugins** and press **+**.
2. Choose **Add custom MCP server** and name it Fugte.
3. Paste `https://mcp.fugte.com/mcp` as the **Server URL** and set **Authentication** to
   **No authentication**.
4. Confirm the notice, choose **Create as a plugin**, and install it.
5. In a chat, type **@** and pick Fugte.

ChatGPT runs custom MCP servers on the web only. Free accounts cannot add one, and OpenAI lists
full support, including tools that save something, on Business and Enterprise or Edu plans.
ChatGPT asks you to confirm before a save. Not yet tried with Fugte.

### Claude Code, Cursor, VS Code and other clients

These are each client's usual setup for a remote server with no sign-in. They have not been
tried with Fugte yet.

Claude Code:

```bash
claude mcp add --transport http fugte https://mcp.fugte.com/mcp
```

Cursor (`.cursor/mcp.json`):

```json
{
  "mcpServers": {
    "fugte": {
      "url": "https://mcp.fugte.com/mcp"
    }
  }
}
```

VS Code (`.vscode/mcp.json`):

```json
{
  "servers": {
    "fugte": {
      "type": "http",
      "url": "https://mcp.fugte.com/mcp"
    }
  }
}
```

Any other client that connects to a remote MCP server over Streamable HTTP, with no
authentication, can use the same address.

## The tool: `save_widget`

Shown as **Save to Fugte**. It saves one widget and returns a link.

| Argument | Type | Required | What it is |
|---|---|---|---|
| `html` | string | yes | The widget's HTML: a full page or a fragment. It may already hold its `<style>` and `<script>` tags. |
| `css` | string | no | CSS that is not already in a `<style>` tag in the HTML. |
| `js` | string | no | Plain browser JavaScript that is not already in a `<script>` tag. No modules. |
| `title` | string | no | A short name, up to 80 characters, such as "Music player" or "RSVP form". |

It returns `url`, the link to give the user, and `expiresAt`, when the link stops working
(ISO 8601).

Annotations: `readOnlyHint: false`, `destructiveHint: false`, `idempotentHint: false`,
`openWorldHint: false`. Each call saves a new copy and returns a new link.

### Things to ask for

- "Make a countdown to our grand opening on March 3 at 10 AM, in navy and gold. Save it to Fugte."
- "Build an FAQ accordion from these questions and answers, with the first one open. Save it to Fugte."
- "Write a restaurant menu with starters, mains and desserts, prices on the right. Save it to Fugte."

### What the code must be

- One self-contained widget in plain HTML, CSS and JavaScript, with no build step.
- No `export` or `require`, and no `import` of a package by name. Libraries, images and fonts
  can load from full `https` links.
- Up to 150,000 characters with the HTML, CSS and JavaScript put together.

Code that breaks a rule is not saved. The tool returns an error that says what to change, so
the chat can fix it and save again.

### What happens to a saved widget

- It waits behind the link the chat gives you, and nothing is published until you publish it
  in Fugte.
- The link works for 7 days. After that the saved code is deleted, and the chat can save the
  widget again.
- Opening the link needs a Fugte account, which can be created then. The widget becomes a draft
  in that account. Text, links, colors and sizes in the code become fields in the editor, and
  repeated parts, such as cards or questions, usually become blocks. If the code already has a
  Fugte schema, its fields are used as written.
- Saving uses none of your Fugte AI credits, because the chat writes the code.
- The connector cannot see, list or change the widgets already in your Fugte account.

Fugte takes a set number of saves from all AI chats together each day. When that is reached,
the tool says so and suggests pasting the code at https://fugte.app instead, which ends in the
same editor.

## Privacy

The server receives the widget's code and title when the chat saves, never the conversation,
and it cannot read your Fugte account. It keeps them with the time of the save and the name the
chat app gives for itself. When someone signs in and opens the link, Fugte records which account
opened it and when. The code is deleted after 7 days; the record of the save is kept to count
how the connector is used.

Everything a chat sends or receives can also be seen by your AI provider as part of your
conversation.

Full policy: https://fugte.com/privacy/

## Support

hello@fugte.com, or https://fugte.com/support/

Fugte is made by Made4Uo LLC.
