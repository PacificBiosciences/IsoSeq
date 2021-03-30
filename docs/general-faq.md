---
layout: default
title: General FAQ
nav_order: 5
---

# General FAQ
## BAM tags explained
Following BAM tags are being used:

 - `ib` Barcode summary: triplets delimited by semicolons, each triplet contains two barcode indices and the ZMW counts, delimited by comma. Example: `0,1,20;0,3,5`
 - `ic` Sum of number of passes from all ZMWs used to create consensus
 - `im` ZMW names associated with this isoform
 - `is` Number of ZMWs associated with this isoform
 - `it` List of barcodes/UMIs clipped during `tag`
 - `iz` Maximum number of subreads used for polishing
 - `rq` Predicted accuracy for polished isoform
 - `XA` Order of `tag` names
 - `XC` barcode sequence `tag`
 - `XG` PacBio's `GGG` UMI suffix `tag`
 - `XM` UMI sequence `tag`
 - `XO` overhang sequence `tag`

 Quality values are capped at `93`.

## The binary does not work on my linux system!
Binaries require **SSE4.1 CPU support**; CPUs after 2008 (Penryn) include it.
