---
layout: default
parent: Clustering
title: CLI Workflow
nav_order: 3
---

# CLI Workflow

The low-level workflow explained via CLI calls. All necessary dependencies are
installed via bioconda.

## Step 1 - Input
### HiFi Reads
Each sequencing run is processed by [*ccs*](https://github.com/PacificBiosciences/ccs)
to generate one HiFi read from productive ZMWs.
After CCS is performed, you can use the `hifi_reads.bam` as input.
The `hifi_reads.bam` contains only HiFi reads, with predicted accuracy ≥Q20. No
additional filtering is required. HiFi reads that have been demultiplexed can also be used.

### Segmented Reads (S-reads)
HiFi reads that have been segmented using [*skera*](https://skera.how/) as a part of the MAS-Seq application can also be used.

## Step 2 - Primer removal and demultiplexing
Removal of primers and identification of cDNA barcodes is performed using [*lima*](https://lima.how/),
which can be installed with \
`conda install lima` and offers a specialized `--isoseq` mode.
Even in the case that your sample is not barcoded, primer removal is performed
by *lima*.
If there are more than two sequences in your `primer.fasta` file or better said
more than one pair of 5' and 3' primers, please use *lima* with `--peek-guess`
to remove spurious false positive signal.
More information about how to name input primer(+barcode)
sequences in this [lima Iso-Seq FAQ](https://lima.how/faq/isoseq).

    $ lima movieX.hifi_reads.bam barcoded_primers.fasta movieX.fl.bam --isoseq --peek-guess

If your sample is barcoded at the cDNA level, use `--overwrite-biosample-names` to update the sample names.
    $ lima movieX.hifi_reads.bam barcoded_primers.fasta movieX.fl.bam --isoseq --peek-guess --overwrite-biosample-names

**Example 1:**
Following is the IsoSeq_v2_primers_12.fasta for the MAS-Seq bulk Iso-Seq primers that supports 12-plex barcoding at the cDNA level:
    >IsoSeqX_bc01_5p
    CTACACGACGCTCTTCCGATCTACTACACGCAATGAAGTCGCAGGGTTGGG
    >IsoSeqX_bc02_5p
    CTACACGACGCTCTTCCGATCTACTAGTAGCAATGAAGTCGCAGGGTTGGG
    >IsoSeqX_bc03_5p
    CTACACGACGCTCTTCCGATCTAGTGTACGCAATGAAGTCGCAGGGTTGGG
    >IsoSeqX_bc04_5p
    CTACACGACGCTCTTCCGATCTATCACTAGCAATGAAGTCGCAGGGTTGGG
    >IsoSeqX_bc05_5p
    CTACACGACGCTCTTCCGATCTCAGCTGTGCAATGAAGTCGCAGGGTTGGG
    >IsoSeqX_bc06_5p
    CTACACGACGCTCTTCCGATCTCAGTCACGCAATGAAGTCGCAGGGTTGGG
    >IsoSeqX_bc07_5p
    CTACACGACGCTCTTCCGATCTCATGTATGCAATGAAGTCGCAGGGTTGGG
    >IsoSeqX_bc08_5p
    CTACACGACGCTCTTCCGATCTCGTATGTGCAATGAAGTCGCAGGGTTGGG
    >IsoSeqX_bc09_5p
    CTACACGACGCTCTTCCGATCTGACATGTGCAATGAAGTCGCAGGGTTGGG
    >IsoSeqX_bc10_5p
    CTACACGACGCTCTTCCGATCTGAGTCTAGCAATGAAGTCGCAGGGTTGGG
    >IsoSeqX_bc11_5p
    CTACACGACGCTCTTCCGATCTGTAGATAGCAATGAAGTCGCAGGGTTGGG
    >IsoSeqX_bc12_5p
    CTACACGACGCTCTTCCGATCTGTATGACGCAATGAAGTCGCAGGGTTGGG
    IsoSeqX_3p
    AAGCAGTGGTATCAACGCAGAGTAC

**Example 2: old Iso-Seq primers**
Following is the primer.fasta for the old Iso-Seq (not MAS-Seq) cDNA primers:

    >NEB_5p
    GCAATGAAGTCGCAGGGTTGGG
    >Clontech_5p
    AAGCAGTGGTATCAACGCAGAGTACATGGGG
    >NEB_Clontech_3p
    GTACTCTGCGTTGATACCACTGCTT

**Example 3:**
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

## Step 3 - Refine
Your data now contains full-length reads, but still needs to be refined by:
 - [Trimming](https://github.com/PacificBiosciences/trim_isoseq_polyA) of poly(A) tails
 - Rapid concatemer [identification](https://github.com/jeffdaily/parasail) and removal

**Input**\
The input file for *refine* is one demultiplexed CCS file with full-length reads
and the primer fasta file:
 - `<movie.primer--pair>.fl.bam` or `<movie.primer--pair>.fl.consensusreadset.xml`
 - `primers.fasta`

**Output**\
The following output files of *refine* contain full-length non-concatemer reads:
 - `<movie>.flnc.bam`
 - `<movie>.flnc.transcriptset.xml`

Actual command to refine:

    $ isoseq refine movieX.NEB_5p--NEB_Clontech_3p.fl.bam primers.fasta movieX.flnc.bam

If your sample has poly(A) tails, use `--require-polya`.
This filters for FL reads that have a poly(A) tail
with at least 20 base pairs (`--min-polya-length`) and removes identified tail:

    $ isoseq refine movieX.NEB_5p--NEB_Clontech_3p.fl.bam movieX.flnc.bam --require-polya

## Step 3b - Merge SMRT Cells
If you used more than one SMRT cells, list all of your `<movie>.flnc.bam` in one
`flnc.fofn`, a file of filenames:

    $ ls movie*.flnc.bam movie*.flnc.bam movie*.flnc.bam > flnc.fofn

## Step 4 - Clustering
The new `isoseq cluster2` implements a novel streaming clustering
workflow that can handle >100M input reads, without running out of memory,
while saturating high-core count servers.
Note: `isoseq cluster` will be deprecated and all isoform clustering should be using
`isoseq cluster2` moving forward.

**Input**
The input file for `isoseq cluster2` is one FLNC file:
 - `<movie>.flnc.bam`, `flnc.fofn`, or `<movie>.flnc.transcriptset.xml`

**Output**
The following output files of *cluster2* contain clustered isoforms:
 - `<prefix>.bam`
 - `<prefix>.bam.pbi`
 - `<prefix>.transcriptset.xml`

Example invocation:

    $ isoseq cluster2 flnc.fofn clustered.bam

The *cluster2* output contains isoforms with at least 2 FLNC reads.
To include singletons, the `--singletons` option should be used.
Additionally, with HiFi input all isoforms have a predicted accuracy ≥ 0.99.

Previously, `isoseq cluster` performed a single clustering technique.
Due to the nature of the algorithm, it can't be efficiently parallelized.
If using `isoseq cluster`, it is advised to give this step as many cores as possible.
The individual steps of *cluster* are as following:

 - Clustering using hierarchical n*log(n) [alignment](https://github.com/lh3/minimap2) and iterative cluster merging
 - Polished [POA](https://github.com/rvaser/spoa) sequence generation, using a QV guided consensus approach

**Input**
The input file for *cluster* is one FLNC file:
 - `<movie>.flnc.bam` or `flnc.fofn`

**Output**
The following output files of *cluster* contain polished isoforms:
 - `<prefix>.bam`
 - `<prefix>.hq.fasta.gz` with predicted accuracy ≥ 0.99
 - `<prefix>.lq.fasta.gz` with predicted accuracy < 0.99
 - `<prefix>.bam.pbi`
 - `<prefix>.transcriptset.xml`

Example invocation:

    $ isoseq cluster flnc.fofn clustered.bam --verbose --use-qvs
