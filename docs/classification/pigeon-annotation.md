---
layout: default
parent: Classification
title: Pigeon Annotations
nav_order: 6
---

## How to create a pigeon‐compatible annotation GTF

Pigeon is designed to work for [Gencode annotation](https://www.gencodegenes.org/) GTF file formats. Other GTF formats will need to be modified to work with `pigeon classify`.

The pigeon GTF format requirements are:

A tab-delimited 9-column file [GFF/GTF File Format](https://useast.ensembl.org/info/website/upload/gff.html)

* Column 1 must be the chromosome
* Column 2 is ignored
* Column 3 will only be processed if it is gene, transcript, or exon. All other types (e.g. CDS) are ignored.
* Column 4 & 5 are 1-based start/end
* Column 6 & 8 are ignored
* Column 7 is the strand which must be + or -
* Column 9 is attribute, a semicolon-separated list of tag-value pairs. To be processed properly, the following tags must have values: gene_id , transcript_id and gene_name. Ex: gene_id "ENSG0001"; transcript_id "ENST000A"; gene_name "TP53";
* No extra blank lines at the beginning or end of the file
* Annotations must be organized with a "gene" record, followed by one or more associated "transcript" records, and each "transcript" record is followed by one or more associated "exon" records. Example:
```
  gene
  transcript_1
    exon_1_1
    exon_1_2
  transcript_2
    exon_2_1
    exon_2_2
```

## Example 1: Gencode annotation

Below is a snippet of a Gencode annotation as a reference:

```
chr1    ENSEMBL gene    17369   17436   .       -       .       gene_id "ENSG00000278267.1"; gene_type "miRNA"; gene_status "KNOWN"; gene_name "MIR68
59-1"; level 3;
chr1    ENSEMBL transcript      17369   17436   .       -       .       gene_id "ENSG00000278267.1"; transcript_id "ENST00000619216.1"; gene_type "mi
RNA"; gene_status "KNOWN"; gene_name "MIR6859-1"; transcript_type "miRNA"; transcript_status "KNOWN"; transcript_name "MIR6859-1-201"; level 3; tag "
basic"; transcript_support_level "NA";
chr1    ENSEMBL exon    17369   17436   .       -       .       gene_id "ENSG00000278267.1"; transcript_id "ENST00000619216.1"; gene_type "miRNA"; ge
ne_status "KNOWN"; gene_name "MIR6859-1"; transcript_type "miRNA"; transcript_status "KNOWN"; transcript_name "MIR6859-1-201"; exon_number 1; exon_id
 "ENSE00003746039.1"; level 3; tag "basic"; transcript_support_level "NA";
chr1    HAVANA  gene    29554   31109   .       +       .       gene_id "ENSG00000243485.3"; gene_type "lincRNA"; gene_status "KNOWN"; gene_name "RP1
1-34P13.3"; level 2; tag "ncRNA_host"; havana_gene "OTTHUMG00000000959.2";
chr1    HAVANA  transcript      29554   31097   .       +       .       gene_id "ENSG00000243485.3"; transcript_id "ENST00000473358.1"; gene_type "li
ncRNA"; gene_status "KNOWN"; gene_name "RP11-34P13.3"; transcript_type "lincRNA"; transcript_status "KNOWN"; transcript_name "RP11-34P13.3-001"; leve
l 2; tag "not_best_in_genome_evidence"; tag "basic"; transcript_support_level "5"; havana_gene "OTTHUMG00000000959.2"; havana_transcript "OTTHUMT0000
0002840.1";
chr1    HAVANA  exon    29554   30039   .       +       .       gene_id "ENSG00000243485.3"; transcript_id "ENST00000473358.1"; gene_type "lincRNA";
gene_status "KNOWN"; gene_name "RP11-34P13.3"; transcript_type "lincRNA"; transcript_status "KNOWN"; transcript_name "RP11-34P13.3-001"; exon_number
1; exon_id "ENSE00001947070.1"; level 2; tag "not_best_in_genome_evidence"; tag "basic"; transcript_support_level "5"; havana_gene "OTTHUMG0000000095
9.2"; havana_transcript "OTTHUMT00000002840.1";
chr1    HAVANA  exon    30564   30667   .       +       .       gene_id "ENSG00000243485.3"; transcript_id "ENST00000473358.1"; gene_type "lincRNA";
gene_status "KNOWN"; gene_name "RP11-34P13.3"; transcript_type "lincRNA"; transcript_status "KNOWN"; transcript_name "RP11-34P13.3-001"; exon_number
2; exon_id "ENSE00001922571.1"; level 2; tag "not_best_in_genome_evidence"; tag "basic"; transcript_support_level "5"; havana_gene "OTTHUMG0000000095
9.2"; havana_transcript "OTTHUMT00000002840.1";
chr1    HAVANA  exon    30976   31097   .       +       .       gene_id "ENSG00000243485.3"; transcript_id "ENST00000473358.1"; gene_type "lincRNA";
gene_status "KNOWN"; gene_name "RP11-34P13.3"; transcript_type "lincRNA"; transcript_status "KNOWN"; transcript_name "RP11-34P13.3-001"; exon_number
3; exon_id "ENSE00001827679.1"; level 2; tag "not_best_in_genome_evidence"; tag "basic"; transcript_support_level "5"; havana_gene "OTTHUMG0000000095
9.2"; havana_transcript "OTTHUMT00000002840.1";
```

## Example 2: modified non-model organism annotation for Pigeon

Here is an example of a pigeon-compatible annotation after it's been manually modified.

```
Pf3D7_13_v3     VEuPathDB       gene    21364   28787   .       +       .       gene_id "PF3D7_1300100"; transcript_id "PF3D7_1300100.1"; gene_name "
PF3D7_1300100"; transcript_name "PF3D7_1300100.1"; biotype "test";
Pf3D7_13_v3     VEuPathDB       transcript      21364   28787   .       +       .       gene_id "PF3D7_1300100"; transcript_id "PF3D7_1300100.1"; gen
e_name "PF3D7_1300100"; transcript_name "PF3D7_1300100.1"; biotype "test";
Pf3D7_13_v3     VEuPathDB       exon    21364   26538   .       +       .       gene_id "PF3D7_1300100"; transcript_id "PF3D7_1300100.1"; gene_name "
PF3D7_1300100"; transcript_name "PF3D7_1300100.1"; biotype "test";
Pf3D7_13_v3     VEuPathDB       exon    27474   28787   .       +       .       gene_id "PF3D7_1300100"; transcript_id "PF3D7_1300100.1"; gene_name "
PF3D7_1300100"; transcript_name "PF3D7_1300100.1"; biotype "test";
Pf3D7_13_v3     VEuPathDB       CDS     21364   26538   .       +       0       Parent=PF3D7_1300100.1
Pf3D7_13_v3     VEuPathDB       CDS     27474   28787   .       +       0       Parent=PF3D7_1300100.1
Pf3D7_13_v3     VEuPathDB       gene    30605   31881   .       -       .       gene_id "PF3D7_1300200"; transcript_id "PF3D7_1300200.1"; gene_name "
PF3D7_1300200"; transcript_name "PF3D7_1300200.1"; biotype "test";
Pf3D7_13_v3     VEuPathDB       transcript      30605   31881   .       -       .       gene_id "PF3D7_1300200"; transcript_id "PF3D7_1300200.1"; gen
e_name "PF3D7_1300200"; transcript_name "PF3D7_1300200.1"; biotype "test";
Pf3D7_13_v3     VEuPathDB       exon    30605   31597   .       -       .       gene_id "PF3D7_1300200"; transcript_id "PF3D7_1300200.1"; gene_name "
PF3D7_1300200"; transcript_name "PF3D7_1300200.1"; biotype "test";
Pf3D7_13_v3     VEuPathDB       exon    31828   31881   .       -       .       gene_id "PF3D7_1300200"; transcript_id "PF3D7_1300200.1"; gene_name "
PF3D7_1300200"; transcript_name "PF3D7_1300200.1"; biotype "test";
Pf3D7_13_v3     VEuPathDB       CDS     30605   31597   .       -       0       Parent=PF3D7_1300200.1
Pf3D7_13_v3     VEuPathDB       CDS     31828   31881   .       -       0       Parent=PF3D7_1300200.1
```

## Example 3: SIRV control annotation

Here is an example of an SIRV control annotation compatible with pigeon.

```
SIRV1	LexogenSIRVData	gene	1001	11643	.	-	0	gene_name "SIRV1"; gene_id "SIRV1";
SIRV1	LexogenSIRVData	transcript	1001	10786	.	-	0	gene_name "SIRV1"; gene_id "SIRV1"; transcript_id "SIRV101";
SIRV1	LexogenSIRVData	exon	1001	1484	.	-	0	gene_name "SIRV1"; gene_id "SIRV1"; transcript_id "SIRV101"; exon_assignment "SIRV101_0";
SIRV1	LexogenSIRVData	exon	6338	6473	.	-	0	gene_name "SIRV1"; gene_id "SIRV1"; transcript_id "SIRV101"; exon_assignment "SIRV101_1";
SIRV1	LexogenSIRVData	exon	6561	6813	.	-	0	gene_name "SIRV1"; gene_id "SIRV1"; transcript_id "SIRV101"; exon_assignment "SIRV101_2";
SIRV1	LexogenSIRVData	exon	7553	7814	.	-	0	gene_name "SIRV1"; gene_id "SIRV1"; transcript_id "SIRV101"; exon_assignment "SIRV101_3";
SIRV1	LexogenSIRVData	exon	10283	10366	.	-	0	gene_name "SIRV1"; gene_id "SIRV1"; transcript_id "SIRV101"; exon_assignment "SIRV101_4";
SIRV1	LexogenSIRVData	exon	10445	10786	.	-	0	gene_name "SIRV1"; gene_id "SIRV1"; transcript_id "SIRV101"; exon_assignment "SIRV101_5";
SIRV1	LexogenSIRVData	transcript	1007	10366	.	-	0	gene_name "SIRV1"; gene_id "SIRV1"; transcript_id "SIRV102";
SIRV1	LexogenSIRVData	exon	1007	1484	.	-	0	gene_name "SIRV1"; gene_id "SIRV1"; transcript_id "SIRV102"; exon_assignment "SIRV102_0";
SIRV1	LexogenSIRVData	exon	6338	6813	.	-	0	gene_name "SIRV1"; gene_id "SIRV1"; transcript_id "SIRV102"; exon_assignment "SIRV102_1";
SIRV1	LexogenSIRVData	exon	7553	7814	.	-	0	gene_name "SIRV1"; gene_id "SIRV1"; transcript_id "SIRV102"; exon_assignment "SIRV102_2";
SIRV1	LexogenSIRVData	exon	10283	10366	.	-	0	gene_name "SIRV1"; gene_id "SIRV1"; transcript_id "SIRV102"; exon_assignment "SIRV102_3";
SIRV1	LexogenSIRVData	transcript	1001	10791	.	-	0	gene_name "SIRV1"; gene_id "SIRV1"; transcript_id "SIRV103";
SIRV1	LexogenSIRVData	exon	1001	1484	.	-	0	gene_name "SIRV1"; gene_id "SIRV1"; transcript_id "SIRV103"; exon_assignment "SIRV103_0";
SIRV1	LexogenSIRVData	exon	6338	6473	.	-	0	gene_name "SIRV1"; gene_id "SIRV1"; transcript_id "SIRV103"; exon_assignment "SIRV103_1";
SIRV1	LexogenSIRVData	exon	6561	6813	.	-	0	gene_name "SIRV1"; gene_id "SIRV1"; transcript_id "SIRV103"; exon_assignment "SIRV103_2";
SIRV1	LexogenSIRVData	exon	7553	7814	.	-	0	gene_name "SIRV1"; gene_id "SIRV1"; transcript_id "SIRV103"; exon_assignment "SIRV103_3";
SIRV1	LexogenSIRVData	exon	10283	10366	.	-	0	gene_name "SIRV1"; gene_id "SIRV1"; transcript_id "SIRV103"; exon_assignment "SIRV103_4";
SIRV1	LexogenSIRVData	exon	10648	10791	.	-	0	gene_name "SIRV1"; gene_id "SIRV1"; transcript_id "SIRV103"; exon_assignment "SIRV103_5";
```
