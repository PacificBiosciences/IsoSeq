---
layout: default
parent: Single cell
title: Dedup FAQ
nav_order: 5
---

## Dedup FAQ

This FAQ explains how `isoseq3 dedup` identifies that two UMIs (+cell barcodes)
are likely to stem from the same founder molecule.

Following two parameters control the tresholds for comparison:
```
  --max-tag-mismatches      INT   Maximum number of mismatches between tags. [1]
  --max-tag-shift           INT   Tags may be shifted by at maximum of N bases. [1]
```

If your UMI (+cell barcode) design is very short, default parameters might
lead to overclustering. In this case, please adjust parameters accordingly.

Following an example of one founder molecule that is sequenced twice. PCR and
sequencing errors are introduced, leading to a clipped base in one of the cell
barcodes and a substitution in the other cell barcode.

<img src="../img/isoseq-dedup-faq.png"/>
