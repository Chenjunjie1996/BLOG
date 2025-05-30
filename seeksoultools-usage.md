## seeksoultools v2.1 download
```
mkdir seeksoultools.1.2.0
cd seeksoultools.1.2.0
wget -c -O seeksoultools.1.2.0.tar.gz "https://seekgene-public.oss-cn-beijing.aliyuncs.com/software/seeksoultools/seeksoultools.1.2.0.tar.gz"
```

```
# decompress
tar zxf seeksoultools.1.2.0.tar.gz

# install
source ./bin/activate
./bin/conda-unpack

# export path in bashrc
export PATH=`pwd`/bin:$PATH
echo "export PATH=$(pwd)/bin:\$PATH" >> ~/.bashrc
seeksoultools --version
```

## GRCh38 genome download
```
wget -c -O GRCh38.tar.gz "https://seekgene-public.oss-cn-beijing.aliyuncs.com/software/data/reference/GRCh38.tar.gz"
# decompress
tar -zxvf GRCh38.tar.gz
```

## Usage
```
$ seeksoultools --help

Commands:
  fast   quantifies singlecell gene expression
  rna    quantifies singlecell gene expression
  utils  utils.
  vdj    assembl and annotate vdj sequence.
```

## scRNA analysis
```
seeksoultools rna run \
--fq1 /path/to/demo_dd/demo_dd_S39_L001_R1_001.fastq.gz \
--fq2 /path/to/demo_dd/demo_dd_S39_L001_R2_001.fastq.gz \
--samplename demo_dd \
--genomeDir /path/to/GRCh38/star \
--gtf /path/to/GRCh38/genes/genes.gtf \
--chemistry DDV2 \
--core 4 \
--include-introns

–fq1 R1 fastq数据路径。

–fq2 R2 fastq数据路径。

–samplename 样本名称，会在outdir目录下创建以样本名称命名的目录。仅支持数字，字母和下划线。

–outdir 结果输出目录。默认值：./。

–genomeDir STAR构建的参考基因组路径, 版本需要与SeekSoulTools使用的STAR一致。

–gtf 相应物种的gtf路径。

–core 分析使用的线程数。

–chemistry
试剂类型，每种对应一组--shift、--pattern、 --structure、--barcode和--sc5p的组合，可选值：DDV2，DD5V1，MM，MM-D；
DDV2 对应SeekOne® DD单细胞3’转录组试剂盒；
DD5V1 对应SeekOne® DD单细胞5’转录组试剂盒；
MM 对应SeekOne® MM单细胞转录组试剂盒；
MM-D 对应SeekOne® MM大孔径高通量转录组试剂盒

–skip_misB barcode不允许碱基错配，默认允许一个碱基错配。

–skip_misL linker不允许碱基错配，默认允许一个碱基错配。

–skip_multi 舍弃能纠错为多个白名单barocde的reads，默认纠错为比例最高的barcode。

–expectNum 预估的捕获细胞数目。

–forceCell 当正常分析得到的细胞数⽬不理想时，选⽤此参数，后⾯加⼀个预期的数值N，SeekSoulTools软件会按照UMI从⾼到低取前N个细胞。

–include-introns 不启用时，只会选择exon reads⽤于定量；启用时，intron reads也会⽤于定量。

–star_path 指定其他版本的STAR路径进行比对，版本需要与--genomeDir版本兼容，默认的--star_path为环境下的STAR。

```
