---
layout: default
title: Iso-Seq Home
nav_order: 1
description: "Scalable De Novo Isoform Discovery from PacBio HiFi Reads."
permalink: /
---

<p align="center">
  <img src="img/isoseq_card.png" alt="lima logo" width="650px"/>
</p>

***

*IsoSeq* contains the newest tools to identify transcripts in PacBio
single-molecule sequencing data. Starting in SMRT Link v6.0.0, those tools power
the *Iso-Seq GUI-based analysis* application. A composable workflow of existing
tools and algorithms, combined with new clustering techniques, allows to process
the ever-increasing yield of PacBio machines. Starting with version 3.4, support
for UMI and cell barcode based deduplication has been added. Version 4.0 adds a
new `cluster2` tool that enables clustering of hundreds of millions of HiFi
reads.

## Availability
Latest version can be installed via bioconda package `isoseq`.

Please refer to our [official pbbioconda page](https://github.com/PacificBiosciences/pbbioconda)
for information on Installation, Support, License, Copyright, and Disclaimer.

## Latest Version
Version **4.0.0**: [Full changelog here](/changelog)

## What's new!
Version 4.0 adds a new `cluster2` tool that enables clustering of hundreds of
millions of HiFi reads. This is an early access version. Additional
documentation will follow soon.

The binary has been renamed from `isoseq3` to `isoseq` to enable major version
changes. Bioconda will still generate a `isoseq3` softlink. The old bioconda
`isoseq3` package will automatically install the latest `isoseq` package.

