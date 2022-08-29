---
layout: default
parent: Single cell
title: Barcode Correction
nav_order: 6
---

## Barcode Correction Documentation

### Why Barcode Correction?

Single-cell, spatially-resolved, and other barcoded sequencing applications
rely on the accuracy of the cell or group barcode, which is typically chosen from a set of
known candidates, often referred to as a "whitelist".

This contrasts with the uniformly randomly-generated molecular barcodes (a.k.a. UMIs, "Unique molecular identifiers").

This tool uses the set of known candidates to correct sequencing errors in cell barcode identification. There are two primary benefits:

1. Increased yield
2. Improved accuracy in downstream deduplication.

By correcting errors in cell barcodes, the total number of usable reads is increased (typically ~5%).

And, once cell barcodes are corrected, the downstream groupdedup software tool can perform deduplication much more efficiently
than standard deduplication. This is because only reads sharing a cell barcode are compared, which dramatically reduces the search space compared to exhaustive pairwise comparisons.

### What does Barcode Correction do?

The tool takes a list of true barcodes and builds a locality-sensitive hashing (LSH) index over that set to facilitate fast nearest-neighbor queries.

This remaps reads with cell barcodes to their nearest-neighbors within the truth set.

In addition, "real cells" wll be marked with `rc` tag after this step, which will be used by `isoseq3 groupdedup`.

### When would a user call this tool?

Run this tool on barcode-tagged BAM files before deduplication (`isoseq3 groupdedup`).
This provides substantial runtime improvements compared to `isoseq3 dedup`.

## Usage

### (with barcode-set in barcodes.txt)
```
isoseq3 correct --barcodes barcodes.txt[.gz] input.bam output.bam
```

#### Tags
This requires the existance of XC and XU barcode tags.
The program will fail if either are missing.

We also add or update the following tags:


| Tag | Type | Short Name | Relevant Executable | Value |
| --- | ---- | ---------- | ----- | ----- |
|CR| string  | Cell Barcode  | `correct` | Raw (uncorrected) cell barcode |
|CB| string  | Cell Barcode  | `correct` | Corrected cell barcode |
|XC| string  | Cell Barcode | `tag`, `correct` | Raw cell barcode |
|nc| int     | Number of Candidates | `correct` | Number of candidate barcodes. |
|oc| string  | Other Choices | `correct` | String representation of other potential barcodes. |
|gp| int     | Group Passes | `correct` | Flag specifying whether or not the barcode for the given read passes filters. 1 for passing, 0 for failing. |
|nb| int     | Barcode Distance | `correct` | Edit distance from the barcode for the read to the barcode to which it was reassigned. This is 0 if the barcode matches exactly, -1 if the barcode could not be rescued, and the edit distance otherwise. |
|rc| int     | Real Cell | `correct` | Predicted real cell. This is 1 if a read is predicted to come from a real cell and 0 if predicted to be a non-real cell. |
