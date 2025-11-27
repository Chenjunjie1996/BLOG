## Cellranger-vdj 常见问题
### Structure of VDJ transcript
```
5'UTR + Leader + FWR1 + CDR1 + FWR2 + CDR2 + FWR3 + CDR3 + FWR4 + Constant Region + 3'UTR + PolyA Tail
UTR: Untranslated region; FWR: Framework region; CDR: Complementarity determining region
```
### 克隆型中会存在多条链(e.g. 1 alpha chain and 2 beta chains)
```
A small fraction of T cells are known to express 2 alpha + 1 beta or 1 alpha + 2 beta chains.
Similarly, there is evidence that some B cells express more than 1 heavy or light chain.
```
https://www.science.org/doi/10.1126/science.8211163 \
https://pmc.ncbi.nlm.nih.gov/articles/PMC2191970/pdf/je18141587.pdf \
https://www.nature.com/articles/s41421-019-0137-3 \
https://journals.aai.org/jimmunol/article/185/7/3801/83133/Antigen-Receptor-Allelic-Exclusion-An-Update-and
### What should I do when I get more than 1 chain of same type in the same cell?
https://kb.10xgenomics.com/s/article/360026995572-What-should-I-do-when-I-get-more-than-1-chain-of-same-type-in-the-same-cell
### 某一种克隆型占比非常高的原因
```
正常人的机体时刻在应对各种各样内源性或外源性的抗原，所以不管是BCR还是TCR都应该是多克隆的，单克隆往往提示病理状态（低多样性cdr3）
```
### D基因注释信息缺失的原因
```
Human and mouse D genes are short sequences that are often not very well annotated/represented in the reference
```
### 亚克隆型是什么
```
Exact subclonotype: A subset of cells within a clonotype that share identical immune receptor sequences
at the nucleotide level, spanning the entirety of the V, D, and J genes and the V(D)J junction.
Exact subclonotypes share the same V, D, J, and C gene annotations
(e.g. cells that have identical V(D)J sequences but different C genes or isotypes are split into distinct exact subclonotypes).
```
### cellranger-vdj 不同版本分析差异
```
Cell Ranger versions (3.0-) multi-chain clonotypes are available.

Cell Ranger version 3.1+ assigns low confidence to all contigs in cells that express more than 4 productive chains
they are not included in the output clonotypes.
```
### V,D,J 基因编码区域问题
```
Ig的V区位于重链（H链）和轻链（L链）的N端。编码H链V区的基因包含V、D、J三组基因片段；编码L链V区的基因包含V、J两组基因片段。
1. H链的V基因编码H链V区的CDR1及CDR2互补决定区、D基因编码大部分CDR3互补决定区、J基因编码剩余部分CDR3以及V区和C区的连接结构；
2. L链的V基因编码L链V区的CDR1、CDR2和大部分CDR3互补决定区、J基因编码剩余部分CDR3以及V区和C区的连接结构。
Ig V区是由V、（D）、J基因片段共同翻译出来的，V、（D）、J基因片段重排共同改变了Ig可变区的结构，从而影响BCR的多样性。
```
