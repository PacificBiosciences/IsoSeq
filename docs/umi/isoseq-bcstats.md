---
layout: default
parent: Single cell
title: Barcode Statistics
nav_order: 7
---

***

`isoseq3 bcstats` emits statistics for each barcode:

1. Barcode sequence
2. Number of reads matching the barcode
3. Frequency Rank (within barcodes)
4. Number of unique molecular barcodes matching this barcode
5. Whether the barcode is Group/Cell barcode or a Molecular Barcode/UMI

If `--json` is unset, JSON summary information is written to stderr ("/dev/stderr").
Similarly, if '-o' is unset, output TSV information is written to stdout ("/dev/stdout").

```bash
# Example:
isoseq3 bcstats --json sample.bcstats.json -o sample.bcstats.tsv sample.bam
```

In default behavior, the program only emits stats on group barcodes.
Adding `--umi` will cause stats for the full molecular barcodes to be emitted as well.

<img src="../../doc/img/isoseq.png"/>
