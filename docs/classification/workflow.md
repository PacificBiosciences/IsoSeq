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

Single-cell Iso-Seq:
```
isoseq collapse <mapped.bam> <collapsed.gff>
```

Bulk Iso-Seq:
```
isoseq collapse --do-not-collapse-extra-5exons <mapped.bam> <flnc.bam> <collapsed.gff>
```

Note: The optional `<flnc.bam>` input is required to get the correct FLNC counts for bulk Iso-Seq in the `flnc_count.txt` supplemental file.

### Prepare reference files for pigeon
As of version *1.1.0* `pigeon prepare` replaces the `pigeon sort` and `pigeon index` tools.
Use `pigeon prepare` to sort and index the genome annotation, (optional) CAGE peak, and (optional) intropolis files before classification.
This step ensures that all records for a given chromosome/scaffold are contiguous within the file.
Additionally, if a reference fasta is provided, the `fai` index will be generated.

```
pigeon prepare <gencode.annotation.gtf> <reference.fa> <cage.bed> <intropolis.tsv>
```
or input a file of file names
```
pigeon prepare files.fofn
```

More information about pigeon reference input can be found [here](/classification/pigeon-input).

### Prepare input transcript GFF

Use `prepare` to sort the transcript GFF file output from _isoseq collapse_.

```
pigeon prepare <collapsed.gff>
```

### Classify Isoforms

**Transcript classification**

Classify isoforms into [categories](/classification/categories) using the base required input.

```
pigeon classify <collapsed.sorted.gff> <annotations.gtf> <reference.fa>
```

**Adding supplemental reference information to classification output**

Additionally, supplemental reference information can be added to the `classification.txt` output. Additional reference details can be found in [pigeon input](/classification/pigeon-input).

```
pigeon classify <collapsed.sorted.gff> <annotations.gtf> <reference.fa> --cage-peak refTSS.bed --poly-a polyA.list
```
Alternatively use provided reference sets [here](https://downloads.pacbcloud.com/public/dataset/MAS-Seq/REF-pigeon_ref_sets/).

```
pigeon classify <sorted.gff> --ref Human_hg38_Gencode_v39.referenceset.xml
```
**Adding FLNC counts to classification output**

FLNC counts can be added to the `classification.txt` output.
Pigeon uses supplemental files from `isoseq collapse` to to add counts.

For single-cell Iso-Seq, use the `abundance.txt` output from `isoseq collapse`. This file contains the deduped FLNC counts and cell barcodes.

```
pigeon classify <collapsed.sorted.gff> <annotations.gtf> <reference.fa> --fl abundance.txt
```

For bulk Iso-Seq, use the `flnc_count.txt` output from `isoseq collapse`. This file contains the FLNC counts after `isoseq refine` separated by sample if applicable.

```
pigeon classify <collapsed.sorted.gff> <annotations.gtf> <reference.fa> --fl flnc_count.txt
```

### Filter isoforms

Filter isoforms from the classification output.

```
pigeon filter <classification.txt>
```

If you want to generate a filtered GFF, you need to also provide the GFF that was used as input to `pigeon classify`

```
pigeon filter <classification.txt> --isoforms <collapsed.sorted.gff>
```

The expected output consists of:
```
*.filtered_lite_classification.txt
*.filtered_lite_junctions.txt
*.filtered_lite_reasons.txt
*.sorted.filtered_lite.gff (only if --isoforms is used)
```


### Report gene saturation

Gene and isoform- level saturation can be determined by subsampling the classification output and determining the number of unique genes / isoforms at each subsample size.

```
pigeon report <classification.filtered_lite_classification.txt> <saturation.txt>
```

### Make Seurat-compatible gene- and isoform- count matrix for single-cell Iso-Seq

Output files that are compatible with the downstream [Seurat](https://satijalab.org/seurat/) analysis package.

```
pigeon make-seurat --dedup <dedup.fasta> --group <collapse.group.txt> -d <output_dir> <classification.filtered_lite_classification.txt>
```

The `dedup.fasta` file is obtained after running `isoseq groupdedup`. The `collapse.group.txt` file is obtained after running `isoseq collapse`.

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

