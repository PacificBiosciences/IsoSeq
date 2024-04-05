---
layout: default
parent: Classification
title: Pigeon Output
nav_order: 5
---

## Pigeon _classify_ and _filter_ output

### Classification File

The _classify_ and _filter_ tools output a txt file containing isoform annotation information.
The output from _classify_ and _filter_ will have the extensions `_classification.txt` or `_filtered.classification.txt` respectively.
Both of these outputs follow the SQANTI3 [classification file](https://github.com/ConesaLab/SQANTI3/wiki/Understanding-the-output-of-SQANTI3-QC#glossary-of-classification-file-columns-classificationtxt) convention with the exception of two added columns.

| Column | Description |
| ------ | ----------- |
| fl_assoc | FL count associated with the isoform including PCR duplicates. |
| cell_barcodes | Comma separated list of unique cell barcode ids associated with the isoform. |

*Sample specific classification output*

The output from _classify_ and _filter_ can contain fields that are separated by sample and labeled with the sample name.
This output is expected when the `flnc_count.txt` file from `isoseq collapse` is input using `--flnc` to _classify_.

| Column | Description |
| ------ | ----------- |
| FL.\<Sample\> | FL count for the isoform by sample according to the `*.flnc_count.txt` file. |
| FL_TPM.\<Sample\> | Transcripts per million of the FL count for the isoform by sample. |
| FL_TPM.\<Sample\>_log10 | Log10 of the transcripts per million for the isoform by sample. |
| fl_assoc | FL count associated with the isoform across all samples in input. |

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

The _make-seurat_ tool outputs the required files to run tertiary analysis with [Seurat](https://satijalab.org/seurat/) and other examples [here](/umi/tertiary-analysis). Output is provided at both the isoform-level and the gene-level containing the sum of all isoforms associated with a particular gene. 

Files output:
```
<output_dir>/annotated.info.csv
<output_dir>/annotated-prefilter.info.csv
<output_dir>/info.csv
<output_dir>/genes_seurat/barcodes.tsv
<output_dir>/genes_seurat/genes.tsv
<output_dir>/genes_seurat/matrix.mtx
<output_dir>/isoforms_seurat/barcodes.tsv
<output_dir>/isoforms_seurat/genes.tsv
<output_dir>/isoforms_seurat/matrix.mtx
```

`info.csv` contains the following information:
```
id	UMI	UMIrev	BC	BCrev	length	count
molecule/0	CCGCTCTCCT	AGGAGAGCGG	AAACCTGAGACATAAC	GTTATGTCTCAGGTTT	3718	1
```

`annotated.info.csv` and `annotated-prefilter.info.csv` contains additional information from `pigeon classify` and `pigeon filter`. 
```
id	pbid	length	transcript	gene	category	ontarget	ORFgroup	UMI	UMIrev	BC	BCrev	pass_pigeon_filter
molecule/1856656	PB.10002.69	1201	ENST00000263918.9	STRN	incomplete-splice_match	NA	NA	GCATTACTGT	ACAGTAATGC	ACCGTAAAGAAGATTC	GAATCTTCTTTACGGT	PASS
```