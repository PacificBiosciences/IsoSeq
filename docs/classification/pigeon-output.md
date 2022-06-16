---
layout: default
parent: Classification
title: Pigeon Output
nav_order: 5
---

## Pigeon _classify_ and _filter_ output

### Classification File

The _classify_ and _filter_ tools output a txt file containing isoform annotation information.
The output from _classify_ and _filter_ will have the extensions `_classification.txt` or `_classification.filtered_lite_classification.txt` respectively.
Both of these outputs follow the SQANTI3 [classification file](https://github.com/ConesaLab/SQANTI3/wiki/Understanding-the-output-of-SQANTI3-QC#glossary-of-classification-file-columns-classificationtxt) convention with the exception of two added columns.

| Column | Description |
| ------ | ----------- |
| fl_assoc | FL count associated with the isoform including PCR duplicates. |
| cell_barcodes | Comma separated list of cell barcodes associated with the isoform. |

### Junction File

The _classify_ tool outputs a txt file containing every junction for each isoform (`_junctions.txt`) following the SQANTI3 [junction file](https://github.com/ConesaLab/SQANTI3/wiki/Understanding-the-output-of-SQANTI3-QC#glossary-of-classification-file-columns-classificationtxt) convention.

### Filtered Reasons File

The _filter_ tool outputs a txt file containing the reasons an isoform was filtered.

Reasons an isoform can be filtered:

| Reason | Description |
| ------ | ----------- |
| IntraPriming | The primer was annealing to downstream A-rich regions. |
| Mono-Exonic | The isoform contains a single exon. |
| RTSwitching | There is an artifact of reverse transcriptase template switching. |
| LowCoverage/Non-Canonical | There is a low sample coverage for splice sites that are not in the known "canonical" set of splice sites. |

Example:

```
# classification: sample_classification.txt
# isoform: ????????
# intrapriming cutoff: 0.6
# min_cov cutoff: 3
filtered_isoform,filter
PB.1.1,IntraPriming
PB.1.6,LowCoverage/Non-Canonical
PB.1.7,IntraPriming
PB.5.1,LowCoverage/Non-Canonical
PB.6.1,LowCoverage/Non-Canonical
```

## Pigeon _report_ output

The _report_ tools outputs a txt file containing the read count and number of unique genes found in a subsampled number of reads.

## Pigeon _make-seurat_ output

The _make-seurat_ tool outputs the required files to run tertiary analysis with [Seurat](https://satijalab.org/seurat/).

Files output:
 - `barcodes.tsv`
 - `genes.tsv`
 - `matrix.mtx`
 - `info.csv`
 - `annotated.info.csv`
