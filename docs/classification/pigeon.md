---
layout: default
parent: Classification
title: Pigeon
nav_order: 1
---

## Pigeon

_Pigeon_ is a PacBio Transcript Toolkit that contains tools to classify and filter full-length transcript isoforms into [categories](/categories) against a reference annotation. _Pigeon_ is based off of [SQANTI3](https://github.com/ConesaLab/SQANTI3) and the output is compatible with downstream analysis with Seurat.

The _pigeon_ tool is in beta. Expect continual changes, which may change the algorithm and or the command line interface.

## Tools

| Tool | Description |
| ----------- | ---- |
| sort        | Transcript sorting |
| index       | Annotation file indexing |
| classify    | Transcript classification |
| filter      | Transcript classification filtering |
| report      | Transcript reporting |
| make-seurat | Generate Seurat-compatible matrices |

## Input

- Collapsed isoforms in GFF format from [IsoSeq collapse](/isoseq-collapse).
- Several reference input files required across all steps of the _pigeon_ workflow are described [here](/pigon-input).

## Execution

The CLI workflow is described [here](/workflow).

## Availability
The latest version of `pigeon` is distributed through [BioConda](https://github.com/PacificBiosciences/pbbioconda).

## Versions
Version **0.1.0**: [Full changelog here](/pigeon-changelog)