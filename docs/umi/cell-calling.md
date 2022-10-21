---
layout: default
parent: Single cell
title: Cell Calling
nav_order: 6
---

## Cell Calling Documentation

There are two available methods for determining real cells.

### Knee Finding Method (default)

The knee finding method is the default method for determining real cells. It works by identifying the knee of the barcode rank plot based on [*UMI-tools*](https://github.com/CGATOxford/UMI-tools). 

### Percentile Method

The percentile method approximates real cells based on a percentile cutoff of UMI counts per cell. This method first identifies the X percentile (default 99) of UMI counts per cell, then applies a multiplier of X 10 to generate a cutoff threshold of UMI counts for real cells. 
