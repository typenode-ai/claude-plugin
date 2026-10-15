# Typenode for Claude

Build, validate, publish and debug workflow automations and custom MCP servers in your Typenode workspace from Claude.

## What it connects to

This Plugin connects Claude to the Typenode MCP connector at `https://mcp.typenode.ai/plugins/claude/mcp`. It uses the same address as the Typenode connector in Claude's directory, so with both you see one set of tools. You sign in with OAuth using your Typenode account and approve access to one workspace on Typenode's consent screen. You need a Typenode account with access to a workspace; integrating an app also needs that app's API access and credentials.

## Workflow code and credentials

This repository contains Claude instructions and a remote MCP configuration. JavaScript examples in the skills are workflow step code authored through Typenode's tools and executed in the workflow runtime. In those examples, `secrets.CRM` means a credential configured for the selected Typenode workspace and environment through Typenode's browser setup; it is not a credential read from the machine running Claude. `api.example.com` is a placeholder for the provider selected by the user. Resolve the real provider URL and its authentication requirements before authoring an action.

## Skills

- **Get started**: finds your connected workspace and plans the next step.
- **Build workflows and MCP tools**: turns API calls and business rules into validated workflows.
- **Configure integrations and access**: sets up connections, MCP servers and publication. Secrets and account connections are completed on Typenode's own pages in the browser, never in chat.
- **Debug workflow runs**: finds the cause of failed or waiting runs from their recorded steps and outputs.

## Privacy, support and docs

- How Typenode handles data: [Privacy policy](https://typenode.ai/legal/privacy-policy)
- Terms: [Terms of service](https://typenode.ai/legal/terms-of-service)
- Support: [support@typenode.ai](mailto:support@typenode.ai) or [typenode.ai/support](https://typenode.ai/support)
- Docs: [Connect Claude to Typenode](https://docs.typenode.ai/connect/claude)

## License

MIT
