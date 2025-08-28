## Peak Annotation Enrichment
annotatePeaks.pl can also be used to analyze Peak Annotation Enrichment.

## Basic usage

annotatePeaks.pl <peak file | tss> <genome version>  [additional options...]

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

## Gene Ontology Analysis of Associated Genes

annotatePeaks.pl offers two types of annotation enrichment analysis.  The first is based on Gene Ontology classifications for genes.  The idea is that your regions (i.e. ChIP-Seq peaks) might be preferentially found near genes with specific biological functions.  By specifying "-go <GO output directory>", HOMER will take the list of genes associated with your regions and search for enriched functional categories, placing the output in the indicated directory.  This is just like looking for GO enrichment in a set of regulated genes (covered in greater detail here). 

There are some caveats to this type of analysis - for example, some genes are simply more likely to be considered in the analysis if there are isolated along the genome where they are likely to be the "closest gene" for peaks in the region.  But... since gene density roughly correlates with transcription factor density, it works itself out to some degree.  Other tools, such as GREAT attempt to deal with this problem to some degree, and are worth trying.  My experience is that the results are not that different once you factor in the fact that most other tools use "GO slims".

## Genome Ontology: Looking for Enriched Genomic Annotations
The Genome Ontology looks for enrichment of various genomic annotations in your list of peaks/regions.  Just as the Gene Ontology contains groups of genes sharing a biological function, the Genome Ontology contains groups of genomic regions sharing an annotation, such as TSS, or LINE repeats, coding exons, Transcription factor peaks, etc.  To run the Genome Ontology, add the option "-genomeOntology <output directory>".  A much more detailed description of the Genome Ontology will be available here (coming soon).

## More Options
```
    Primary Annotation Options:
        -mask (Masked repeats, can also add 'r' to end of genome name)
        -m <motif file 1> [motif file 2] ... (list of motifs to find in peaks)
            -mscore (reports the highest log-odds score within the peak)
            -nmotifs (reports the number of motifs per peak)
            -mdist (reports distance to closest motif)
            -mfasta <filename> (reports sites in a fasta file - for building new motifs)
            -fm <motif file 1> [motif file 2] (list of motifs to filter from above)
            -rmrevopp <#> (only count sites found within <#> on both strands once, i.e. palindromic)
            -matrix <prefix> (outputs a motif co-occurrence files:
                prefix.count.matrix.txt - number of peaks with motif co-occurrence
                prefix.ratio.matrix.txt - ratio of observed vs. expected  co-occurrence
                prefix.logPvalue.matrix.txt - co-occurrence enrichment
                prefix.stats.txt - table of pair-wise motif co-occurrence statistics
                additional options:
                -matrixMinDist <#> (minimum distance between motif pairs - to avoid overlap)
                -matrixMaxDist <#> (maximum distance between motif pairs)
            -mbed <filename> (Output motif positions to a BED file to load at UCSC (or -mpeak))
        -d <tag directory 1> [tag directory 2] ... (list of experiment directories to show
            tag counts for) NOTE: -dfile <file> where file is a list of directories in first column
        -bedGraph <bedGraph file 1> [bedGraph file 2] ... (read coverage counts from bedGraph files)
        -wig <wiggle file 1> [wiggle file 2] ... (read coverage counts from wiggle files)
        -p <peak file> [peak file 2] ... (to find nearest peaks)
            -pdist to report only distance (-pdist2 gives directional distance)
            -pcount to report number of peaks within region
        -vcf <VCF file> (annotate peaks with genetic variation infomation, one col per individual)
            -editDistance (Computes the # bp changes relative to reference)
            -individuals <name1> [name2] ... (restrict analysis to these individuals)
        -gene <data file> ... (Adds additional data to result based on the closest gene.
            This is useful for adding gene expression data.  The file must have a header,
            and the first column must be a GeneID, Accession number, etc.  If the peak
            cannot be mapped to data in the file then the entry will be left empty.
        -go <output directory> (perform GO analysis using genes near peaks)
        -genomeOntology <output directory> (perform genomeOntology analysis on peaks)
            -gsize <#> (Genome size for genomeOntology analysis, default: 2e9)
```
```
    Annotation vs. Histogram mode:
        -hist <bin size in bp> (i.e 1, 2, 5, 10, 20, 50, 100 etc.)
        The -hist option can be used to generate histograms of position dependent features relative
        to the center of peaks.  This is primarily meant to be used with -d and -m options to map
        distribution of motifs and ChIP-Seq tags.  For ChIP-Seq peaks for a Transcription factor
        you might want to use the -center option (below) to center peaks on the known motif
        ** If using "-size given", histogram will be scaled to each region (i.e. 0-100%), with
        the -hist parameter being the number of bins to divide each region into.
            Histogram Mode specific Options:
            -nuc (calculated mononucleotide frequencies at each position,
                Will report by default if extracting sequence for other purposes like motifs)
            -di (calculated dinucleotide frequencies at each position)
            -histNorm <#> (normalize the total tag count for each region to 1, where <#> is the
                minimum tag total per region - use to avoid tag spikes from low coverage
            -ghist (outputs profiles for each gene, for peak shape clustering)
            -rm <#> (remove occurrences of same motif that occur within # bp)

    Peak Centering: (other options are ignored)
        -center <motif file> (This will re-center peaks on the specified motif, or remove peak
            if there is no motif in the peak.  ONLY recentering will be performed, and all other
            options will be ignored.  This will output a new peak file that can then be reanalyzed
            to reveal fine-grain structure in peaks (It is advised to use -size < 200) with this
            to keep peaks from moving too far (-mirror flips the position)
        -multi (returns genomic positions of all sites instead of just the closest to center)
```
```
    Advanced Options:
        -len <#> / -fragLength <#> (Fragment length, default=auto, might want to set to 0 for RNA)
        -size <#> (Peak size[from center of peak], default=inferred from peak file)
            -size #,# (i.e. -size -10,50 count tags from -10 bp to +50 bp from center)
            -size "given" (count tags etc. using the actual regions - for variable length regions)
        -log (output tag counts as log2(x+1+rand) values - for scatter plots)
        -sqrt (output tag counts as sqrt(x+rand) values - for scatter plots)
        -strand <+|-|both> (Count tags on specific strands relative to peak, default: both)
        -pc <#> (maximum number of tags to count per bp, default=0 [no maximum])
        -cons (Retrieve conservation information for peaks/sites)
        -CpG (Calculate CpG/GC content)
        -ratio (process tag values as ratios - i.e. chip-seq, or mCpG/CpG)
        -nfr (report nuclesome free region scores instead of tag counts, also -nfrSize <#>)
        -norevopp (do not search for motifs on the opposite strand [works with -center too])
        -noadj (do not adjust the tag counts based on total tags sequenced)
        -norm <#> (normalize tags to this tag count, default=1e7, 0=average tag count in all directories)
        -pdist (only report distance to nearest peak using -p, not peak name)
        -map <mapping file> (mapping between peak IDs and promoter IDs, overrides closest assignment)
        -noann, -nogene (skip genome annotation step, skip TSS annotation)
        -homer1/-homer2 (by default, the new version of homer [-homer2] is used for finding motifs)
```

