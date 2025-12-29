## scATAC-seq Analysis with ArchR
A production-ready pipeline for single-cell ATAC-seq analysis using ArchR, covering dimensionality reduction, clustering, peak calling, motif analysis, and RNA–ATAC integration. ArchR is a mature and scalable framework designed specifically for scATAC-seq analysis. This repository documents a full ArchR-based workflow, from raw fragment files to regulatory inference.

## Key Features.
- Works with paired heavy/light chains or single chains (e.g., nanobodies).
- Input = amino acid sequence(s).
- Disk-backed data storage via Arrow (HDF5) files
- TF-IDF + LSI dimensionality reduction
- Iterative clustering strategy for robust representations
- Pseudo-bulk peak calling with MACS2
- Motif enrichment and TF activity inference (chromVAR)
- Gene score–based RNA–ATAC integration

## Data Requirements
```
ArchR requires fragment files as the only mandatory input:
sample1/fragments.tsv.gz
sample2/fragments.tsv.gz

Optional:
scRNA-seq Seurat object for integration
Cell metadata
```

## Install
R dependencies
```
install.packages("BiocManager")
BiocManager::install(c("ArchR", "chromVAR"))
```
External tools required:
```
MACS2
bedtools
samtools
```

## Workflow
### Create Arrow Files
```
library(ArchR)

addArchRGenome("hg38")
addArchRThreads(threads = 16)

inputFiles <- c(
  sample1 = "sample1/fragments.tsv.gz",
  sample2 = "sample2/fragments.tsv.gz"
)

ArrowFiles <- createArrowFiles(
  inputFiles = inputFiles,
  sampleNames = names(inputFiles),
  minTSS = 4,
  minFrags = 1000
)

```
### Initialize ArchR Project
Antibody renumbering requires installation of AbNumber. To install AbNumber, run the following command:
```
proj <- ArchRProject(
  ArrowFiles = ArrowFiles,
  outputDirectory = "ArchRProject"
)

proj <- filterDoublets(proj)

```
### Quality Control
```
Key QC metrics:
TSS enrichment (signal-to-noise ratio)
Number of fragments per cell
FRiP (fraction of reads in peaks)

Typical thresholds for 10x scATAC:
TSS enrichment > 6
Fragments > 3,000
```
### Dimensionality Reduction with LSI
ArchR applies TF-IDF normalization followed by SVD (LSI). Iterative LSI improves signal separation by refining clusters across iterations.
```
proj <- addIterativeLSI(
  ArchRProj = proj,
  useMatrix = "TileMatrix",
  name = "IterativeLSI",
  iterations = 2,
  clusterParams = list(
    resolution = c(0.2),
    sampleCells = 10000
  )
)
```
### Clustering and Visualization
Clusters reflect chromatin accessibility programs, which may differ from RNA-based cell types.
```
proj <- addClusters(
  input = proj,
  reducedDims = "IterativeLSI",
  method = "Seurat",
  resolution = 0.8
)

proj <- addUMAP(
  ArchRProj = proj,
  reducedDims = "IterativeLSI"
)
```
### Peak Calling
Single-cell ATAC signals are aggregated into pseudo-bulk profiles prior to peak calling. Peak calling is performed using MACS2 with reproducibility filtering.
```
proj <- addGroupCoverages(
  ArchRProj = proj,
  groupBy = "Clusters"
)

proj <- addReproduciblePeakSet(
  ArchRProj = proj,
  groupBy = "Clusters"
)
```
### Motif Analysis and TF Activity
This estimates transcription factor activity based on motif accessibility rather than gene expression.
```
# Motif annotation
proj <- addMotifAnnotations(
  ArchRProj = proj,
  motifSet = "cisbp"
)
# chromVAR deviations
proj <- addBgdPeaks(proj)
proj <- addDeviationsMatrix(proj)
```
### Gene Score Matrix
Gene scores approximate gene activity by integrating promoter and distal regulatory regions using distance-weighted models.
```
proj <- addGeneScoreMatrix(proj)
```

### RNA–ATAC Integration
This performs cross-modality label transfer between scRNA-seq and scATAC-seq data.
```
proj <- addGeneIntegrationMatrix(
  ArchRProj = proj,
  useMatrix = "GeneScoreMatrix",
  matrixName = "GeneIntegrationMatrix",
  reducedDims = "IterativeLSI",
  seRNA = seRNA,
  addToArrow = TRUE
)
```

## 
https://www.archrproject.com
https://www.nature.com/articles/s41588-021-00790-6
