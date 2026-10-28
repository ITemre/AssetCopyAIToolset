# Asset Copy AI Toolset: agent guide

## Copy this setup prompt to your assistant

```text
Please set up the "Asset Copy AI Toolset" Unreal Engine plugin for me and connect yourself to it.

1. Find my Unreal project (.uproject). Ask me to select it if there are several. It must use
   Unreal Engine 5.8 on Windows. Preserve unsaved editor work before editing settings or restarting.
2. Check that the purchased plugin is installed: either in the engine under
   Engine/Plugins/Marketplace or as <Project>/Plugins/AssetCopyToolset. If it is missing, stop and
   tell me to install it from Fab first.
3. Enable these plugins in the .uproject "Plugins" list (add entries with "Enabled": true and
   preserve all other settings): AssetCopyToolset, ToolsetRegistry, ModelContextProtocol.
4. Start the editor's native MCP server with -ModelContextProtocolStartServer, or enable auto start
   in Project Settings > Plugins > Model Context Protocol and restart safely. Confirm the server's
   configured port. The default endpoint is http://127.0.0.1:8000/mcp. Verify it belongs to this project.
5. Add that endpoint to your own MCP configuration as an HTTP (Streamable HTTP) server named
   "unreal", then reload your MCP connection if needed. Preserve existing server entries; use a
   different name if "unreal" already points to another project. Keep the endpoint on loopback.
6. Call list_toolsets and confirm every identifier below is listed. Call describe_toolset for each:
   AssetCopyToolset.AssetCopyToolset
7. Read the agent guide at
   https://github.com/ITemre/AssetCopyAIToolset/blob/main/AI_AGENT_HANDOFF.md
   (also shipped as Documentation/AI_AGENT_HANDOFF.md) and follow the tool reference and project rules.
8. Do not perform a real copy or save assets during setup. Use only inspection or a dry run until I
   request a copy.
   Tell me in two sentences which project and endpoint are connected, whether all required toolsets
   are available, and what I can ask you to do next.
```

## Working rules

- Inspect before changing content. Read `describe_toolset` rather than guessing arguments.
- Work on duplicates when changing meshes, bones, weight data or package references.
- Stop PIE before editor asset mutations; PIE probing is read-only unless explicitly documented.
- Check dirty levels before any workflow that opens or saves another level.
- Save only the intended assets. A tool success is not visual or gameplay acceptance.
- Keep asset paths and file paths distinct. `/Game/...` is a package path, not a Windows filename.
- Request operation-specific approval from your user for destructive changes or redistribution of assets.

## Product-specific constraints

- Copying assets does not grant redistribution rights. Check the source asset licence before shipping or reselling any copied content.
- Copy and reference-repointing operations save files and are not guaranteed to be reversible with Ctrl+Z. Keep a project backup.
- Header-patching mode is an advanced workflow. Review conflicts, external references and stray-copy reports before a real run.
