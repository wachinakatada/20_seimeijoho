## バクテリアゲノム機能解析（2020年12月26日）

## 伊藤通浩（熱帯生物圏研究センター・分子生命科学研究施設）

<img src="https://raw.githubusercontent.com/wachinakatada/20_seimeijoho/main/03_Ito/fig/201226_fig.jpg" width="1000">

> ①-1のみローカル（各自のPC）で行います。①-1終了後、sshで共有サーバに接続して下さい。

### ⓪ condaを用いてfastqcをWindows for LinuxまたはMacにインストール

```
$ conda install fastqc
```



### ①-1 Raw dataのquality確認 [fastqc] 

```
＃解析結果の出力フォルダの事前作成が必要

# 形
$ mkdir [フォルダ名] 

# 例
$ mkdir fastqc_HQ_R1 


# 次にfastqcで解析

# 形
$ fastqc -o [outputのフォルダ名]/ [inputのファイル名] -t [数値]

# 例
$ fastqc -o fastqc_HQ_R1/ HQ_R1.fastq.gz -t 2 
```



### ①-2 Raw dataのquality確認 [vsearch --fastq_eestats] 

```
# 形
$ vsearch --fastq_eestats [inputのファイル名(.fastq)] --output [outputのファイル名 (.txt)]

# 例
$ vsearch --fastq_eestats HQ_R1.fastq.gz --output eestats_HQ_R1.txt
```



### ② Read1(R1) とRead2 (R2) のmerge [vsearch --fastq_mergepaires]

```
# 形
$ vsearch --fastq_mergepairs [ファイル名 (R1, .fastq.gz)] \
-reverse [ファイル名 (R2, .fastq.gz)] \
--fastqout [マージされたファイル名( .fastq)] \
--fastqout_notmerged_fwd [マージされなかったファイル名 (R1,.fastq)] \
--fastqout_notmerged_rev [マージされなかったファイル名 (R1,.fastq)] \
--threads [数値] --fastq_allowmergestagger --fastq_minovlen [数値] --fastq_maxdiffs [数値]

# 例
$ vsearch --fastq_mergepairs HQ_R1.fastq.gz \
-reverse HQ_R2.fastq.gz \
--fastqout merged_HQ.fastq \
--fastqout_notmerged_fwd notmerged_fwd_HQ.fastq \
--fastqout_notmerged_rev notmerged_rev_HQ.fastq \
--threads 2 --fastq_allowmergestagger --fastq_minovlen 10 --fastq_maxdiffs 10
```



### ③ Quality control [vsearch --fastxfilter]

```
# 形
$ vsearch --fastx_filter [ファイル名(.fastq)] \
--fastqout [QCされたファイル名(.fastq)] \
--fastaout [QCされたファイル名(.fasta)] \
--fastq_truncee [数値] --fastq_truncqual [数値] \

#マージされた配列をQC
# 例（c）
$ vsearch --fastx_filter merged_HQ.fastq \
--fastqout qc_merged_HQ.fastq \
--fastaout merged_HQ.fasta \
--fastq_truncee 0.5 --fastq_truncqual 10

#マージされた配列、マージされなかったR1配列、マージされなかったR2配列をまとめてQC
# 例（c-2）
$ vsearch --fastx_filter merged_HQ.fastq \
--fastqout qc_merged_HQ.fastq \
--fastaout merged_HQ.fasta \
--fastq_truncee 0.5 --fastq_truncqual 10 \
&&vsearch --fastx_filter notmerged_fwd_HQ.fastq \
--fastqout qc_notmerged_fwd_HQ.fastq \
--fastaout notmerged_fwd_HQ.fasta \
--fastq_truncee 0.5 --fastq_truncqual 10 \
&&vsearch --fastx_filter notmerged_rev_HQ.fastq \
--fastqout qc_notmerged_rev_HQ.fastq \
--fastaout notmerged_rev_HQ.fasta \
--fastq_truncee 0.5 --fastq_truncqual 10
```



### ④ Assembly [SPAdes]

```
# 形 (a)・(b)
$ spades.py \
-1 [R1のファイル名 (fastq.gz, fastq, fasta)] \
-2 [R2のファイル名 (fastq.gz, fastq, fasta)] \
--only-assembler -k auto -t [数値] --careful \
-o [outputのフォルダ名]

# 例 (a)
$ spades.py \
-1 HQ_R1.fastq.gz \
-2 HQ_R2.fastq.gz \
--only-assembler -k auto -t 2 --careful \
-o assembled_HQ


# 形 (c)・(d)
$ spades.py \
-1 [R1のファイル名 (fastq.gz, fastq, fasta)] \
-2 [R2のファイル名 (fastq.gz, fastq, fasta)] \
--merged [マージされたファイル名(fastq, fasta)]
--only-assembler -k auto -t [数値] --careful \
-o [outputのフォルダ名]

# 例 (c)
$ spades.py \
-1 notmerged_fwd_HQ.fasta \
-2 notmerged_rev_HQ.fasta \
--merged merged_HQ.fasta \
--only-assembler -k auto -t 2 --careful \
-o assembled_qc_merged_HQ

# 例 (d)
$ spades.py \
-1 notmerged_fwd_HQ.fastq \
-2 notmerged_rev_HQ.fastq \
--merged merged_HQ.fastq \
--only-assembler -k auto -t 2 --careful \
-o assembled_merged_HQ
```



### ⑤ Assemblyの評価 [quast]

```
＃複数のSPAdesのoutputを扱う場合は、outputのscaffolds.fastaの名前の変更を推奨

# 例(c)の場合
# scaffolds.fasta →　scaffolds_qc_merged_HQ.fasta

# 形
# outputのフォルダ名を指定しなくても自動的にフォルダは生成されるが、それぞれのフォルダ名の設定を推奨
$ quast.py [アセンブルされたファイル名 (.fasta)] -o [outputのフォルダ名]

# 例 (c)
$ quast.py scaffolds_qc_merged_HQ.fasta -o qc_merged_scaffolds

＃終了後、outputフォルダをフォルダごと各自のPCに移動させ、各自のPCにてhtmlファイルを参照する
```


### [補足] 解析ツールのインストール

vsearch
( https://github.com/torognes/vsearch )

cuadapt
( https://cutadapt.readthedocs.io/en/stable/ )

spades
( https://cab.spbu.ru/software/spades/ )

quast
( http://quast.sourceforge.net )

prodigal
( https://github.com/hyattpd/Prodigal )

blast
( https://blast.ncbi.nlm.nih.gov/Blast.cgi?PAGE_TYPE=BlastDocs&DOC_TYPE=Download ) 

rdp_classifier
( http://rdp.cme.msu.edu )
( https://sourceforge.net/projects/rdp-classifier/ )


#### condaを使ってのインストールもできる
参照: https://github.com/wachinakatada/20_seimeijoho/blob/main/01_Wachi/introduction.md

```
$ conda install vsearch
$ conda install cutadapt
$ conda install spades
$ conda install quast
$ conda install prodigal
$ conda install blast
$ conda install rdp_classifier
```

