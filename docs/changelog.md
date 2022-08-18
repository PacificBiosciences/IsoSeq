---
layout: default
title: Changelog
nav_order: 99
---

# Version changelog

 * **3.8.0**
   * `collapse` allows isoforms with 5p degradation to collapse by default
   * `--do-not-collapse-extra-5exons` added to `collapse`
   * `collapse` max 5p and 3p distances can be set in CLI using 
     `--max-5p-diff` and `--max-3p-diff`
   * Real-cell annotation in `correct` using `rc` tag
   * Real-cell filtering in `groupdedup`, `dedup`, and `collapse`
  
 * 3.7.0
   * Adding `bcstats`, `correct`, and `groupdedup` to CLI
   * `bcstats` emits frequency statistics for 10x barcodes
   * `correct` uses a truth-set to correct sequencing errors in cell barcodes
   * `groupdedup` provides substantial performance improvements over dedup
   * Support SEGMENT read type

 * 3.6.0
   * Adding `tag` and `dedup` to CLI

 * 3.5.0
   * SMRT Link release 11.0
   * Remove support for CLR data and disable `polish` step
   * Enable `cluster --use-qvs` as always on

 * 3.4.0
   * SMRT Link release 10.0.0
   * Add support for UMI and cell barcode handling, by adding `tag` and `dedup`
   * Add `refine --min-rq` to support RQ filtering for unfiltered
     `<movie>.reads.bam` input

 * 3.3.0
   * SMRT Link release 9.0.0

 * 3.2.2
   * Fix `polish` not generating fasta/q output. This bug was introduced in
     v3.2.0

 * 3.2.1
   * Fix a gff index 1-off bug in `collapse`
   * We have removed implicit dependencies from the bioconda recipe. Please
     install `pbccs`, `lima`, and `pbcoretools` as needed

 * 3.2.0
   * **`polish` dropped support for RS II datasets!**
   * Add `collapse` step for aligned transcript BAM input
   * Enable CCS-only workflow `cluster --use-qvs`
   * Add `refine --min-polya-length`
   * Add `cluster --singletons` to output unclustered FLNCs; potential sample
     prep artifacts!
   * Fix minimap2 bugs. Outputs might change slightly

 * 3.1.2
   * Reduce `polish` memory footprint

 * 3.1.1
   * Edge case fix where `polish` would not finish and stale
   * Improve `polish` run time for large scale datasets (> 1M CCS)
   * Improve `polish` result quality

 * 3.1.0
   * We outsourced the poly(A) tail removal and concatemer detection into a new
     tool called `refine`. Your custom `primers.fasta` is used in this step to
     detect concatemers
