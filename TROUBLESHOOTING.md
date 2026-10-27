# Asset Copy AI Toolset: troubleshooting

| Symptom | Check |
| --- | --- |
| Plugin is missing | Install the purchased plugin into the correct engine or project Plugins directory. |
| Module cannot load | Use UE 5.8 / Win64 and rebuild the source plugin with the supported toolchain. |
| Toolset is missing | Enable all declared plugins, restart, then inspect Python and plugin errors in the Output Log. |
| MCP cannot connect | Confirm the native server is running, its port, the client transport and loopback address. |
| An argument is rejected | Refresh `describe_toolset` and follow its runtime schema. |
| Assets or classes are missing | Supply the required project assets and enable the engine dependency that owns the class. |
| An operation succeeds but looks wrong | Inspect it in the editor and undo or restore your duplicate/backup before continuing. |

## Before filing an issue

Reproduce in a fresh test project, record the minimal operation sequence, and include the engine and plugin versions. Do not attach your entire paid project, credentials or third-party assets.

## First smoke check

Run CopyPackages with bDryRun=true, no referencer folders and bPatchHeaders=false. Inspect entries, conflicts and external references. Choose a new /Game destination, run the approved copy, then load the copied assets and compile affected materials.
