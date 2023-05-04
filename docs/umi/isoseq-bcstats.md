---
layout: default
parent: Single cell
title: Barcode Statistics
nav_order: 7
---

## Barcode Statistics Documentation

`isoseq3 bcstats` emits statistics for each barcode:

1. Barcode sequence
2. Number of reads matching the barcode
3. Frequency Rank (within barcodes)
4. Number of unique molecular barcodes matching this barcode
5. Whether the barcode is Group/Cell barcode or a Molecular Barcode/UMI

This tool should be run after `isoseq3 correct`.


```bash
# Example:
isoseq3 bcstats --json sample.bcstats.json -o sample.bcstats.tsv corrected.bam
```

If `--json` is unset, JSON summary information is written to stderr ("/dev/stderr").
Similarly, if '-o' is unset, output TSV information is written to stdout ("/dev/stdout").

By default, the program only emits stats on group barcodes.
Adding `--umi` will cause stats for the full molecular barcodes to be emitted as well.

Note that, if `isoseq3 correct` was called using the percentile method (see [Cell Calling](https://isoseq.how/umi/cell-calling.html)), then `isoseq3 bcstats` need to use the same parameters as well. For example:

```
isoseq3 correct --method percentile --percentile 90 ...
isoseq3 bcstats --method percentile --percentile 90
```

