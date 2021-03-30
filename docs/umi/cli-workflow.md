---
layout: default
parent: Single cell
title: CLI Workflow
nav_order: 3
---

# CLI Workflow

The low-level workflow explained via CLI calls. All necessary dependencies are
installed via bioconda.

## Step 1 - Input
### CLR data from Sequel / Sequel II / Sequel IIe
For each SMRT cell a `movieX.subreads.bam` is needed for processing.

Each sequencing run is processed by [*ccs*](https://github.com/PacificBiosciences/ccs)
to generate one HiFi read from productive ZMWs.
It is advised to use the latest CCS version 4.2.0 or newer.
_ccs_ can be installed with `conda install pbccs`.

    $ ccs movieX.subreads.bam movieX.ccs.bam

You can easily parallelize _ccs_ generation by chunking, please follow [this how-to](https://ccs.how/faq/parallelize).

### CCS data from Sequel IIe
If on-instrument CCS was performed, you can use the `reads.bam` or
`hifi_reads.bam` as input.

The `hifi_reads.bam` contains only HiFi reads, with predicted accuracy ≥Q20. No
additional filtering is required.

The `reads.bam` contains one representative sequence per productive ZMW,
irrespective of quality and passes. Do not forget to use `isoseq3 refine
--min-rq 0.99` in step 3!


## Step 2 - Primer removal and demultiplexing
Removal of primers and identification of barcodes is performed using [*lima*](https://lima.how/),
which can be installed with \
`conda install lima` and offers a specialized `--isoseq` mode.
Even in the case that your sample is not barcoded, primer removal is performed
by *lima*.
If there are more than two sequences in your `primer.fasta` file or better said
more than one pair of 5' and 3' primers, please use *lima* with `--peek-guess`
to remove spurious false positive signal.
More information about how to name input primer(+barcode)
sequences in this [lima Iso-Seq FAQ](https://lima.how/faq/isoseq).

    $ lima movieX.ccs.bam barcoded_primers.fasta movieX.fl.bam --isoseq --peek-guess

**Example 1:**
Following is the `primer.fasta` for the Clontech SMARTer and NEB cDNA library
prep, which are the officially recommended protocols:

    >NEB_5p
    GCAATGAAGTCGCAGGGTTGGG
    >Clontech_5p
    AAGCAGTGGTATCAACGCAGAGTACATGGGG
    >NEB_Clontech_3p
    GTACTCTGCGTTGATACCACTGCTT

**Example 2:**
Following are examples for barcoded primers using a 16bp barcode followed by
Clontech primer:

    >primer_5p
    AAGCAGTGGTATCAACGCAGAGTACATGGGG
    >brain_3p
    CGCACTCTGATATGTGGTACTCTGCGTTGATACCACTGCTT
    >liver_3p
    CTCACAGTCTGTGTGTGTACTCTGCGTTGATACCACTGCTT

*Lima* will remove unwanted combinations and orient sequences to 5' → 3' orientation.

Output files will be called according to their primer pair. Example for
single sample libraries:

    movieX.fl.NEB_5p--NEB_Clontech_3p.bam

If your library contains multiple samples, execute the following workflow
for each primer pair:

    movieX.fl.primer_5p--brain_3p.bam
    movieX.fl.primer_5p--liver_3p.bam

## Step 3 - Tag
Tags, such as UMIs and cell barcodes, have to be clipped from the reads and
associated with the reads for later deduplication.

**Input**
The input file for *tag* is one demultiplexed CCS file with full-length reads:
 - `<movie.primer--pair>.fl.bam` or `<movie.primer--pair>.fl.consensusreadset.xml`

**Output**
The following output files of *tag* contain full-length tagged:
 - `<movie>.flt.bam`
 - `<movie>.flt.transcriptset.xml`

Insert your own design or pick a preset:

    $ isoseq tag movieX.NEB_5p--NEB_Clontech_3p.fl.bam movieX.flt.bam --design XXX

## Step 4 - Refine
Your data now contains full-length tagged reads, but still needs to be refined by:
 - [Trimming](https://github.com/PacificBiosciences/trim_isoseq_polyA) of poly(A) tails
 - Rapid concatmer [identification](https://github.com/jeffdaily/parasail) and removal

**Input**
The input file for *refine* full-length tagged reads and the primer fasta file:
 - `<movie.primer--pair>.flt.bam` or `<movie.primer--pair>.flt.consensusreadset.xml`
 - `primers.fasta`

**Output**
The following output files of *refine* contain full-length non-concatemer reads:
 - `<movie>.fltnc.bam`
 - `<movie>.fltnc.transcriptset.xml`

Actual command to refine:

    $ isoseq refine movieX.NEB_5p--NEB_Clontech_3p.fl.bam primers.fasta movieX.fltnc.bam

If your sample has poly(A) tails, use `--require-polya`.
This filters for FL reads that have a poly(A) tail
with at least 20 base pairs and removes identified tail:

    $ isoseq refine movieX.NEB_5p--NEB_Clontech_3p.fl.bam movieX.fltnc.bam --require-polya

Optional read quality filtering, if your initial CCS input is `<movie>.reads.bam`

    $ isoseq refine movieX.NEB_5p--NEB_Clontech_3p.fl.bam movieX.flnc.bam --min-rq 0.9

## Step 4b - Merge SMRT Cells
If you used more than one SMRT cells, merge all of your `<movie>.fltnc.bam` files:

    $ ls movie1.fltnc.bam movie2.fltnc.bam movieN.fltnc.bam > fltnc.fofn

## Step 5 - Deduplication
This step performs PCR deduplicatation via clustering by UMI and cell barcodes (if available).
After deduplication, *dedup* generates one consensus sequence per founder molecule,
using a QV guided consensus approach.

**Method**

Perform all vs all comparison and cluster two reads if:
 * lengths are within +- 50 bp length
 * UMI (+cell barcode) match with at max 1 mismatch and may be shifted by at max 1 base
 * pairwise concordance is at least 97%
 * alignment starts/ends within 5 bp of the other read
 * no more than 5 bps are deleted or inserted in a window of 20 bp (like in isoseq cluster)

**Input**
The input file for *dedup* is one FLTNC file:
 - `<movie>.fltnc.bam` or `fltnc.fofn`

**Output**
The following output files of *dedup* contain polished isoforms:
 - `<prefix>.bam`
 - `<prefix>.hq.fasta.gz` with predicted accuracy ≥ 0.99
 - `<prefix>.lq.fasta.gz` with predicted accuracy < 0.99
 - `<prefix>.bam.pbi`
 - `<prefix>.transcriptset.xml`

Example invocation:

    $ isoseq dedup fltnc.fofn dedup.bam --verbose
