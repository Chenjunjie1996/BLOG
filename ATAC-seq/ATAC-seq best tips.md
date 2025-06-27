* Perform raw read QC using FASTQC before alignment
* Perform post-alignment QC using ATACSeqQC
* Perform peak calling using a peak caller, such as MACS2 in narrowdPeak mode with option settings: “shift -s and extend 2s”, Genrich, or HMMRATAC.
* Perform post-peak calling QC
  * Annotate peaks and generate peak distribution among genomic features using ChIPpeakAnno
  * Obtain functions of genes associated to peaks using the Genomic Regions Enrichment of Annotations Tool

Reference: https://haibol2016.github.io/ATACseqQCWorkshop/articles/ATACseqQC_workshop.html
