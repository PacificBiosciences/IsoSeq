---
layout: default
parent: Classification
title: Pigeon Input
nav_order: 4
---

## Pigeon Input

| Input | Tool | Description |
| ----- | ---- | ----------- |
| Transcript GFF | _sort_ | Collapsed transcript GFF from _isoseq collapse_ |
| Reference Annotation | _sort_ | Reference annotation in GTF format |
| CAGE Peaks (Optional) | _sort_ | CAGE peaks in BED format |
| Intropolis data (Optional) | _sort_ | Intropolis data in custom format |
| Sorted Reference Annotations | _index_ | Sorted genome annotation GTF, CAGE peak BED, Intropolis TSV |
| Reference Genome | _classify_ | Reference genome in FASTA format |
| Reference Annotation | _classify_ | Sorted and indexed reference annotation in GTF format |
| FL Counts (Optional) | _classify_ | FL count info from _isoseq collapse_ (`*.abundance.txt`) |
| CAGE Peaks (Optional) | _classify_ | Sorted and indexed CAGE peaks in BED format |
| polyA motif list (Optional) | _classify_ | polyA motif list in custom format |
| Intropolis data (Optional) | _classify_ | Sorted and indexed Intropolis data in custom format |
| Molecules FASTA | _make-seurat_ | Molecules FASTA file from _isoseq dedup_ |
| Groups txt file | _make-seurat_ | Groups text file from _isoseq collapse_ |

Sorted and indexed references for human and mouse can be found [here](https://downloads.pacbcloud.com/public/dataset/MAS-Seq/REF-pigeon_ref_sets/).