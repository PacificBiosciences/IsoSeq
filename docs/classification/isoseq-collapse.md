---
layout: default
parent: Classification
title: Pigeon Workflow
nav_order: 2
---

## IsoSeq collapse

After transcript sequences are mapped to a reference genome, `isoseq3 collapse` can be used to collapse redundant transcripts (based on exonic structures) into unique isoforms. Output consists of unique isoforms in GFF format and secondary files containing information about the number of reads supporting each unique isoform.

### Collapse Examples

<img src="../img/collapse.png" alt="collapse" width="1000px"/>

### Execution

Map reads using _pbmm2_ before collapsing

```
pbmm2 align --preset ISOSEQ --sort <input.bam> <ref.fa> <mapped.bam>
```

Collapse mapped reads into unique isoforms using _isoseq collapse_.

```
isoseq3 collapse <mapped.bam> <collapse.gff>
```

### Ouptut

- `collapse.gff` contains the collapsed isoforms in gff format.
- `*.abundance.txt` contains information about the number of FLNC reads supporting each isoform and cell barcodes if applicable. Each unique isoform has the ID format PB.X.Y, while `count_fl` denotes the number of unique molecules (after UMI deduplication) supporting the isoform, and `fl_assoc` denotes the number of reads (before UMI deduplication) supporting it. `cell_barcodes` shows the list of single cell barcodes from which the reads came from, if applicable.
    ```
    pbid	count_fl	fl_assoc	cell_barcodes
    PB.1.1	2	2	ATCCATTCACCTCTGT,ATCGGCGCAGAGATGC
    PB.2.1	1	1	CGGACACCATTGCCGG
    PB.3.1	1	1	ACTTCGCGTCTAACTG
    ```
- `*.group.txt` shows the grouping of redundant isoforms (based on mapped exonic structures), where the read names `molecule/<number>` denote a unique molecule after UMI deduplication.
    ```
    PB.1.1	molecule/7343975,molecule/7738347
    PB.2.1	molecule/14601188
    PB.3.1	molecule/3998518
    ```
- `*.read_stat.txt` shows the assignment of each read (before UMI deduplication) to the final, unique isoforms PB.X.Y. Read names with the format `<movie>/<zmw>/ccs` indicate a CCS read, whereas `<movie>/<zmw>/ccs/<start>_<end>` further denotes a segment of a CCS read (S-read), likely as a result of segmentation (using, for example, [Skera](http://skera.how/)) of concatenated single cell libraries.
    ```
    id	pbid
    m64012_220421_000242/120719489/ccs/10460_11196	PB.1.1
    m64012_220421_000242/17565024/ccs/13918_14203	PB.1.1
    m64012_220421_000242/161089449/ccs/1955_2888	PB.2.1
    m64012_220421_000242/158664505/ccs/2488_2901	PB.3.1
    ```
