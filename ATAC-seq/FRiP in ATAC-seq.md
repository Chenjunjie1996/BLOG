# FRiP in ATAC-seq  
## Understanding, Interpreting, and Using FRiP Correctly

This document provides a **technical and practical overview** of the **FRiP (Fraction of Reads in Peaks)** metric in ATAC-seq and scATAC-seq analysis, including its definition, biological meaning, computational caveats, and best practices in production pipelines.

---

## What Is FRiP?

**FRiP (Fraction of Reads in Peaks)** is defined as:

> The fraction of mapped sequencing reads that overlap called peak regions.

Mathematically:

\[
FRiP = \frac{\text{Reads overlapping peaks}}{\text{Total mapped reads}}
\]

FRiP is widely used as a **signal-to-noise indicator** in ATAC-seq experiments.

---

## Biological Interpretation

ATAC-seq relies on the assumption that **Tn5 transposase preferentially inserts into open chromatin regions**.

- **High FRiP** → reads are concentrated in accessible chromatin
- **Low FRiP** → reads are dispersed across the genome (high background)

Thus, FRiP reflects **how well open chromatin regions explain the observed reads**.

---

## FRiP Is NOT Sequencing Depth

A common misconception is to treat FRiP as a proxy for sequencing depth.

| Metric | What it measures |
|------|------------------|
| Total reads / fragments | Sequencing depth |
| FRiP | Signal-to-noise ratio |

A dataset can have:
- High sequencing depth
- Low FRiP (poor library quality or high background)

---

## Typical FRiP Ranges

| Data type | Typical FRiP |
|---------|--------------|
| Bulk ATAC-seq | 0.3 – 0.6 |
| scATAC-seq (pseudo-bulk) | 0.15 – 0.4 |
| scATAC-seq (single cell) | Highly unstable (not recommended) |

> **Note:** FRiP should not be interpreted at the single-cell level.

---

## Relationship Between FRiP and TSS Enrichment

| Metric | Focus |
|------|------|
| FRiP | Global enrichment in peaks |
| TSS enrichment | Functional enrichment at promoters |

Although correlated, **FRiP and TSS enrichment capture different aspects of data quality** and should always be evaluated together.

---

## FRiP Is Peak-Set Dependent

FRiP does **not solely measure data quality**. It also depends on:

- Peak calling algorithm
- Peak number
- Peak width
- Peak calling thresholds

### Important Consequence

> **FRiP is a model-dependent metric.**

Increasing the number or width of peaks will almost always increase FRiP — even if biological signal does not improve.

---

## Comparability Caveat

FRiP values are **only comparable when the same peak set is used**.

Invalid comparisons include:
- Different peak callers (MACS2 vs Genrich)
- Different peak parameters
- Different reference peak sets

---

## FRiP in scATAC-seq

### Why Single-Cell FRiP Is Discouraged

- Each cell contains very few reads
- Peaks are defined at pseudo-bulk level
- FRiP shows extreme variance per cell

As a result, **single-cell FRiP has little QC value**.

---

### Recommended Usage in scATAC-seq

FRiP should be computed and interpreted at:
- Sample-level pseudo-bulk
- Cluster-level pseudo-bulk

Not at the individual cell level.

---

## FRiP in Common Toolchains

### ArchR

- FRiP is computed using **reproducible peak sets**
- Typically evaluated at sample or cluster level
- Used as a **QC summary metric**, not a cell filter

### Cell Ranger ATAC (10x Genomics)

- FRiP is calculated using 10x internal peak definitions
- Reported in sample-level QC summaries
- Not directly comparable to ArchR FRiP

---

## Best Practices

- Always report peak calling parameters  
- Fix the peak set before comparing FRiP across samples  
- Combine FRiP with TSS enrichment and fragment size QC  
- Use FRiP for **sample-level**, not cell-level, filtering  

- Do not optimize peak calling solely to maximize FRiP  
- Do not compare FRiP across different peak definitions  

---

## Recommended QC Context

A healthy ATAC-seq dataset typically satisfies:

| Metric | Recommended |
|------|-------------|
| FRiP (scATAC pseudo-bulk) | > 0.2 |
| TSS enrichment | > 6 |
| Fragment size periodicity | Clear nucleosomal pattern |
| Mitochondrial fraction | Low |

---

## Key Takeaway

> **FRiP does not measure read quality.  
> It measures how well the chosen peak model explains the reads.**

FRiP should be treated as a **context-aware, peak-dependent QC metric**, not a universal indicator of data usability.

---

## References

- ENCODE ATAC-seq QC Guidelines  
- ArchR documentation  
- 10x Genomics Cell Ranger ATAC documentation  

---

## Possible Extensions

- FRiP vs TSS enrichment: which matters more?
- QC decision trees for scATAC-seq
- FRiP behavior under different peak calling strategies
- Benchmarking FRiP across ArchR, Signac, and Cell Ranger ATAC
