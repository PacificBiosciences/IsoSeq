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
irrespective of quality and passes. 

We recommend using only HiFi reads!


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

    $ lima movieX.ccs.bam primers.fasta movieX.fl.bam --isoseq --peek-guess

**Example 1:**
If using the 10x 3' kit, the primers are below:

    >5p
    AAGCAGTGGTATCAACGCAGAGTACATGGG
    >3p
    AGATCGGAAGAGCGTCGTGTAG

**Example 2:**
If using the 10x 5' kit, the primers are below:

    >5p
    CTACACGACGCTCTTCCGATCT
    >3p
    GTACTCTGCGTTGATACCACTGCTT

*Lima* will remove unwanted combinations and orient sequences to 5' → 3' orientation.

Output files will be called according to their primer pair. Example for
single sample libraries:

    movieX.fl.5p--3p.bam


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

    $ isoseq tag movieX.fl.5p--3p.bam movieX.flt.bam --design XXX
    
Refer to the [UMI and BC design page](https://isoseq.how/umi/umi-barcode-design.html) for how to specify `--design`.

For example, the 10x 3' (v3.1) kit has a 12bp UMI and 16bp BC on the 3' end, so the design would be `--design T-12U-16B`.

In contrast, the 10x 5' kit has a 16bp BC and 10bp UMI, so the design would be `--design 16B-10U-T`.

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

    $ isoseq refine movieX.fl.5p--3p.bam primers.fasta movieX.fltnc.bam --require-polya

If your sample has poly(A) tails, use `--require-polya`.
This filters for FL reads that have a poly(A) tail
with at least 20 base pairs and removes identified tail:

    $ isoseq refine movieX.NEB_5p--NEB_Clontech_3p.fl.bam movieX.fltnc.bam --require-polya

Optional read quality filtering, if your initial CCS input is `<movie>.reads.bam`

    $ isoseq refine movieX.NEB_5p--NEB_Clontech_3p.fl.bam movieX.flnc.bam --min-rq 0.9

## Step 4b - Merge SMRT Cells
If you used more than one SMRT cells, merge all of your `<movie>.fltnc.bam` files:

    $ ls movie1.fltnc.bam movie2.fltnc.bam movieN.fltnc.bam > fltnc.fofn


## Step 5 - Cell Barcode Correction
This step identifies 10x cell barcode errors and correct them. The tool uses the 10x cell barcode whitelist to reassign erroneous barcodes based on edit distance.


**Method**

First, the *correct* tool builds a Locality-Sensitive Hashing (LSH) index over the 10x whitelist barcode subsequences.
In the second step, *correct* uses the LSH index to map raw input barcodes to their nearest barcodes in the truth-set.

For each input HiFI read containing a 10x cell barcode:
 -  If the barcode is in the whitelist, it is unchanged.
 -  If the barcode is not found in the whitelist, the index is queried for the closest match in the whitelist.
 -  Edit distance is calculated between all retrieved whitelist cell barcodes and the input barcode.
 -  The barcode with the lowest edit distance and lowest hamming distance is output.
 -  By default, if the edit distance between the cell barcode and whitelist barcode is > 2, the read is marked as failing.
 -  If no candidates were found, the barcode is unchanged, and the read is marked as failing.

**Input** The input file for correct is one FLTNC file:
 - `<movie>.fltnc.bam`

**Output** The following output files of correct contain reads with corrected cell barcodes:
 - `<prefix>.bam`
 - `<prefix>.bam.pbi`

Example invocation:
    $ isoseq correct --barcodes barcode_set.txt flnc.bam flnc.corrected.bam


## Step 6 - Deduplication
This step performs PCR deduplicatation via clustering by UMI and cell barcodes (if available).

We provide two methods: *dedup* and *groupdedup*.

They perform nearly identical functionality. The key difference is that *groupdedup* only deduplicates
reads sharing a cell barcode and *groupdedup* requires both barcode correction with the *correct* tool and sorting by cell barcode (tag "CB").
(Sorting a BAM by cell barcode may be efficiently accomplished by `samtools sort -t CB`.)

This is because sequencing errors introduce erroneous barcodes, yielding spurious reads.
*dedup* allows for barcode errors through pairwise barcode alignment, but *groupdedup* assumes that barcodes are correct.
Performing this correction step allows this faster *groupdedup* step to reasonably make this assumption while
also allowing for mismatches using the index.

This can provide over 200x speed-ups, as well as substantially reducing RAM requirements.


After deduplication, *dedup* and *groupdedup* generate one consensus sequence per founder molecule,
using a QV guided consensus.

**Method**

Perform all vs all comparison and cluster two reads if:
 * lengths are within +- 50 bp length
 * UMI (+cell barcode) match with at max 1 mismatch and may be shifted by at max 1 base
 * pairwise concordance is at least 97%
 * alignment starts/ends within 5 bp of the other read
 * no more than 5 bps are deleted or inserted in a window of 20 bp (like in isoseq cluster)
 * *groupdedup* only: these reads have the same cell barcode

**Input**
The input file for *dedup* is one FLTNC file:
 - `<movie>.fltnc.bam` or `fltnc.fofn`

The input file for *groupdedup* is one FLTNC file, sorted by 10x cell barcode tag:
 - `<movie>.tagsort.bam`


**Output**
The following output files of *dedup* contain polished isoforms:
 - `<prefix>.bam`
 - `<prefix>.hq.fasta.gz` with predicted accuracy ≥ 0.99
 - `<prefix>.lq.fasta.gz` with predicted accuracy < 0.99
 - `<prefix>.bam.pbi`
 - `<prefix>.transcriptset.xml`

The following output files of *groupdedup* contain polished isoforms:
 - `<prefix>.bam`
 - `<prefix>.bam.pbi`

Example invocation (*dedup*):

    $ isoseq dedup fltnc.fofn dedup.bam --verbose

Example invocation (*groupdedup*):

    $ isoseq groupdedup fltnc.tagsort.bam dedup.bam
