# Set up an MCP server

1. Establish the tool's intended inputs, outputs, business rules and stopping point. Inspect existing workflows and MCP servers before creating another. Use the graph-authoring guidance (the native Build skill in the plugin) to build and validate the workflow.
2. For a workflow tool, define the reserved input step's `fields.input_json_schema` as a valid object-root schema. Its sole trigger must be a webhook with `response_mode:"sync"` and `auth:{kind:"access_key"}`. Other trigger or authentication combinations do not contribute a workflow tool to the server's catalog.
3. Complete authorized configuration and publication in the exact environment using the trigger/publication guidance. Observe the terminal publication operation. Binding a draft alone does not make a callable tool.
4. Create or reuse the designated MCP server and workflow binding. Preserve exact server and workflow IDs and choose a clear `tool_name` and truthful `tool_description`. Both the server and binding must be enabled. Read back with `get_mcp_server(include:["bindings"])` and match the intended source and tool name.
5. Use the returned `endpoint_url_template` for the chosen environment. The person adds that URL as a separate MCP connector in their AI client and signs in with OAuth. Those tools do not appear on the Typenode management connection. Verify refreshed discovery when that client is available; report connection verification as pending otherwise.

The caller needs effective `workflow.invoke` access to the bound workflow in the selected environment. A server or binding does not grant that access. Use access-management guidance for a requested access change. Discovery and a successful binding receipt are separate from an authorized invocation and a verified business result; report only the stages actually observed.

Existing skill IDs can also be bound when available and authorized. The Claude plugin cannot create or list customer-authored skills; it omits those management tools. Binding an existing skill is not installation of a native plugin skill.

When configuring connections, credentials, or consequential actions, read [handoffs](handoffs.md).

When a tool fails or an operation's outcome is uncertain, read [repair](repair.md).
