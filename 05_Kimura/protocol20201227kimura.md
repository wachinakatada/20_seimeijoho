# 集団遺伝学的解析（2020年12月27日）

木村亮介（医学研究科）

2020年12月19日作成

2020年12月20日加筆 (markdown版の作成, condaでのインストール・RパッケージのBioconductorでのインストールなど: 和智仲是）


## 0) 事前の準備

### SSH接続ツール

Tera TermまたはPuTTYのインストール(Windows)

### ファイル転送ツール

WinSCP(Windows)またはCyberduckのインストール

### GUI解析ツール 

#### SplitTree4

( https://software-ab.informatik.uni-tuebingen.de/download/splitstree4/welcome.html )

以下のソフトウェアのインストールはサーバーを使う場合には不要

### Rおよび必要なパッケージ

#### パッケージのインストール
```
> if (!requireNamespace("BiocManager", quietly = TRUE))
    install.packages("BiocManager")

> BiocManager::install("SNPRelate")
> BiocManager::install("SeqArray")
> BiocManager::install("gdsfmt")
> BiocManager::install("phangorn")
```

### 解析ツール

#### Plink

( https://www.cog-genomics.org/plink/ )

#### ADMIXTURE

( http://dalexander.github.io/admixture/index.html )

#### EIGENSOFT

( https://reich.hms.harvard.edu/software )


これらについてはcondaで導入が可能

```
$ conda install plink
$ conda install admixture
$ conda install eigensoft
```



## 1) 準備

### 1-1) 計算機サーバへの接続
http://xxx.xxx.u-ryukyu.ac.jp/ を参照

### SSH接続ツール (Tera Termなど) および ファイル転送ツール (WinSCPなど) を起動

ホスト： xxx.xxx.u-ryukyu.ac.jp

ユーザ名、passを入力

### 1-2) テストファイルディレクトリのコピー
```
$ cp -r /mnt/bioInfo2020_share/test20201227kimura test20201227kimura
```

### 1-3) ディレクトリの移動
```
$ cd test20201227kimura
```

### 1-4) ファイルをみる
```
$ ls
```

### 1-5) ファイルの中身を閲覧
```
# ↑↓で移動、qで停止
$ less merged.vcf.gz
```

## 2) 遺伝距離行列を計算して系統ネットワークを作ろう

### 2-1) Rの起動
```
$ R
```

### 2-2) パッケージの呼び出し
```
> library("SNPRelate")
> library("SeqArray")
> library("gdsfmt")
> library("phangorn")
```

### 2-2) VCFファイルからGDSフォーマットへの変換
```
> vcf.fn <- "merged.vcf.gz"
> snpgdsVCF2GDS(vcf.fn,"basic.gds",method="copy.num.of.ref")
> snpFromVCFtoGDS <- snpgdsOpen("basic.gds")
```

### 2-3) VCFファイルからGDSフォーマットへの変換
```
> dissMatrix  <-  snpgdsDiss(snpFromVCFtoGDS, autosome.only=FALSE, remove.monosnp=FALSE, missing.rate=NaN, num.thread=4, verbose=TRUE)
```

### 2-4) NEXUSファイルの作成
```
> sampleNumber <- length(read.gdsn(index.gdsn(snpFromVCFtoGDS, "sample.id")))

> sampleNameList <- sapply(1:sampleNumber, function(x){strsplit(read.gdsn(index.gdsn(snpFromVCFtoGDS,"sample.id")), split="-M3")[[x]][1]})

> outputDist <- dissMatrix$diss
> rownames(outputDist) <- sampleNameList
> colnames(outputDist) <- sampleNameList
> phangorn::writeDist(outputDist, file=paste(vcf.fn, ".nj.nex", sep=""), format="nexus")
```

### 2-5) Rの終了
```
> q()
```

### 2-6) SplitTreeを起動し、merged.vcf.gz.nj.nexをopen

neibour-netネットワーク図が出たことを確認

TreesタブからNJを選びApply


## 3) ADMIXTURE解析をしよう

### 3-1) VCFファイルをBEDファイルに変換
```
$ plink --vcf merged.vcf.gz --keep-allele-order --make-bed --out merged
```

### 3-2) ADMIXTUREのラン
```
$ admixture merged.bed 2
```

### 3-3) bashを使ったADMIXTUREのラン(オプションでCVエラーを計算）(時間差）
```
$ bash ADMIXTURE.sh
```

**ADMIXTURE.sh**
```
#########################
#!/usr/bin/bash
for K in 2 3 4 5; 
do admixture --cv merged.bed $K | tee log${K}.out; done
#########################
```

### 3-4）Rを起動して図を描く
```
$ R
> tbl=read.table("merged.4.Q")
> png("k4.png", width = 600, height = 300)
> barplot(t(as.matrix(tbl)), col=rainbow(7), xlab="Individual #", ylab="Ancestry", border=NA)
> dev.off() 
> q()
```

### 実行結果
<img src="https://raw.githubusercontent.com/wachinakatada/20_seimeijoho/main/05_Kimura/Results/k4.png" width="600">

### 3-5) CVエラーの値
```
$ grep -h CV log*.out
```

## 4) 主成分分析を実行しよう

### 4-1) EIGENSOFTのダウンロード
```
$ git clone https://github.com/DReichLab/EIG.git
```

### 4-2) EIGENSOFTのインストール
```
$ cd EIG/src
$ make
$ make install
```

### 4-3) BEDファイルをEIGENSTRATフォーマット(GENOファイル)に変換
```
$ cd ../../
$ EIG/bin/convertf -p par.PED.EIGENSTRAT
```

**パスが通っている場合（共有サーバーやcondaでインストールをしている場合）**
```
$ convertf -p par.PED.EIGENSTRAT
```

**par.PED.EIGENSTRAT**
```
#########################
genotypename:    merged.bed
snpname:         merged.bim
indivname:       merged.fam
outputformat:    EIGENSTRAT
genotypeoutname: merged.geno
snpoutname:      merged.snp
indivoutname:    merged.ind
familynames:     NO
#########################
```

### 4-4) 集団情報を加えたindファイル（merged.pop.ind）を用意

**merged.pop.ind**
```
i0 U p1
i1 U p1
i2 U p1
i3 U p1
i4 U p1
i5 U p1
i6 U p1
.
.
.
```

### 4-4-2) パラメータファイルの準備

**par.smartpca**
```
#########################
genotypename:    merged.geno
snpname:         merged.snp
indivname:       merged.pop.ind
evecoutname:     pca_merged.evec
evaloutname:     pca_marged.eval
numoutevec:      5
#########################
```

### 4-5) smartpcaの実行(時間差）
```
$ EIG/bin/smartpca -p par.smartpca > pca_merged.log
```

**パスが通っている場合（共有サーバーやcondaでインストールをしている場合）**
```
$ smartpca -p par.smartpca > pca_merged.log
```

### 4-6) Rでプロットの作成
```
$ R
> fn = "pca_merged.evec"
> evec = read.table(fn, col.names=c("Sample", "PC1", "PC2", "PC3", "PC4", "PC5", "Pop"))
> png("PC1vsPC2.png", width = 600, height = 600)
> plot(evec$PC1, evec$PC2, col=factor(evec$Pop))
> legend("bottomright", legend=levels(factor(evec$Pop)), col=1:length(levels(factor(evec$Pop))), pch=20)
> dev.off()
```

### 実行結果
<img src="https://raw.githubusercontent.com/wachinakatada/20_seimeijoho/main/05_Kimura/Results/PC1vsPC2.png" width="600">

ハンズオンは以上になります。お疲れさまでした。
