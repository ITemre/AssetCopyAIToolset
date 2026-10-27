# Runtime MCP schemas

Captured from `describe_toolset` in the native Unreal Engine 5.8.4 MCP server. These are documentation examples, not plugin source files. Refresh the schema from your own editor before automation.

## AssetCopyToolset.AssetCopyToolset

```json
{
  "name": "AssetCopyToolset.AssetCopyToolset",
  "version": "1.0",
  "description": "Copies content, including everything it depends on, into a project folder and points the references between\nthe copies at each other. Also works for content that Advanced Copy in the Content Browser refuses, such as the\ncontent of engine plugins.",
  "tools": [
    {
      "description": "Rebuilds the inputs and outputs of every material function call in the materials and material functions below\nthe folders from the functions they call, then recompiles and saves what changed. Repairs copies whose function\ncalls still point at the inputs of the original functions (\"Missing function input\" when compiling).",
      "name": "AssetCopyToolset.AssetCopyToolset.RefreshMaterialFunctionCalls",
      "inputSchema": {
        "type": "object",
        "properties": {
          "folders": {
            "type": "array",
            "items": {
              "type": "string"
            },
            "description": "Project folders, without a trailing slash."
          }
        },
        "required": [
          "folders"
        ]
      },
      "outputSchema": {
        "type": "object",
        "properties": {
          "returnValue": {
            "type": "array",
            "items": {
              "type": "string"
            },
            "description": "The packages that changed."
          }
        },
        "required": [
          "returnValue"
        ]
      }
    },
    {
      "description": "Copies packages and the packages they depend on, keeping their folder layout below the content root of the\nsources. A destination package that exists already is kept and used instead of a new copy, so several runs\ncan share content. Originals are never changed or deleted.",
      "name": "AssetCopyToolset.AssetCopyToolset.CopyPackages",
      "inputSchema": {
        "type": "object",
        "properties": {
          "sources": {
            "type": "array",
            "items": {
              "type": "string"
            },
            "description": "Packages or folders, for example \"/MetaHumanCharacter/Lookdev_UHM/Teeth\". All must share one content root."
          },
          "destinationFolder": {
            "type": "string",
            "description": "Project folder that takes the place of the content root, for example \"/Game/EpicContent/MetaHumans/Common\". No trailing slash."
          },
          "bDryRun": {
            "type": "boolean",
            "description": "Only report what would be copied."
          },
          "referencerFolders": {
            "type": "array",
            "items": {
              "type": "string"
            },
            "description": "Project folders whose assets get their references to the sources replaced by the copies, then saved. Empty for none."
          },
          "bPatchHeaders": {
            "type": "boolean",
            "description": "Copy the package files and rewrite their references instead of loading and duplicating the objects. Keeps every object of the assets exactly as it is, which duplicating does not for material functions. The engine would also write kept dependencies below /Game at their source path; such a copy is refused, see ExpectedStrayCopies."
          },
          "sharedFolder": {
            "type": "string",
            "description": "Project folder that already holds copies of some of the content, for example \"/Game/EpicContent/MetaHumans/Common\". Unused when SharedPrefixes is empty."
          },
          "sharedPrefixes": {
            "type": "array",
            "items": {
              "type": "string"
            },
            "description": "Paths below the content root, for example \"/Face\". Packages below them are not copied: the same package in SharedFolder is used instead, nothing it depends on is followed, and references to them are pointed at SharedFolder. Raises when such a package is missing there."
          }
        },
        "required": [
          "sources",
          "destinationFolder",
          "bDryRun",
          "referencerFolders",
          "bPatchHeaders",
          "sharedFolder",
          "sharedPrefixes"
        ]
      },
      "outputSchema": {
        "type": "object",
        "properties": {
          "returnValue": {
            "title": "AssetCopyReport",
            "type": "object",
            "properties": {
              "entries": {
                "type": "array",
                "items": {
                  "title": "AssetCopyEntry",
                  "type": "object",
                  "properties": {
                    "source": {
                      "type": "string",
                      "description": "Package the copy is made from, for example /MetaHumanCharacter/Materials/M_Hide."
                    },
                    "destination": {
                      "type": "string",
                      "description": "Package the copy is written to."
                    },
                    "bReused": {
                      "type": "boolean",
                      "description": "The destination package existed already; it is kept and nothing is copied for this entry."
                    },
                    "bShared": {
                      "type": "boolean",
                      "description": "The package matches a shared prefix: Destination lies in the shared folder and its dependencies are not followed."
                    }
                  },
                  "required": [
                    "source",
                    "destination",
                    "bReused",
                    "bShared"
                  ]
                },
                "description": "The packages that are copied or reused, sources first and then the dependencies found from them."
              },
              "externalReferences": {
                "type": "array",
                "items": {
                  "type": "string"
                },
                "description": "Packages referenced by the copied ones that stay where they are: other content roots such as /Engine."
              },
              "bCopied": {
                "type": "boolean",
                "description": "The copy was made. False for a dry run."
              },
              "repointedReferencers": {
                "type": "array",
                "items": {
                  "type": "string"
                },
                "description": "Assets of ReferencerFolders whose references were pointed at the copies."
              },
              "conflicts": {
                "type": "array",
                "items": {
                  "type": "string"
                },
                "description": "Destination packages that exist but are not the same asset as their source (other class, or a file the asset\nregistry does not know), and with patched headers existing packages where the engine would write a stray copy\n(see ExpectedStrayCopies). A real run raises and copies nothing while any exist."
              },
              "expectedStrayCopies": {
                "type": "array",
                "items": {
                  "type": "string"
                },
                "description": "With patched headers, the copies the engine would write on its own, below /Game at their source path, for\ndependencies that are kept rather than copied: \"stray -> package it duplicates\". A real run raises and copies\nnothing while any are expected, because repointing them breaks material function calls."
              },
              "unplannedStrayCopies": {
                "type": "array",
                "items": {
                  "type": "string"
                },
                "description": "Sources the engine would copy on its own that are no reused or shared entry of the plan, such as dependencies\nof shared packages. They block the run as conflicts; a same-named package elsewhere is never assumed to match."
              }
            },
            "required": [
              "entries",
              "externalReferences",
              "bCopied",
              "repointedReferencers",
              "conflicts",
              "expectedStrayCopies",
              "unplannedStrayCopies"
            ]
          }
        },
        "required": [
          "returnValue"
        ]
      }
    }
  ]
}
```

