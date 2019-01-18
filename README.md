<h1 align="center"><img width="300px" src="doc/img/isoseq3.png"/></h1>
<h1 align="center">IsoSeq3</h1>
<p align="center">Scalable De Novo Isoform Discovery</p>

***

*IsoSeq3* contains the newest tools to identify transcripts in
PacBio single-molecule sequencing data.
Starting in SMRT Link v6.0.0, those tools power the
*IsoSeq3 GUI-based analysis* application.
A composable workflow of existing tools and algorithms, combined with
a new clustering technique, allows to process the ever-increasing yield of PacBio
machines with similar performance to *IsoSeq1* and *IsoSeq2*.

## Availability
Latest version can be installed via bioconda package `isoseq3`.

Please refer to our [official pbbioconda page](https://github.com/PacificBiosciences/pbbioconda)
for information on Installation, Support, License, Copyright, and Disclaimer.

## Specific Version Documentation

 * [Version 3.1, SMRT Link 7.0](README_v3.1.md)
 * [Version 3.0, SMRT Link 6.0](README_v3.0.md)

## Changelog
 * **3.1.0**: We outsourced the poly(A) tail removal and concatemer detection into a new tool
called `refine`. Your custom `primers.fasta` is used in this step to detect
concatemers.

## FAQ
### Why IsoSeq3 and not the established IsoSeq1 or IsoSeq2?
The ever-increasing throughput of the Sequel system gave rise to the need for a
scalable software solution that can handle millions of CCS reads, while
maintaining sensitivity and accuracy. Internal benchmarks have shown that
*IsoSeq3* is orders of magnitude faster than currently employed solutions and
[SQANTI](https://bitbucket.org/ConesaLab/sqanti) attributes *IsoSeq3* a higher
number of perfectly annotated isoforms:

<img width="1000px" src="doc/img/isoseq3-performance.png"/>

Additional benefit, single linux binary that requires no dependencies.

### Why is the number of transcripts much lower with IsoSeq3?
Even though we also observe fewer polished transcripts with *IsoSeq3*, the
overall quality is much higher. Most of the low-quality transcripts are lost in the
demultiplexing step. *Isoseq1/2 classify* is too relaxed and is not filtering
junk molecules to a satisfactory level. In fact, *lima* calls are spot on and
effectively removes most molecules that are wrongly tagged, as in two 5' or two
3' primers. Only a proper 5' and 3' primer pair allows to identify a full-length
transcript and its orientation.


### I can't find the *classify* step
Starting with version 3.1, *classify* functionality has been split into two tools.
Removal of (barcoded) primers is performed with PacBio's standard demultiplexing
tool *lima*. *Lima* does not remove poly(A) tails, nor detects concatemers.
For this, `isoseq3 refine` generates FLNC reads.

For version 3.0, poly(A) tail removal and concatemer detection is performed in
`isoseq3 cluster`

### My sample has poly(A) tails, how can I remove them?
Use `--require-polya` for `isoseq3 refine`.
This filters for FL reads that have a poly(A) tail
with at least 20 base pairs and removes identified tail.

### How long will it take until my data has been processed?
There is no ETA feature. Depending on the sample type, whole transcriptome
or targeted amplification, run time varies. The same number of reads from a
whole transcriptome sample can finish clustering in minutes, whereas a single
gene amplification of 10kb transcripts can take a couple of hours.

### Which clustering algorithm is used?
In contrast to its predecessors, *IsoSeq3* does not rely on NP-hard clique
finding, but uses a hierarchical alignment strategy with `O(N*log(N))`.
Recent advances in rapid alignment of long reads make this this approach
feasible.

### How many CCS reads are used for the unpolished cluster sequence representation?
*Cluster* uses up to 10 CCS reads to generate the unpolished cluster consensus.

### How many subreads are used for polishing?
*Polish* uses up to 60 subreads to polish the cluster consensus.

### When are two reads clustered?
*IsoSeq3* deems two reads to stem from the same transcript, if they meet
following criteria:

<img width="1000px" src="doc/img/isoseq3-similar-transcripts.png"/>

There is no upper limit on the number of gaps.

### BAM tags explained
Following BAM tags are being used:

 - `ib` Barcode summary: triplets delimited by semicolons, each triplet contains two barcode indices and the ZMW counts, delimited by comma. Example: `0,1,20;0,3,5`
 - `im` ZMW names associated with this isoform
 - `is` Number of ZMWs associated with this isoform
 - `iz` Maximum number of subreads used for polishing
 - `rq` Predicted accuracy for polished isoform

 Quality values are capped at `93`.

### What SMRTbell designs are possible?

PacBio supports three different SMRTbell designs for IsoSeq libraries.
In all designs, transcripts are labelled with asymmetric primers,
whereas a poly(A) tail is optional. Barcodes may be optionally added.

<img width="600px" src="doc/img/isoseq3-barcoding.png"/>

### The binary does not work on my linux system!
Binaries require **SSE4.1 CPU support**; CPUs after 2008 (Penryn) include it.

## DISCLAIMER

THIS WEBSITE AND CONTENT AND ALL SITE-RELATED SERVICES, INCLUDING ANY DATA, ARE PROVIDED "AS IS," WITH ALL FAULTS, WITH NO REPRESENTATIONS OR WARRANTIES OF ANY KIND, EITHER EXPRESS OR IMPLIED, INCLUDING, BUT NOT LIMITED TO, ANY WARRANTIES OF MERCHANTABILITY, SATISFACTORY QUALITY, NON-INFRINGEMENT OR FITNESS FOR A PARTICULAR PURPOSE. YOU ASSUME TOTAL RESPONSIBILITY AND RISK FOR YOUR USE OF THIS SITE, ALL SITE-RELATED SERVICES, AND ANY THIRD PARTY WEBSITES OR APPLICATIONS. NO ORAL OR WRITTEN INFORMATION OR ADVICE SHALL CREATE A WARRANTY OF ANY KIND. ANY REFERENCES TO SPECIFIC PRODUCTS OR SERVICES ON THE WEBSITES DO NOT CONSTITUTE OR IMPLY A RECOMMENDATION OR ENDORSEMENT BY PACIFIC BIOSCIENCES.
