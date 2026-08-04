# SpaCir-VDJ: High-Fidelity Spatial Immune Repertoire Profiling via Circularization & Automated Bioinformatics

> **Paper Title**: *SpaCir-VDJ: a broadly compatible circularization strategy for spatial immune repertoire profiling*
> 
> **Preprint DOI**: `10.64898/2026.04.26.720528`
> 
> **Keywords**: Spatial Transcriptomics, Immune Repertoire (TCR/BCR), Single-Stranded DNA Circularization, Clonotype Calling, Somatic Hypermutation (SHM), Lineage Reconstruction

---

## 📌 1. The Core Bottleneck: Why Is Spatial Immune Repertoire Hard to Map?

In adaptive immunity, the receptor diversity of T cells (TCR) and B cells (BCR) generated through V(D)J recombination governs antigen recognition specificity. While single-cell V(D)J sequencing (e.g., 10x 5' V(D)J) pairs receptor sequences with single-cell transcriptomes, **tissue dissociation irreversibly destroys the spatial microenvironmental context**.

When attempting to profile immune repertoires within spatial transcriptomics (ST) platforms (such as 10x Visium 3' capture), two major engineering bottlenecks arise:

1. **The Spatial Distance Bottleneck ($1 \sim 2\text{ kb}$ Separation)**:
* **Spatial Barcode + UMI** reside at the $3'$ end of cDNA.
* **CDR3 hypervariable regions** (which dictate antigen specificity) reside near the $5'$ end.
* Standard short-read sequencers (e.g., Illumina PE150) cannot bridge this $1\sim2\text{ kb}$ gap in a single read pair.


2. **Low Abundance & False-Positive Risks**:
* TCR/BCR transcripts constitute a tiny fraction of total cellular RNA.


* Existing solutions rely either on long-read sequencing (PacBio/Nanopore)—whose high base-error rates generate false-positive clonotype calls—or on costly custom hybrid-capture probes with substantial bias.





---

## 🛠 2. Methodology: Physical Topology via "Single-Stranded Circularization"

**SpaCir-VDJ** resolves the distance constraint through **single-stranded DNA (ssDNA) circularization**, physically bringing the distal $3'$ Spatial Barcode into close proximity with the $5'$ V(D)J CDR3 region.

```
Linear cDNA (1–2 kb):  [Spatial Barcode + UMI] ------------ (1~2 kb) ------------ [CDR3 Area]
                                  │                                              │
                                  └────────────── Denaturation & Circularization ┘
                                                          │
Circular ssDNA Template:                        [Spatial Barcode]
                                                         │ (Physical Proximity)
                                                   [CDR3 Area]

```

### Key Library Construction Steps:

* **Multiplex PCR Enrichment**: Low-bias multiplex PCR primers targeting TCR $\alpha/\beta/\gamma/\delta$ and BCR $\text{IGH/IGK/IGL}$ constant regions ($C$ regions) selectively amplify low-abundance V(D)J transcripts from cDNA.


* **Single-Stranded DNA Circularization**: Amplified cDNA is denatured into single-stranded DNA and circularized using splint oligonucleotides and ligase.


* **Short-Read Paired-End Sequencing Strategy**:
* **Read 1**: Decodes the `Spatial Barcode` ($16\text{ bp}$) and `UMI` ($12\text{ bp}$).
* **Read 2**: Traverses the circular junction to read directly into the `V(D)J CDR3 region`.
* **Result**: Achieves $>99.5\%$ sequence accuracy at standard short-read sequencing costs without requiring long-read platforms.



---

## 💻 3. Bioinformatics Processing Pipeline

SpaCir-VDJ includes an end-to-end, automated computational pipeline engineered for parsing circular junction reads, spatial deconvolution, and high-fidelity clonotype calling.

```
                    SpaCir-VDJ Raw Sequencing Reads (PE150)
                                      │
                                      ▼
                      1. Read Preprocessing & Quality Control
                        (Cutadapt/Fastp Adapter Trimming)
                                      │
                                      ▼
                      2. Circularization Junction Parsing
                          (Splint Alignment & Splitting)
                       ┌──────────────┴──────────────┐
                       ▼                             ▼
               [Spatial Barcode + UMI]           [V(D)J Fragment]
                       │                             │
                       ▼                             ▼
        3. Barcode Matching & Deconvolution   4. V(D)J Local Assembly
         (Spatial Whitelist Match, Dist <= 1)   & IgBLAST / MixCR Mapping
                       │                             │
                       └──────────────┬──────────────┘
                                      ▼
                       5. High-Fidelity Clonotype Calling
                         (Spatial Barcode + CDR3 + V/J Gene)
                                      │
                                      ▼
                      6. Downstream Spatial & Clonal Analytics
               (Lineage Tree, SHM Quantification, Tumor Mutation Correlation)

```

### Detailed Computational Steps:

1. **Junction Parsing & Read Splitting**: Reads are scanned for the splint adapter sequence and partitioned into two functional modules:
* **Module A**: `[Spatial Barcode]` + `[UMI]`
* **Module B**: `[V(D)J CDR3 Sequence]`


2. **Spatial Barcode Deconvolution**: Extracted Spatial Barcodes are mapped against the tissue slide's spatial whitelist allowing a Levenshtein Distance $\le 1$ to tolerate sequencing errors, achieving $>90\%$ read utilization.
3. **V(D)J Alignment & Clonotype Calling**: Separated V(D)J reads are aligned against IMGT databases using **IgBLAST** or **MixCR**. In-frame CDR3 amino acid sequences and V/D/J gene segments are extracted to define the clonotype:

$$\text{Clonotype ID} = \{\text{V-Gene}\} + \{\text{CDR3}_{\text{AA}}\} + \{\text{J-Gene}\}$$



---

## 🔬 4. Analytical Applications & Algorithmic Highlights

### 1. Inter-GC Clonal Redistribution Inference

In human lymphoid tissues (tonsils and lymph nodes), SpaCir-VDJ constructed spatially annotated phylogenetic lineage trees. By evaluating shared clonal lineages and mutation trajectories across distinct germinal centers (GCs), the spatial evolutionary model demonstrated that **GCs are not fully isolated reaction units, but engage in active inter-GC clonal redistribution**.

### 2. Spatial SHM Quantification & Key Regulator Discovery

The somatic hypermutation (SHM) rate is quantified per spatial spot:


$$\text{SHM Rate} = \frac{\text{Mutation Count in V-Gene Segment}}{\text{Total Length of V-Gene Segment}}$$

Integrating spatial SHM maps with whole-transcriptome expression profiles via spatial co-expression analysis identified novel SHM regulators. Functional knockdown (shRNA) in Ramos B cells validated these predictions:

| Candidate Gene | Inferred Spatial Role | Knockdown Validation in Ramos B Cells |
| --- | --- | --- |
| **MDM4** | Positively correlated with high-SHM niches

 | Significant reduction in SHM rate upon KD

 |
| **LYN** | Key kinase in BCR signaling pathway

 | Marked reduction in SHM rate upon KD

 |
| **EAF2** | GC B cell-specific transcription factor associate

 | Blocked high-mutation phenotype upon KD

 |
| **H2AFY** | Chromatin remodeling / Epigenetic factor

 | Substantial decrease in SHM frequency upon KD

 |

### 3. Spatial Tumor Mutational Burden vs. TCR Diversity Modeling

In gastric cancer tissue, SpaCir-VDJ modeled the spatial relationship between ST-derived local tumor mutational burden (Cumulative Tumor Mutations) and local T-cell receptor diversity ($\text{Shannon Entropy} / \text{Richness}$). The analysis revealed a strong positive correlation, demonstrating spatial T-cell lineage remodeling driven by local tumor mutational niches.

---

## 📊 5. Technology Benchmarking

| Feature / Metric | Single-Cell V(D)J | Spatial VDJ (Probe + Long-Read) | **SpaCir-VDJ (This Work)** |
| --- | --- | --- | --- |
| **Spatial Resolution** | ❌ None (Dissociated)

 | ✅ Spot-level

 | **✅ Spot-level (Broad ST compatibility)**<br> |
| **Sequencing Platform** | Illumina Short-Read | PacBio / Nanopore Long-Read

 | **✅ Short-Read Illumina / MGI (Cost-Effective)**<br> |
| **Library Cost** | Moderate | Very High (Custom probes)

 | **✅ Low (Multiplex PCR + Circularization)**<br> |
| **Sequence Accuracy** | High ($>99\%$) | Lower (High error rate)

 | **✅ High ($>99.5\%$, zero false clonotypes)**<br> |
| **Downstream Analytics** | Single-cell RNA linkage | Basic clonal mapping

 | **✅ Lineage Trees, SHM Mapping, TME Modeling**<br> |

---

## 📜 Summary

**SpaCir-VDJ** uses **physical circularization** to solve the fundamental limitation of short-read spatial transcriptomics. Combined with an automated computational pipeline, it offers a scalable, low-cost, and accurate framework for interrogating immune clonal evolution, germinal center dynamics, and tumor microenvironment remodeling.