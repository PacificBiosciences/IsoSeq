---
layout: default
parent: Single cell
title: Examples
nav_order: 4
---

## Real-world example

This is an example of an end-to-end cmd-line-only workflow:

    # Download HiFi reads
    $ wget https://downloads.pacbcloud.com/public/dataset/ISMB_workshop/singlecell/ccs.bam

    # Download primers
    $ wget https://downloads.pacbcloud.com/public/dataset/ISMB_workshop/singlecell/primers.fasta

    # Check lima version to be >= 2.6.0
    $ lima --version
    lima 2.6.0 (commit v2.6.0)

    # Check isoseq3 version to be >= 3.7.0
    $ isoseq3 --version
    isoseq3 3.7.0 (commit v3.7.0)

    # Primer removal
    $ lima --isoseq ccs.bam primers.fasta output.bam

    # Clip UMI and cell barcode
    $ isoseq3 tag output.5p--3p.bam flt.bam --design T-8U-12B

    # Remove poly(A) tails and concatemer
    $ isoseq3 refine flt.bam primers.fasta fltnc.bam --require-polya

    # Correct Barcodes
    $ isoseq3 correct --barcodes include.txt fltnc.bam corrected.bam

    $samtools sort -t CB corrected.bam -o corrected.sorted.bam

    # Deduplicate transcripts from the identical molecule
    $ isoseq3 groupdedup corrected.bam dedup.bam --log-level INFO
