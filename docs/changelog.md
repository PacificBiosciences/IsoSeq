---
layout: default
title: Changelog
nav_order: 99
---

# Version changelog

 * **3.4.0**
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
     install `pbccs`, `lima`, and `pbcoretools` as needed.

 * 3.2.0
   * **`polish` dropped support for RS II datasets!**
   * Add `collapse` step for aligned transcript BAM input
   * Enable CCS-only workflow `cluster --use-qvs`
   * Add `refine --min-polya-length`
   * Add `cluster --singletons` to output unclustered FLNCs; potential sample
     prep artifacts!
   * Fix minimap2 bugs. Outputs might change slightly.

 * 3.1.2
   * Reduce `polish` memory footprint

 * 3.1.1
   * Edge case fix where `polish` would not finish and stale
   * Improve `polish` run time for large scale datasets (> 1M CCS)
   * Improve `polish` result quality

 * 3.1.0
   * We outsourced the poly(A) tail removal and concatemer detection into a new
     tool called `refine`. Your custom `primers.fasta` is used in this step to
     detect concatemers.
