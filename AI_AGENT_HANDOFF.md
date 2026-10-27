# Asset Copy AI Toolset: agent guide

## Copy this setup prompt to your assistant

```text
Set up the purchased Asset Copy AI Toolset plugin for my Unreal Engine 5.8 project.
Find the .uproject and ask me to select it if there are several. Verify the plugin
is already installed through Fab or in Project/Plugins/AssetCopyToolset.
Enable the plugin and its declared dependencies without replacing other settings.
Enable ModelContextProtocol, restart when necessary, and connect to the editor's
native Streamable HTTP MCP server on its configured loopback port.
Run list_toolsets and describe_toolset; verify these identifiers:
AssetCopyToolset.AssetCopyToolset
Read https://github.com/ITemre/AssetCopyAIToolset/blob/main/README.md and the tool reference.
Start with read-only inspection in a disposable project and show me the first result.
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
