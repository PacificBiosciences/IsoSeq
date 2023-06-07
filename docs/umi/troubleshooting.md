---
layout: default
parent: Single cell
title: Troubleshooting
nav_order: 9
---

# Troubleshooting SMRT Link MAS-Seq Single-cell Iso-Seq output

Last Updated: 02/07/2023

This page serves as a supplement guide to troubleshooting SMRT Link Read Segmentation & Single-cell Iso-Seq analysis for using the [MAS-Seq for 10x Single Cell 3' kit](https://www.pacb.com/products-and-services/applications/rna-sequencing/single-cell-rna-sequencing/). Please always refer to [the official SMRT Link documentation](https://www.pacb.com/support/software-downloads/) for the latest documentation. 

MAS-Seq dataset and reference URL: [https://downloads.pacbcloud.com/public/dataset/MAS-Seq/](https://downloads.pacbcloud.com/public/dataset/MAS-Seq/)

***
* <a href="#read-segmentation">Read Segmentation output metrics</a>
* <a href="#single-cell-iso-seq-read-statistics">Single-cell Iso-Seq, Read Statistics output metrics</a>
* <a href="#single-cell-iso-seq-cell-statistics">Single-cell Iso-Seq, Cell Statistics output metrics</a>
* <a href="#single-cell-iso-seq-transcript-statistics">Single-cell Iso-Seq, Transcript Statistics output metrics</a>
* <a href="#troubleshooting-smrt-link-output">Troubleshooting SMRT Link output</a>
***

## Read Segmentation

Read segmentation de-concatenates HiFi reads into segmented reads (S-reads) based on segmentation adapters using [skera](https://skera.how).

| Metric       |  Explanation  | Typical value | 
| :------------- |:------------- |:-----|
| **Reads**      | Number of HiFi reads | Depends on sequencing yield |
| **S-reads**     | Number of segmented reads | Depends on HiFi read yield and concatenation success |
| **Mean length of S-reads** | Mean RL of S-reads | 600-800bp for 10x cDNA | 
| **Percent of reads with full arrays** | % HiFi reads with full MAS arrays | 85-90% |
| **Mean array size** | Concatenation factor | ~15.xx |

A typical HiFi RL will have a clean peak between 10,000-14,000 bp indicating good array formation and successful enrichment of full arrays.

While the S-read read length should large reflect the original 10x cDNA library size.

## Single-cell Iso-Seq, Read Statistics

| Metric       |  Explanation  | Typical value | 
| :------------- |:------------- |:-----|
| **Reads**      | Number of (S-)reads | Depends on sequencing yield |
| **Read Type**  | CCS or SEGMENT | CCS or SEGMENT |
| **Reads with 5' and 3' Primers with extracted UMIs and Barcodes** | FL tagged reads | >95% of reads should be FL tagged | 
| **Non-Concatemer Reads with 5' and 3' Primers and PolyA Tail** | FLNC tagged reads | >90% of reads should be FLNC tagged |
| **FLNC Reads with Valid Barcodes** | FLNC reads matching barcode whitelist | >90% reads should match barcode | 
| **FLNC Reads with Valid Barcodes, corrected** | FLNC reads matching barcode whitelist w correction | >90% reads should match barcode post correction |
| **Reads after Barcode Correction and UMI Deduplication** | Dedup reads | De-duplicated read yield depends on 10x library complexity and PCR duplication rate |

## Single-cell Iso-Seq, Cell Statistics

| Metric       |  Explanation  | Typical value | 
| :------------- |:------------- |:-----|
| **Estimated Number of Cells**      | Number of real cells | Depends on 10x library |
| **Reads in Cells**  | % of reads in real cells | >85% |
| **Mean Reads per Cell**  | mean reads per real cell | Depends on 10x library and read yield |
| **Median UMIs per Cell**  | median UMI per real cell | Depends on 10x library, read yield, and PCR duplication rate |

The number of estimated cells, mean reads/cell and median UMIs/cell are highly dependent on the 10x single cell library and sample complexity. In the case where it is suspected that the cell estimation is incorrect using the default `knee` method for `isoseq correct`, the cells can be re-estimated using alternative approach of `percentile` method. Refer to [cell calling](https://isoseq.how/umi/cell-calling.html) for more details.

## Single-cell Iso-Seq, Transcript Statistics

| Metric       |  Explanation  | Typical value | 
| :------------- |:------------- |:-----|
| **FLNC Reads Mapped Confidently to Genome**      | FLNC reads (before dedup) mapped to genome [note1] | ~80% |
| **FLNC Reads Mapped Confidently to Transcriptome**  | FLNC reads (before dedup) mapped to transcriptome [note2] | 30-50% |
| **Total Unique Genes**  | Total unique genes before `pigeon filter` [note3] | Sample-dependent |
| **Total Unique Genes, filtered**  | Total unique genes after `pigeon filter` [note3] | Sample-dependent|
| **Total Unique Genes, known genes only**  | Total unique known genes before `pigeon filter` [note3] | Sample-dependent |
| **Total Unique Genes, filtered, known genes only**  | Total unique known genes after `pigeon filter` [note3] | Sample-dependent|
| **Total Unique Transcripts**  | Total unique transcripts before `pigeon filter` | Sample-dependent |
| **Total Unique Transcripts, filtered**  | Total unique transcripts after `pigeon filter` | Sample-dependent|
| **Total Unique Transcripts, known transcripts only**  | Total unique known transcripts before `pigeon filter` | Sample-dependent |
| **Total Unique Transcripts, filtered, known transcripts only**  | Total unique known transcripts after `pigeon filter` | Sample-dependent|

[note1] FLNC reads mapped to the genome after running `isoseq collapse` (we actually map dedup reads, but expand it back to reflect the pre-deduplicated FLNC count). Note that `isoseq collapse` filters for reads that map chimerically or map with low identity, so if there are cancer fusion genes or genes not well represented in the genome, they’d be excluded at this step. In general, we should expect most (~80%) FLNC reads to map to the genome, even if they end up mapping to, say, intergenic regions.

[note2] FLNC reads mapped to known genes (known or novel isoforms) after `pigeon classify` and `pigeon filter`. Think of this as the “number of usable reads” that actually go into a standard single-cell analyses. This number includes ribosomal/mitochondrial genes. We typically see 30-50% FLNC reads map to the transcriptome, which is consistent with equivalent 10x short read sequencing data. Most of the non-transcriptomic but genomically mapped reads are attributed to intergenic regions and are filtered out by `pigeon filter`.

[note3] It is typical to see a very high number of "total number of genes/transcripts" before `pigeon filter`. This is due to the high number of loci that are intergenic and still being assigned a "novel gene" status before `pigeon filter`. 

## Troubleshooting SMRT Link output

| Issue      |  Likely cause  | Solution | 
| :------------- |:------------- |:-----|
| Good concatenation factor<br>Low S-read yield | Low sequencing yield | Additional sequencing |
| Good S-read yield<br>Poor FLNC yield and beyond | Not using 10x 3' kit (v3.1) | Reanalyze with proper cDNA primer, UMI/BC design, and barcode whitelist [note1] | 
| Good S-read yield<br>Good cell statistics<br>Poor read  mapping and low gene counts| Wrong reference selected | Choose correct reference genome & annotation [note2] |
| Good S-read yield<br>Poor cell recovery<br> | Algorithm underestimated number of cells | Re-analyze with `percentile` method in SL or command line |
| `Analysis experienced an error, but was able to recover and complete successfully; High Barcode Errors` | Incorrect barcode whitelist | Re-analyze with correct barcode whitelist [note3] |

[note1] Additional 10x cDNA primers and barcode whitelist may be found [here](https://downloads.pacbcloud.com/public/dataset/MAS-Seq/)

[note2] SMRT Link only supports [human and mouse reference genome + Gencode annotation](https://downloads.pacbcloud.com/public/dataset/MAS-Seq/REF-pigeon_ref_sets/). If using different genomes, refer to [pigeon documentation](https://isoseq.how/classification/) for command line analysis.

[note3] If you see the error message `Analysis experienced an error, but was able to recover and complete successfully; High Barcode Errors` it means the barcode whitelist provided is incorrect. Note that SMRT Link expects a barcode whitelist that is *reverse-complemented*, which is not how the 10x whitelist is typically provided. A list of common barcode whitelist in reverse-complement can be found [here](https://downloads.pacbcloud.com/public/dataset/MAS-Seq/REF-10x_barcodes/).