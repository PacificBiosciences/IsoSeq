---
layout: default
parent: Classification
title: Pigeon Input
nav_order: 4
---

## Pigeon Tools Input

**prepare**
| File | Description |
| ----------- | ---- |
| Transcript GFF | Collapsed transcript GFF from _isoseq collapse_ |
| Reference Annotation | Reference annotation in GTF format |
| CAGE Peaks (Optional) | CAGE peaks in BED format |
| Intropolis data (Optional) | Intropolis data in custom format |

**classify**
| File | Description |
| ----------- | ---- |
| Reference Genome | Reference genome in FASTA format |
| Reference Annotation | Sorted and indexed (`prepare`) reference annotation in GTF format |
| CAGE Peaks (Optional) | Sorted and indexed (`prepare`) CAGE peaks in BED format |
| Intropolis data (Optional) | Sorted and indexed (`prepare`) Intropolis data in custom format |
| polyA motif list (Optional) | polyA motif list in custom format |
| FL Counts (Optional) | Single-cell IsoSeq FL count info from _isoseq collapse_ (`*.abundance.txt`)|
| FL Counts (Optional) | Bulk IsoSeq FL count info from _isoseq collapse_ (`*.flnc_counts.txt`) |

**make-seurat**
| File | Description |
| ----------- | ---- |
| Molecules FASTA | Molecules FASTA file from _isoseq dedup_ |
| Groups txt file | Groups text file from _isoseq collapse_ |

### Examples
Sorted and indexed references for human and mouse can be found [here](https://downloads.pacbcloud.com/public/dataset/MAS-Seq/REF-pigeon_ref_sets/).

Guidance about custom references can be found [here] (/classification/pigeon-annotation).
