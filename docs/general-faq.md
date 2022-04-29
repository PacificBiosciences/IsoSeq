---
layout: default
title: General FAQ
nav_order: 5
---

# General FAQ
## BAM tags explained
Following BAM tags are being used:

 - `ib` Barcode summary: triplets delimited by semicolons, each triplet contains two barcode indices and the read counts, delimited by comma. Example: `0,1,20;0,3,5`
 - `ic` Number of reads used to generate consensus. If less than `is`, this means that reads were down-sampled when consensus-calling
 - `im` Read names associated with this isoform
 - `is` Number of reads associated with this isoform
 - `it` List of barcodes/UMIs clipped during `tag`
 - `iz` Maximum number of subreads used for polishing
 - `rq` Predicted accuracy for polished isoform
 - `XA` Order of `tag` names
 - `XC` Cell/group barcode sequence `tag`
 - `CB` Cell/group barcode sequence `tag`. This is an alias for XC, but its presence indicates that the barcode has been corrected
 - `CR` Raw cell/group barcode sequence `tag`
 - `XG` PacBio's `GGG` UMI suffix `tag`
 - `XM` UMI sequence `tag`
 - `XO` Overhang sequence `tag`
 - `nb` Edit distance between corrected cell/group barcode and raw cell/group barcode
 - `gp` Pass/fail for cell/group barcode correction using a truth-set. 1 for pass, 0 for fail
 - `nc` Number of known cell/group barcodes in the truth-set sharing the shortest distance from the raw barcode. If this number is > 1, this indicates ambiguity in remapping
 - `oc` Original known cell/group barcodes in the truth-set sharing the shortest distance from the raw barcode

 Quality values are capped at `93`.

## The binary does not work on my linux system!
Binaries require **SSE4.1 CPU support**; CPUs after 2008 (Penryn) include it.
