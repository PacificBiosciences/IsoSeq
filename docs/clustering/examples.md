---
layout: default
parent: Clustering
title: Examples
nav_order: 4
---

## Real-world example

### Single sample

    This is a toy dataset consisting of ~80k segmented reads (S-reads) from a Kinnex full-length RNA library. The original HiFi reads have already been segmented using Skera, so we can directly go into primer removal using lima and such.

    # Download toy S-read dataset
    $ wget https://downloads.pacbcloud.com/public/dataset/IsoSeq_sandbox/human_80k_Sreads.segmented.bam

    # Download the Iso-Seq v2 cDNA primers (from Iso-Seq express 2.0 kit)
    $ wget https://downloads.pacbcloud.com/public/dataset/Kinnex-full-length-RNA/REF-primers/IsoSeq_v2_primers_12.fasta

    $ lima --version
    lima 2.9.0

    $ lima human_80k_Sreads.segmented.bam IsoSeq_v2_primers_12.fasta output.bam --isoseq --peek-guess

    $ ls output.*
    output.IsoSeqX_bc10_5p--IsoSeqX_3p.bam
    output.IsoSeqX_bc10_5p--IsoSeqX_3p.bam.pbi
    output.IsoSeqX_bc10_5p--IsoSeqX_3p.consensusreadset.xml
    output.json
    output.lima.clips
    output.lima.counts
    output.lima.guess
    output.lima.report
    output.lima.summary


    $ isoseq refine output.IsoSeqX_bc10_5p--IsoSeqX_3p.bam IsoSeq_v2_primers_12.fasta flnc.bam --require-polya

    $ ls flnc.*
    flnc.bam
    flnc.bam.pbi
    flnc.consensusreadset.xml
    flnc.filter_summary.report.json
    flnc.report.csv

    $ isoseq cluster2 flnc.bam transcripts.bam


    $ ls transcripts*
    transcripts.bam
    transcripts.bam.pbi
    transcripts.cluster_report.csv

### Multiplexed samples

    This is a 12-plex regular Iso-Seq (non-Kinex) run on Sequel II system consisting of ~3 million HiFi reads.

    # Download HiFi reads from a non-Kinnex (regular Iso-Seq) BAM file
    $ wget https://downloads.pacbcloud.com/public/dataset/Kinnex-full-length-RNA/DATA-SQ2-UHRR-Monomer/1-CCS/m64307e_230628_025302.hifi_reads.bam
    $ wget https://downloads.pacbcloud.com/public/dataset/Kinnex-full-length-RNA/DATA-SQ2-UHRR-Monomer/1-CCS/m64307e_230628_025302.hifi_reads.bam.pbi

    # Download the Iso-Seq v2 cDNA primers (from Iso-Seq express 2.0 kit)
    $ wget https://downloads.pacbcloud.com/public/dataset/Kinnex-full-length-RNA/REF-primers/IsoSeq_v2_primers_12.fasta

    # Demux and primer removal
    $ lima --overwrite-biosample-names --isoseq --peek-guess m64307e_230628_025302.hifi_reads.bam IsoSeq_v2_primers_12.fasta output.bam

    # Combine inputs
    $ ls output.IsoSeqX*bam > all.fofn

    # Remove poly(A) tails and concatemer
    $ isoseq refine all.fofn IsoSeq_v2_primers_12.fasta flnc.bam --require-polya

    $ isoseq cluster2 flnc.bam transcripts.bam
