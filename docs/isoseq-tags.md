---
layout: default
title: BAM Tags
nav_order: 8
---

#### Iso-seq Tags

| Tag | Type | Short Name | Relevant Executable | Value |
| --- | ---- | ---------- | ----- | ----- |
|CR| string  | Cell Barcode  | `correct` | Raw (uncorrected) cell barcode |
|CB| string  | Cell Barcode  | `correct` | Corrected cell barcode |
|UR| string  | UMI | None currently | Raw UMI |
|UB| string  | UMI | None currently | Corrected UMI |
|XM| string  | UMI | `tag`, `correct` | Raw (after `tag`) or corrected (after `correct`) UMI |
|XC| string  | Cell Barcode | `tag`, `correct` | Raw cell barcode |
|XA| string  | tag name order| `tag`, `correct` | Order of tags names. |
|nc| int     | Number of Candidates | `correct` | Number of candidate barcodes. |
|oc| string  | Other Choices | `correct` | String representation of other potential barcodes. |
|gp| int     | Group Passes | `correct` | Flag specifying whether or not the barcode for the given read passes filters. 1 for passing, 0 for failing. |
|nb| int     | Barcode Distance | `correct` | Edit distance from the barcode for the read to the barcode to which it was reassigned. This is 0 if the barcode matches exactly, -1 if the barcode could not be rescued, and the edit distance otherwise. |
|ic| int     | input-consensus | `dedup`, `groupdedup` | Number of reads used to generate consensus. If less than `is`, this means that reads were down-sampled when consensus-calling. |
|is| int     | input-sequences | `dedup`, `groupdedup` | Number of reads associated with isoform. |
|XO| string  | X Overhang | `tag` |  Overhang sequence tag. | 
|XG| string  | X GGG      | `tag` | PacBio's GGG UMI suffix tag |
|rq | float | read quality | | Predicted accuracy for polished isoform |
|iz | int   | maximum subreads used | | maximum number of subreads used for polishing |
|it | string | trimmed | `tag` | List of barcodes/UMIs clipped during tag |
|im | string | names | `dedup`, `groupdedup` | List of names of input reads used in generating consensus |

<img src="../doc/img/isoseq.png"/>
