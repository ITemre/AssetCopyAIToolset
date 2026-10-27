# Asset Copy AI Toolset: tool reference

Operation names and descriptions below are taken from the selected plugin source. Query `describe_toolset` for the exact runtime schema, required fields and defaults. C++ metadata can alter wire names; use the runtime schema. Python toolset names retain their module and class names.

## AssetCopyToolset

### `CopyPackages`

```text
static FAssetCopyReport CopyPackages(const TArray<FString>& Sources, const FString& DestinationFolder, bool bDryRun, const TArray<FString>& ReferencerFolders, bool bPatchHeaders, const FString& SharedFolder, const TArray<FString>& SharedPrefixes)
```

One package of a copy, with the package it is copied from. */
USTRUCT(BlueprintType)
struct FAssetCopyEntry
{
	GENERATED_BODY()

	/** Package the copy is made from, for example /MetaHumanCharacter/Materials/M_Hide. */
	UPROPERTY()
	FString Source;

	/** Package the copy is written to. */
	UPROPERTY()
	FString Destination;

	/** The destination package existed already; it is kept and nothing is copied for this entry. */
	UPROPERTY()
	bool bReused = false;

	/** The package matches a shared prefix: Destination lies in the shared folder and its dependencies are not followed. */
	UPROPERTY()
	bool bShared = false;
};

/** What CopyPackages copies, or would copy. */
USTRUCT(BlueprintType)
struct FAssetCopyReport
{
	GENERATED_BODY()

	/** The packages that are copied or reused, sources first and then the dependencies found from them. */
	UPROPERTY()
	TArray<FAssetCopyEntry> Entries;

	/** Packages referenced by the copied ones that stay where they are: other content roots such as /Engine. */
	UPROPERTY()
	TArray<FString> ExternalReferences;

	/** The copy was made. False for a dry run. */
	UPROPERTY()
	bool bCopied = false;

	/** Assets of ReferencerFolders whose references were pointed at the copies. */
	UPROPERTY()
	TArray<FString> RepointedReferencers;

	/**
Destination packages that exist but are not the same asset as their source (other class, or a file the asset
registry does not know), and with patched headers existing packages where the engine would write a stray copy
(see ExpectedStrayCopies). A real run raises and copies nothing while any exist.
/
	UPROPERTY()
	TArray<FString> Conflicts;

	/**
With patched headers, the copies the engine would write on its own, below /Game at their source path, for
dependencies that are kept rather than copied: "stray -> package it duplicates". A real run raises and copies
nothing while any are expected, because repointing them breaks material function calls.
/
	UPROPERTY()
	TArray<FString> ExpectedStrayCopies;

	/**
Sources the engine would copy on its own that are no reused or shared entry of the plan, such as dependencies
of shared packages. They block the run as conflicts; a same-named package elsewhere is never assumed to match.
/
	UPROPERTY()
	TArray<FString> UnplannedStrayCopies;
};

/**
Copies content, including everything it depends on, into a project folder and points the references between
the copies at each other. Also works for content that Advanced Copy in the Content Browser refuses, such as the
content of engine plugins.
/
UCLASS(BlueprintType)
class UAssetCopyToolset : public UToolsetDefinition
{
	GENERATED_BODY()

public:
	/**
Copies packages and the packages they depend on, keeping their folder layout below the content root of the
sources. A destination package that exists already is kept and used instead of a new copy, so several runs
can share content. Originals are never changed or deleted.
@param Sources Packages or folders, for example "/MetaHumanCharacter/Lookdev_UHM/Teeth". All must share one content root.
@param DestinationFolder Project folder that takes the place of the content root, for example "/Game/EpicContent/MetaHumans/Common". No trailing slash.
@param bDryRun Only report what would be copied.
@param ReferencerFolders Project folders whose assets get their references to the sources replaced by the copies, then saved. Empty for none.
@param bPatchHeaders Copy the package files and rewrite their references instead of loading and duplicating the objects. Keeps every object of the assets exactly as it is, which duplicating does not for material functions. The engine would also write kept dependencies below /Game at their source path; such a copy is refused, see ExpectedStrayCopies.
@param SharedFolder Project folder that already holds copies of some of the content, for example "/Game/EpicContent/MetaHumans/Common". Unused when SharedPrefixes is empty.
@param SharedPrefixes Paths below the content root, for example "/Face". Packages below them are not copied: the same package in SharedFolder is used instead, nothing it depends on is followed, and references to them are pointed at SharedFolder. Raises when such a package is missing there.

### `RefreshMaterialFunctionCalls`

```text
static TArray<FString> RefreshMaterialFunctionCalls(const TArray<FString>& Folders)
```

Rebuilds the inputs and outputs of every material function call in the materials and material functions below
the folders from the functions they call, then recompiles and saves what changed. Repairs copies whose function
calls still point at the inputs of the original functions ("Missing function input" when compiling).
@param Folders Project folders, without a trailing slash.
@return The packages that changed.
