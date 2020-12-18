# RNAseq解析チュートリアル

松波　雅俊（大学院医学研究科先進ゲノム検査医学講座）

## Rの起動とデータ読み込み

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

ファイルは"RNAseq_class"ディレクトリに出力されます

```R
setwd("/home/hoge/RNAseq_class")
```

データの読み込み

```R
d <- read.table("https://raw.githubusercontent.com/wachinakatada/20_seimeijoho/main/02_Matsunami/SimData.txt",sep="\t",header=T,row.names=1)
```

中身確認

```R
#行列の先頭行
head(d)

#G1_rep1の列
d$G1_rep1

#1の列目
d[,1]

#1行目
d[1,]
```

## scatter plot

```R
#値が0だとエラーが出るので1を足す
G1_1 <- d$G1_rep1 + 1 
G1_2 <- d$G1_rep2 + 1 
G1_3 <- d$G1_rep3 + 1 
G2_1 <- d$G2_rep1 + 1 
G2_2 <- d$G2_rep2 + 1 
G2_3 <- d$G2_rep3 + 1 

#1 pair表示
plot(G1_1, G1_2, log="xy", col="black", pch=16, cex=0.5, xlim = c(1, 10000), ylim = c(1, 10000))
```

まとめて出力して"DE.scatter.jpeg"保存

```R
jpeg('DE.scatter.jpeg',width = 1024, height = 768)
par(mfrow=c(2,3))
par(ps = 20)
plot(G1_1, G1_2, log="xy", col="black", pch=16, cex=0.5, xlim = c(1, 10000), ylim = c(1, 10000))
plot(G1_2, G1_3, log="xy", col="black", pch=16, cex=0.5, xlim = c(1, 10000), ylim = c(1, 10000))
plot(G1_3, G1_1, log="xy", col="black", pch=16, cex=0.5, xlim = c(1, 10000), ylim = c(1, 10000))
plot(G2_1, G2_2, log="xy", col="black", pch=16, cex=0.5, xlim = c(1, 10000), ylim = c(1, 10000))
plot(G2_2, G2_3, log="xy", col="black", pch=16, cex=0.5, xlim = c(1, 10000), ylim = c(1, 10000))
plot(G2_1, G2_3, log="xy", col="black", pch=16, cex=0.5, xlim = c(1, 10000), ylim = c(1, 10000))
dev.off()
```



## 発現解析





