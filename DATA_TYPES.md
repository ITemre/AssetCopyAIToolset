# Data types

These fields come from the plugin source. Query `describe_toolset` for the complete wire schema, including required fields and engine-reflected types.

## FAssetCopyEntry

```cpp
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
```

## FAssetCopyReport

```cpp
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
	 * Destination packages that exist but are not the same asset as their source (other class, or a file the asset
	 * registry does not know), and with patched headers existing packages where the engine would write a stray copy
	 * (see ExpectedStrayCopies). A real run raises and copies nothing while any exist.
	 */
	UPROPERTY()
	TArray<FString> Conflicts;

	/**
	 * With patched headers, the copies the engine would write on its own, below /Game at their source path, for
	 * dependencies that are kept rather than copied: "stray -> package it duplicates". A real run raises and copies
	 * nothing while any are expected, because repointing them breaks material function calls.
	 */
	UPROPERTY()
	TArray<FString> ExpectedStrayCopies;

	/**
	 * Sources the engine would copy on its own that are no reused or shared entry of the plan, such as dependencies
	 * of shared packages. They block the run as conflicts; a same-named package elsewhere is never assumed to match.
	 */
	UPROPERTY()
	TArray<FString> UnplannedStrayCopies;
```

