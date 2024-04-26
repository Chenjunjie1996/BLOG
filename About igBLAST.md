## Igblast
IgBLAST was developed at the NCBI to facilitate the analysis of immunoglobulin and T cell receptor variable domain sequences.

IgBLAST allows users to view the matches to the germline V, D, J and C genes, details at rearrangement junctions, the delineation of IG V domain framework regions and complementarity determining regions. IgBLAST has the capability to analyse nucleotide and protein sequences and can process sequences in batches. Furthermore, IgBLAST allows searches against the germline gene databases and other sequence databases simultaneously to minimize the chance of missing possibly the best matching germline V gene.

IgBLAST is described at https://www.ncbi.nlm.nih.gov/pubmed/23671333

## Set-up
IgBlast program can be downloaded from (https://ftp.ncbi.nih.gov/blast/executables/igblast/release/LATEST ).

IgBlast allows you to search any germline databases of your choice (using -germline_db_V, -germline_db_J and -germline_db_D options).

The NCBI mouse germline gene databases (i.e., mouse_gl_V, etc.) are supplied on our FTP site (https://ftp.ncbi.nih.gov/blast/executables/igblast/release/database/. Also see https://www.ncbi.nlm.nih.gov/igblast/ about database details).

The germline gene databases from AIRR community (also known as Open Germline Receptor Database, OGRDB (https://ogrdb.airr-community.org/)) are also available on our FTP site (https://ftp.ncbi.nih.gov/blast/executables/igblast/release/database/airr/).

To search IMGT germline sequences, you need to download them from IMGT web site (http://www.imgt.org/vquest/refseqh.html#VQUEST ). You need to download all V, D and J sequences for whatever organisms you are interested in. Combine all V, all D and all J sequences, respectively, into separate files (i.e., one file for all V sequences, one for all D sequences and one file all for J sequences). After you have downloaded the sequences, invoke our utility tool edit_imgt_file.pl (in the bin directory in the IgBlast release package) to process these sequences (i.e., to change the long IMGT definition lines to germline gene names only). For example:
```
bin/edit_imgt_file.pl imgt_file > my_seq_file
```
Then you can use NCBI’s makeblastdb tool (also in the bin directory ) to make the blast database from the above output file. For example:
```
bin/makeblastdb -parse_seqids -dbtype nucl -in my_seq_file
```
Now you can use my_seq_file as blast database file for IgBlast.