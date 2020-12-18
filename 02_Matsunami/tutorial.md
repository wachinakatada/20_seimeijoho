# Rの起動とデータ読み込み

作業ディレクトリの作成とRの起動

"/home/hoge/"の部分は解析環境に合わせて変えてください

```sh
cd /home/hoge/
mkdir RNAseq_class
R
```

使用するライブラリーの読み込み

```R
library(edgeR)
library(heatmap3)
```

作業ディレクトリの設定

"/home/hoge/"の部分は解析環境に合わせて変えてください

```R
setwd("/home/hoge/RNAseq_class")
```

データの読み込み

```R
d <- read.table("https://raw.githubusercontent.com/wachinakatada/20_seimeijoho/main/02_Matsunami/SimData.txt",sep="\t",header=T,row.names=1)
```

中身確認
#行列の先頭行
head(d)



