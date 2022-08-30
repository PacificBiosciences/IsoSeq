---
layout: default
title: Getting Started
nav_order: 3
---

# Getting Started



## Recommended bulk Iso-Seq workflow

| Command | Description | Output format |
| --- | --- | --- |
| *lima* | Remove cDNA primers | `fl.bam` |
| *isoseq3 refine* | Remove polyA tail and artificial concatemers | `flnc.bam` |
| *isoseq3 cluster* | *De novo* isoform-level clustering | `unpolished.bam` |
| *pbmm2* | Align to the genome | `mapped.bam` |
| *isoseq3 collapse* | Collapse redundant transcripts based on exonic structures | `collapsed.gff` |
| *pigeon classify* | Classify transcripts against annotation | GFF and TXT files |
| *pigeon filter* | Filter transcripts for potential artifacts | GFF and TXT files |

Begin with the [bulk workflow](https://isoseq.how/clustering/) which ends at `isoseq3 cluster`, then continue to [pigeon workflow](https://isoseq.how/classification/) for transcript mapping, collapse, and classification.



## Recommended single-cell Iso-Seq workflow

| Command | Description | Output format |
| --- | --- | --- |
| *lima* | Remove cDNA primers | `fl.bam` |
| *isoseq3 tag* | Extract UMI and cell barcodes | `flt.bam` |
| *isoseq3 refine* | Remove polyA tail and artificial concatemers | `flnc.bam` |
| *isoseq3 correct* | Correct cell barcodes and tag reads that are real cells | `corrected.bam` |
| *isoseq3 bcstats* | Summarize barcode statistics for real/non-real cells | `bcstats_report.tsv` | 
| *isoseq3 groupdedup* | Deduplicate reads | `dedup.bam` |
| *pbmm2* | Align to the genome | `mapped.bam` |
| *isoseq3 collapse* | Collapse redundant transcripts based on exonic structures | `collapsed.gff` |
| *pigeon classify* | Classify transcripts against annotation | GFF and TXT files |
| *pigeon filter* | Filter transcripts for potential artifacts | GFF and TXT files |
| *pigeon make-seurat* | Make gene- and isoform-level matrices | MTX and TSV files |

Begin with the [single cell-specific worfklow](https://isoseq.how/umi/) which ends at `isoseq3 groupdedup`, then continue to [pigeon workflow](https://isoseq.how/classification/) for transcript mapping, collapse, and classification.
