# RNAseq解析チュートリアル

松波　雅俊（大学院医学研究科先進ゲノム検査医学講座）

2020年12月18日作成


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

今回は2グループ（それぞれ繰り返し3回）のシミュレーションで作ったデータを使います

```R
d <- read.table("https://raw.githubusercontent.com/wachinakatada/20_seimeijoho/main/02_Matsunami/SimData.txt",sep="\t",header=T,row.names=1)
```

中身確認

```R
#行列の先頭行
head(d)

#G1_rep1の列
d$G1_rep1

#1列目
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

出力結果

<img src="https://raw.githubusercontent.com/wachinakatada/20_seimeijoho/main/02_Matsunami/Results/DE.scatter.jpeg" width="500">


## 発現解析

正規化する

```R
#比較グループを設定
grp <- c("G1","G1","G1","G2","G2","G2")
D <- DGEList(d, group=grp)

#正規化
D <- calcNormFactors(D) 
```

MDS plotを書く

```R
jpeg('DE.MDS.jpeg')
#plotMDS(D, labels=grp)
plotMDS(D, labels=grp, xlim = c(-5, 5), ylim = c(-5, 5))
dev.off()
```

<img src="https://raw.githubusercontent.com/wachinakatada/20_seimeijoho/main/02_Matsunami/Results/DE.MDS.jpeg" width="300">

発現変動遺伝子の検出

```R
#common dispersionを計算
D <- estimateCommonDisp(D)

#moderated tagwise dispersionを計算
D <- estimateTagwiseDisp(D)

#exact test (正確確率検定)で発現変動遺伝子を検出
results <- exactTest(D,pair=c("G1","G2"))

#tmp <- topTags(results, n=nrow(results$table))
tmp <- topTags(results, n=NULL)

#結果をファイルに書き出す
write.table(tmp$table, "DE.results.tsv", sep="\t", quote=F)

#P < 0.05だった遺伝子のみの結果をファイルに書き出す
write.table(tmp[tmp$table$PValue <= 0.05,], "DE.results.p0.05.tsv", sep="\t", quote=F)
```

MA plotを書く （FDR < 0.05を赤で色分け）

```R
#発現変動遺伝子を定義
q.value <- p.adjust(results$table$PValue, method = "BH")
is.DEG <- (q.value < 0.05)
de.tags <- rownames(results$table)[is.DEG]

#MA plot
jpeg('201214DE.MA.jpeg')
plotSmear(results, de.tags = de.tags)
abline(h=c(-2,2),col="blue")
dev.off()
```

出力結果

<img src="https://raw.githubusercontent.com/wachinakatada/20_seimeijoho/main/02_Matsunami/Results/DE.MA.jpeg" width="500">


P < 0.05だった遺伝子でheat mapをかく

```R
#count dataからP < 0.05だった遺伝子のみ抽出
count <- transform(D$count, PValue=results$table$PValue)
count2 <- count[count$PValue <= 0.05,]
count2$PValue <- NULL

#cpmで正規化してplot
m <- as.matrix((cpm(count2)))
jpeg('201214DE.heatmap.jpeg',width = 1024, height = 768)
heatmap3(m, method="ward.D2", labRow="")
dev.off()
```

出力結果

<img src="https://raw.githubusercontent.com/wachinakatada/20_seimeijoho/main/02_Matsunami/Results/DE.heatmap.jpeg" width="500">



**お疲れ様です！！！**

