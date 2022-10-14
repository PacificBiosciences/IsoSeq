---
layout: default
parent: Single cell
title: CLI Workflow
nav_order: 3
---

# CLI Workflow

The low-level workflow explained via CLI calls. All necessary dependencies are
installed via bioconda.

For a toy dataset + command walkthrough, see the [Example](https://isoseq.how/umi/examples.html) page.

## Step 1 - Input
### CLR data from Sequel / Sequel II / Sequel IIe
For each SMRT cell a `<movie>.subreads.bam` is needed for processing.

Each sequencing run is processed by [*ccs*](https://github.com/PacificBiosciences/ccs)
to generate one HiFi read from productive ZMWs.
It is advised to use the latest CCS version 4.2.0 or newer.
_ccs_ can be installed with `conda install pbccs`.

    $ ccs <movie>.subreads.bam <movie>.ccs.bam

You can easily parallelize _ccs_ generation by chunking, please follow [this how-to](https://ccs.how/faq/parallelize).

### CCS data from Sequel IIe
If on-instrument CCS was performed, you can use the `reads.bam` or
`hifi_reads.bam` as input.

The `hifi_reads.bam` contains only HiFi reads, with predicted accuracy ≥Q20. No
additional filtering is required.

The `reads.bam` contains one representative sequence per productive ZMW,
irrespective of quality and passes. 

We recommend using only HiFi reads!


## Step 2 - Primer removal 

Removal of primers and identification of barcodes is performed using [*lima*](https://lima.how/),
which can be installed with \
`conda install lima` and offers a specialized `--isoseq` mode.
Even in the case that your sample is not barcoded, primer removal is performed
by *lima*.

More information about how to name input primer
sequences in this [lima Iso-Seq FAQ](https://lima.how/faq/isoseq).

    $ lima <movie>.ccs.bam primers.fasta <movie>.fl.bam --isoseq 

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

    <movie>.fl.5p--3p.bam


## Step 3 - Tag
Tags, such as UMIs and cell barcodes, have to be clipped from the reads and
associated with the reads for later deduplication.

**Input**
The input file for *tag* is one full-length CCS file from the previous _lima_ step: `<movie>.fl.5p--3p.bam`

**Output**
The following output files of *tag* contain full-length tagged:
 - `<movie>.flt.bam`
 - `<movie>.flt.transcriptset.xml`

Insert your own design or pick a preset:

    $ isoseq3 tag <mvie>.fl.5p--3p.bam <movie>.flt.bam --design XXX
    
Refer to the [UMI and BC design page](https://isoseq.how/umi/umi-barcode-design.html) for how to specify `--design`.

For example, the 10x 3' (v3.1) kit has a 12bp UMI and 16bp BC on the 3' end, so the design would be `--design T-12U-16B`.

In contrast, the 10x 5' kit has a 16bp BC and 10bp UMI, so the design would be `--design 16B-10U-T`.

## Step 4 - Refine
Your data now contains full-length tagged reads, but still needs to be refined by:
 - Trimming of poly(A) tails
 - Unintended concatmer identification and removal (note: if the library was constructed using the MAS-Seq method, the reads should have already gone through [skera](https://skera.how) and is not expected to contain any more concatemers at this step)

**Input**
The input file for *refine* full-length tagged reads and the primer fasta file:
 - `<movie>.flt.bam` or `<movie>.flt.transcriptset.xml`
 - `primers.fasta` 

**Output**
The following output files of *refine* contain full-length non-concatemer (FLNC) reads:
 - `<movie>.fltnc.bam`
 - `<movie>.fltnc.transcriptset.xml`

Actual command to refine:

    $ isoseq3 refine <movie>.fl.5p--3p.bam primers.fasta <movie>.fltnc.bam --require-polya

If your sample has poly(A) tails, use `--require-polya`. 

This filters for FL reads that have a poly(A) tail with at least 20 base pairs. You can change the polyA length minimum with `--min-polya-length`.


## Step 4b - Merge SMRT Cells
If you used more than one SMRT cells, merge all of your `<movie>.fltnc.bam` files:

    $ ls <movie1>.fltnc.bam <movie2>.fltnc.bam ... <movieN>.fltnc.bam > fltnc.fofn


## Step 5 - Cell Barcode Correction and Real Cell Identification
This step identifies cell barcode errors and corrects them. The tool uses a cell barcode whitelist to reassign erroneous barcodes based on edit distance. Additionally, *correct* estimates which reads are likely to originate from a real cell and labels them using the `rc` tag.

For details on barcode correction, visit the [barcode correction](https://isoseq.how/umi/isoseq-correct.html) page.


**Input** The input file for *correct* is one FLTNC file:
 - `<movie>.fltnc.bam`

**Output** The following output files of *correct* contain reads with corrected cell barcodes:
 - `<prefix>.bam`
 - `<prefix>.bam.pbi`

Example:
    $ isoseq3 correct --barcodes barcode_set.txt fltnc.bam fltnc.corrected.bam

Common single-cell whitelist (e.g. 10x whitelist for 3' kit) can be found in the [MAS-Seq dataset](https://downloads.pacbcloud.com/public/dataset/MAS-Seq/).


## Step 6 - Deduplication
This step performs PCR deduplication via clustering by UMI and cell barcodes (if available).

We provide two methods: *dedup* and *groupdedup*. It is recommended	to use *groupdedup* for most cases.

They perform nearly identical functionality. The key difference is that *groupdedup* only deduplicates
reads sharing a cell barcode and *groupdedup* requires both barcode correction with the *correct* tool and sorting by cell barcode (tag "CB").
(Sorting a BAM by cell barcode may be efficiently accomplished by `samtools sort -t CB`.)

This is because sequencing errors introduce erroneous barcodes, yielding spurious reads.
*dedup* allows for barcode errors through pairwise barcode alignment, but *groupdedup* assumes that barcodes are correct.
Performing this correction step allows this faster *groupdedup* step to reasonably make this assumption while
also allowing for mismatches using the index.

This can provide over 200x speed-ups, as well as substantially reducing RAM requirements.

If the `rc` tag added by *correct* is present in the input, *groupdedup* and *dedup* will filter the output to only include real cells. This can be turned off using ` --keep-non-real-cells`.

After deduplication, *dedup* and *groupdedup* generate one consensus sequence per founder molecule,
using a QV guided consensus.

For more details, visit the [dedup FAQ](https://isoseq.how/umi/dedup-faq.html).


**Input**
The input file for *dedup* is a barcode-corrected FLTNC file: `<movie>.fltnc.corrected.bam` 

The input file for *groupdedup* is one FLTNC file, sorted by cell barcode (`CB`) tag: `<movie>.fltnc.corrected.sorted.bam`


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

Example(*dedup*):

    $ isoseq3 dedup fltnc.corrected.bam dedup.bam 

Example(*groupdedup*):

    $ samtools sort -t CB fltnc.corrected.bam -o fltnc.corrected.sorted.bam
    $ isoseq3 groupdedup fltnc.corrected.sorted.bam dedup.bam



## What to do after deduplication

After obtaining deduplicated reads, follow the rest of the recommended [single-cell Iso-Seq workflow](https://isoseq.how/getting-started.html#recommended-single-cell-iso-seq-workflow), which continues on with [mapping, collapse, and transcript classification](https://isoseq.how/classification/workflow.html).
