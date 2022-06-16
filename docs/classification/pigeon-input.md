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
| Reference Annotation | _index_ | Reference annotation in GTF format |
| Reference Genome | _classify_ | Reference genome in FASTA format |
| FL Counts (Optional) | _classify_ | FL count info from _isoseq collapse_ (`*.abundance.txt`) |
| CAGE Peaks (Optional) | _classify_ | CAGE peaks in BED format |
| polyA motif list (Optional) | _classify_ | polyA motif list in custom format |
| Intropolis data (Optional) | _classify_ | Intropolis data in custom format |
| Molecules FASTA | _make-seurat_ | Molecules FASTA file from _isoseq dedup_ |
| Groups txt file | _make-seurat_ | Groups text file from _isoseq collapse_ |