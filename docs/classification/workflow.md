---
layout: default
parent: Classification
title: Pigeon CLI Workflow
nav_order: 3
---

## CLI Workflow

### Map reads to a reference genome

Map reads using _pbmm2_.

```
pbmm2 align --preset ISOSEQ --sort <input.bam> <ref.fa> <mapped.bam>
```

### Collapse transcripts into unique isoforms

Collapse mapped reads into unique isoforms using _isoseq collapse_.

```
isoseq3 collapse <mapped.bam> <collapse.gff>
```

### Sort input transcript GFF

Sort the transcript GFF file output from _isoseq collapse_.

```
pigeon sort <collapse.gff> -o sorted.gff
```

### Index the genome annotation

Index the genome annotation file before classification.

```
pigeon index <gencode.annotation.gtf>
```

### Classify Isoforms

Classify isoforms into [categories](/categories) using the base required input.

```
pigeon classify <isoforms.gff> <annotations.gtf> <reference.fa>
```

Alternatively, classify isoforms using supplemental reference information. Details in [pigeon input](/pigeon-input).

```
pigeon classify <sorted.gff> <annotations.gtf> <reference.fa> --fl abundance.txt --cage-peak refTSS.bed --poly-a polyA.list
```

### Filter isoforms

Filter isoforms from the classification output.

```
pigeon filter <classification.txt>
```

### Report gene saturation

Gene saturation can be determined by subsampling the classification output and determining the number of unique genes at each subsample size.

```
pigeon report <classification.filtered_lite_classification.txt> <saturation.txt>
```

### Make Seurat compatible input

Output files that are compatible with the downstream [Seurat](https://satijalab.org/seurat/) analysis package.

```
pigeon make-seurat --dedup molecules.fasta --group group.txt -d output_dir
```


