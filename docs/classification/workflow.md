---
layout: default
parent: Classification
title: Classification CLI Workflow
nav_order: 3
---

## CLI Workflow

### Map reads to a reference genome

Map reads using _pbmm2_.

```
pbmm2 align --preset ISOSEQ --sort <input.bam> <ref.fa> <mapped.bam>
```

### Collapse into unique isoforms

Collapse redundant transcripts into unique isoforms based on exonic structures using _isoseq collapse_.

Single-cell IsoSeq:
```
isoseq3 collapse <mapped.bam> <collapsed.gff>
```

Bulk IsoSeq:
```
isoseq3 collapse --do-not-collapse-extra-5exons <mapped.bam> <collapsed.gff>
```

### Sort input transcript GFF

Sort the transcript GFF file output from _isoseq collapse_.

```
pigeon sort <collapsed.gff> -o sorted.gff
```

### Sort and index the reference files

Sort and index the genome annotation, (optional) CAGE peak, and (optional) intropolis files before classification. 
Sorting prior to indexing ensures that all records for a given chromosome/scaffold are contiguous within the file.

```
pigeon sort gencode.annotation.gtf -o gencode.annotation.sorted.gtf
pigeon index gencode.annotation.sorted.gtf

pigeon sort cage.bed -o cage.sorted.bed
pigeon index cage.sorted.bed

pigeon sort intropolis.tsv -o intropolis.sorted.tsv
pigeon index intropolis.sorted.tsv
```

### Classify Isoforms

Classify isoforms into [categories](/classification/categories) using the base required input.

```
pigeon classify <sorted.gff> <annotations.gtf> <reference.fa>
```

Alternatively, classify isoforms using supplemental reference information. Details in [pigeon input](/classification/pigeon-input).

```
pigeon classify <sorted.gff> <annotations.gtf> <reference.fa> --fl abundance.txt --cage-peak refTSS.bed --poly-a polyA.list
```

### Filter isoforms

Filter isoforms from the classification output.

```
pigeon filter <classification.txt>
```

If you want to generate a filtered GFF, you need to also provide the GFF that was used as input to `pigeon classify`

```
pigeon filter <classification.txt> --isoforms <sorted.gff>
```

The expected output consists of:
```
*.filtered_lite_classification.txt
*.filtered_lite_junctions.txt
*.filtered_lite_reasons.txt
*.sorted.filtered_lite.gff (only if --isoforms is used)
```


### Report gene saturation

Gene saturation can be determined by subsampling the classification output and determining the number of unique genes at each subsample size.

```
pigeon report <classification.filtered_lite_classification.txt> <saturation.txt>
```

### Make Seurat compatible input

Output files that are compatible with the downstream [Seurat](https://satijalab.org/seurat/) analysis package.

```
pigeon make-seurat --dedup <dedup.fasta> --group <collapse.group.txt> -d <output_dir> <classification.filtered_lite_classification.txt>
```

The `dedup.fasta` file is obtained after running `isoseq3 groupdedup` or `isoseq3 dedup`. The `collapse.group.txt` file is obtained after running `isoseq3 collapse`. 

The output will consist of:
```
Make-seurat output:
<output_dir>/annotated.info.csv
<output_dir>/info.csv
<output_dir>/genes_seurat/barcodes.tsv
<output_dir>/genes_seurat/genes.tsv
<output_dir>/genes_seurat/matrix.mtx
<output_dir>/isoforms_seurat/barcodes.tsv
<output_dir>/isoforms_seurat/genes.tsv
<output_dir>/isoforms_seurat/matrix.mtx
```

