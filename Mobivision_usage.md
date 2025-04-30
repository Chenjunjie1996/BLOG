## Mobivision v2.1 download
```
wget -c -O mobivision-v2.1.tar.gz "https://a.mobidrop.vip:8443/download/dataset/software/MobiVision/MobiVision%20v2.1.zip"
tar –xzvf mobivision-v2.1.tar.gz
```

## GRCh38 genome download
```
axel -n 5 https://a.mobidrop.vip:8443/download/dataset/software/RNA/GRCh38.zip 
```

## Usage
```
$ mobivision --help
mobivision-v2.1
Process MobiDrop Single Cell Sequencing data


USAGE:
    mobivision <SUBCOMMAND>


FLAGS:
    -h, --help     Print help information
    -V, --version  Print version information

SUBCOMMANDS:

    quantify      Count gene expression reads from a single sample
    mkindex       Prepare a reference for mobivision software
    fmt_gtf       Format and filter gene type features in annotation GTF file
    rename        Rename the sample_id of output files
    mtx2csv       Convert the matrix files to csv
    re_call_cell  Re do cell-calling using the results from mobivision quantify analysis
    vdj           Analysis single-cell V(D)J sequencing data
```

## scRNA analysis
```
 mobivision quantify -i /SGRNJ06/randd/Shengxin_train/yangjing/MobiVision/ref/GRCh38/GRCh38 \
        -t 12 \
        -f /SGRNJ06/randd/Shengxin_train/yangjing/MobiVision/analysis/fastq/homoPBMC/human-PBMC/221208-FXX-Z5-C03 \
        -o /SGRNJ06/randd/Shengxin_train/yangjing/MobiVision/analysis/221208-FXX-Z5-C03_S1_L002

-i 参考序列文件路径；
-t 使用线程数；
-f fastq文件路径；
-o 输出文件路径；
--intron 默认包含内含子；
--cellnumber 强制细胞数；
--cr2.2 过滤算法，默认EmptyDrops ；
--kit

```
