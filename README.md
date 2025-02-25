<h1 align="center"><img width="300px" src="doc/img/isoseq.png"/></h1>
<h1 align="center">IsoSeq</h1>
<p align="center">Scalable De Novo Isoform Discovery</p>

***

*IsoSeq* contains the newest tools to identify transcripts in PacBio
single-molecule sequencing data. Starting in SMRT Link v6.0.0, those tools power
the *IsoSeq GUI-based analysis* application. A composable workflow of existing
tools and algorithms, combined with new clustering techniques, allows to process
the ever-increasing yield of PacBio machines. Starting with version 3.4, support
for UMI and cell barcode based deduplication has been added. Version 4.0 adds a
new `cluster2` tool that enables clustering of hundreds of millions of HiFi
reads.

## Announcement
The binary has been renamed from `isoseq3` to `isoseq` to enable major version
changes. Bioconda will still generate a `isoseq3` softlink. The old bioconda
`isoseq3` package will automatically install the latest `isoseq` package.

## Availability
Latest version can be installed via bioconda package `isoseq`.

Please refer to our [official pbbioconda page](https://github.com/PacificBiosciences/pbbioconda)
for information on Installation, Support, License, Copyright, and Disclaimer.

## Latest Version
Version **4.3.0**: [Full changelog here](docs/changelog)

## Workflow Documentation

 * Visit [isoseq.how](https://isoseq.how) for the latest documentation

## DISCLAIMER

THIS WEBSITE AND CONTENT AND ALL SITE-RELATED SERVICES, INCLUDING ANY DATA, ARE PROVIDED "AS IS," WITH ALL FAULTS, WITH NO REPRESENTATIONS OR WARRANTIES OF ANY KIND, EITHER EXPRESS OR IMPLIED, INCLUDING, BUT NOT LIMITED TO, ANY WARRANTIES OF MERCHANTABILITY, SATISFACTORY QUALITY, NON-INFRINGEMENT OR FITNESS FOR A PARTICULAR PURPOSE. YOU ASSUME TOTAL RESPONSIBILITY AND RISK FOR YOUR USE OF THIS SITE, ALL SITE-RELATED SERVICES, AND ANY THIRD PARTY WEBSITES OR APPLICATIONS. NO ORAL OR WRITTEN INFORMATION OR ADVICE SHALL CREATE A WARRANTY OF ANY KIND. ANY REFERENCES TO SPECIFIC PRODUCTS OR SERVICES ON THE WEBSITES DO NOT CONSTITUTE OR IMPLY A RECOMMENDATION OR ENDORSEMENT BY PACIFIC BIOSCIENCES.
