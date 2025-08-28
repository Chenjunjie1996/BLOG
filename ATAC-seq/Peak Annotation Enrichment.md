## Peak Annotation Enrichment
annotatePeaks.pl can also be used to analyze Peak Annotation Enrichment.

## Basic usage
```
annotatePeaks.pl <peak file | tss> <genome version>  [additional options...]
```
```
Available Genomes (required argument): (name,org,directory,default promoter set).

User defined annotation files (default is UCSC refGene annotation):
  annotatePeaks.pl accepts GTF (gene transfer formatted) files to annotate positions relative
  to custom annotations, such as those from de novo transcript discovery or Gencode.

  -gtf <gtf format file> (-gff and -gff3 can work for those files, but GTF is better)

Peak vs. tss/tts/rna mode (works with custom GTF file):
  If the first argument is "tss" (i.e. annotatePeaks.pl tss hg18 ...) then a TSS centric
  analysis will be carried out.  Tag counts and motifs will be found relative to the TSS.
  (no position file needed) ["tts" now works too - e.g. 3' end of gene]
  ["rna" specifies gene bodies, will automaticall set "-size given"]
  NOTE: The default TSS peak size is 4000 bp, i.e. +/- 2kb (change with -size option)

  -list <gene id list> (subset of genes to perform analysis [unigene, gene id, accession,
  probe, etc.], default = all promoters)
  -cTSS <promoter position file i.e. peak file> (should be centered on TSS)
```
