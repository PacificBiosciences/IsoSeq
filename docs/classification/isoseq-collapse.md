---
layout: default
parent: Classification
title: Pigeon Workflow
nav_order: 2
---

## IsoSeq collapse

After reads are mapped to a reference genome, transcripts can be collapsed into unique isoforms using `isoseq3 collapse`. Mapped IsoSeq reads are collapsed to produce a set of unique isoforms for each locus of the genome in GFF format, along with a secondary files containing information about the number of processed reads supporting each unique isoform.

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
- `*.abundance.txt` contains information about the number of FLNC reads supporting each isoform and cell barcodes if applicable.
    ```
    pbid	count_fl	fl_assoc	cell_barcodes
    PB.1.1	2	2	ATCCATTCACCTCTGT,ATCGGCGCAGAGATGC
    PB.2.1	1	1	CGGACACCATTGCCGG
    PB.3.1	1	1	ACTTCGCGTCTAACTG
    ```
- `*.group.txt` contains the grouped input by isoform.
    ```
    PB.1.1	molecule/7343975,molecule/7738347
    PB.2.1	molecule/14601188
    PB.3.1	molecule/3998518
    ```
- `*.read_stat.txt` contains the isoforms that reads are assigned to.
    ```
    id	pbid
    m64012_220421_000242/120719489/ccs/10460_11196	PB.1.1
    m64012_220421_000242/17565024/ccs/13918_14203	PB.1.1
    m64012_220421_000242/161089449/ccs/1955_2888	PB.2.1
    m64012_220421_000242/158664505/ccs/2488_2901	PB.3.1
    ```
