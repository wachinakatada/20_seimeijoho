## 「生命情報科学」講義資料

和智仲是（戦略的研究プロジェクトセンター）

2020年10月12日作成

2020年12月18日改訂


## 参考書

・**生命科学者のための Dr. Bono データ解析実践道場**

​	Github: https://github.com/bonohu/DrBonoDojo

・**生命科学データ解析を支える情報解析**



## 参考資料

[動画]

・統合TV: https://togotv.dbcls.jp



[講義資料など]

・**日本乳酸菌学会誌**: (2014 - 2020) 「次世代シーケンサーデータの解析法」

・**NGSハンズオン講習会2017**:  https://biosciencedbc.jp/event/ngs/2017.html



[細々したもの]

・WSL のインストール

​	http://www.iu.a.u-tokyo.ac.jp/~kadota/20190603_kadota.pdf



・conda のインストール

​	http://imamachi-n.hatenablog.com/entry/2017/01/14/212719 等



・TCC-GUI のインストール

​	https://github.com/swsoyee/TCC-GUI

​	インストールがうまく行かない場合はオンライン版を利用する

​	https://infinityloop.shinyapps.io/TCC-GUI/

・MEGA のインストール

​	https://www.megasoftware.net


## 0. Unix を使う方法

### 0.1. Mac の場合 (Unix)

#### 0.1.1. ターミナル

1. `ユーティリティ > ターミナル`

```
$ pwd
```

2. 適当な名前のディレクトリを作っておく

```
$ mkdir [半角英数字]
$ cd [半角英数字]
```



### 0.2. Windows の場合 (WSL)

#### 0.2.1. Ubuntu (うぶんとぅ)

1. `Windows システムツール > コントロールパネル `

`> プログラム > プログラムと機能 > Windowsの機能の有効化または無効化`



2. Windows Subsystem for Linux にチェックを入れる



3. Microsoft Store の検索窓に "WSL" と入力



4. Ubuntu (18.04) をインストール



5. ユーザー名とパスワードを設定

   ユーザー名は 半角小文字・スペースなし・記号なしにしたほうが無難 （例えば tryudai ）

   パスワードは忘れないように



6. rootのパスワードを設定しておく

```
$ sudo passwd root
[ユーザーのパスワード]
[rootのパスワード]
[rootのパスワードをもう一度]
```



7. Windows側の設定をする

7.0. カレントディレクトリを確認しておく

```
$ pwd
/home/[ユーザー名]
```



7.1. デスクトップに `wsl_wd` という名前のディレクトリを作成する。

```
$ mkdir /mnt/c/Users/[Windowsのアカウント名]/desktop/wsl_wd
```



7.2. WSLのホームディレクトリにリンクを貼る。

```
$ ln -s /mnt/c/Users/[Windowsのアカウント名]/desktop/wsl_wd /home/[ユーザー名]/wsl_wd
```



7.3. リンクが貼れているかどうかの確認

```
$ ls
wsl_wd
```



7.4. WSLのホームディレクトリでリンクが機能するかの確認

```
$ mkdir wsl_wd/test
$ cd wsl_wd
$ ls
test
```



7.5. デスクトップの `wsl_wd` に `test` ディレクトリができていることを確認する



（注）WSL上ではパスの確認が面倒。必要があれば、wslpathコマンドを使うかWindows terminalを使うなどして、パスを確認する必要がある。

​	1. wslpath コマンド

​	2. Windows terminal



### 1. 基本ツールの導入方法

#### 1.0. 基本的なコマンド

​	付録1を参照。



#### 1.x. GitHubの利用

Github（ぎっとはぶ）

```
$ git clone https://github.com/wachinakatada/20_seimeijoho
```



#### 1.1. Conda の導入

1. インストーラのダウンロード

**Macの場合**

```
$ curl -O https://repo.anaconda.com/miniconda/Miniconda3-latest-MacOSX-x86_64.sh

  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 53.2M  100 53.2M    0     0  6654k      0  0:00:08  0:00:08 --:--:-- 6536k
```



**Windowsの場合**

```
$ wget https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh

もしくは

$ curl -O https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh
```

**wgetやcurlが使えない場合**

ブラウザで直接ダウンロードする

2. インストールの実行

```
$ bash Miniconda3-latest-MacOSX-x86_64.sh
もしくは
$ bash Miniconda3-latest-Linux-x86_64.sh

Miniconda3 will now be installed into this location:

.../.../miniconda3
＃(注意) この後、必要になるのでメモをしておくこと

 \- Press ENTER to confirm the location

 \- Press CTRL-C to abort the installation

 \- Or specify a different location below
```



3. インストールができているか確認する

```
$ conda -h
```



**Macの場合**

```
$ which conda

$ source ~/.bash_profile

$ which conda
```
.bash_profileに書き込まれていれば、condaのパスが表示されるはず



【HELP】 

```
$ cd
$ vi .bash_profile
```

上記コマンドで vi が起動する。

i キーを押すと入力モードになる。

`.bash_profile`の最後の行に下記の行を付け足す。

```
＃Miniconda3, 2020.12.XX
export PATH="[condaのパス]/bin:$PATH"
```

esc キーを押して入力モードを終了する。

:wq と順に入力してエンターキー（上書き保存）を押す。

```
$ source ~/.bash_profile
$ conda init
```



**Windowsの場合**

```
$ which conda

$ source ~/.bashrc

$ which conda
```

.bashrcに書き込まれていれば、condaのパスが表示されるはず。



【HELP】

```
$ cd
$ vi .bashrc
```

上記コマンドで vi が起動する。

i キーを押すと入力モードになる。

`.bash_profile`の最後の行に下記の行を付け足す。

```
＃Miniconda3, 2020.12.XX
export PATH="[condaのパス]/bin:$PATH"
```

esc キーを押して入力モードを終了する。

:wq と順に入力してエンターキー（= 上書き保存）を押す。

```
$ source ~/.bashrc
$ conda init
```



4. よく使うチャンネルを追加しておく

```
$ conda config --add channels conda-forge
$ conda config --add channels defaults
$ conda config --add channels r
$ conda config --add channels bioconda
```



5. プログラムのインストールを行う

```
$ conda install sra-tools

$ conda install vsearch
$ conda install cutadapt
$ conda install spades
$ conda install quast
$ conda install prodigal
$ conda install blast
$ conda install rdp_classifier

$ conda install admixture
$ conda install eigensoft
```


#### 1.2. R の導入

```
$ conda install r==4.0
$ conda install pkg-config
```



1. パッケージのインストールを行う

```
# Rの起動
$ R

# Bioconductor のインストール
$ if (!requireNamespace("BiocManager", quietly = TRUE))
    install.packages("BiocManager")

# edgeR をインストールする
$ BiocManager::install("edgeR")

# heatmap3 をインストールする
$ BiocManager::install("heatmap3")

# インストールしたパッケージの読み込み
$ library(edgeR)
$ library(heatmap3)
```

R でミラーを聞かれた場合は 66: (other mirrors) > 21: Japan (Yonezawa)



X. Extra

```
# まとめてインストールすることもできる

# 必要なパッケージの一覧を作成する
libs <- c("shiny", "shinydashboard", "shinyWidgets", "plotly", "dplyr", "DT", "heatmaply", "tidyr","utils","rmarkdown","data.table","RColorBrewer", "knitr", "cluster", "shinycssloaders", "shinyBS", "MASS", "TCC")

# パッケージのインストール
for (i in libs){
  if( !is.element(i, .packages(all.available = TRUE)) ) {
     BiocManager::install(i, suppressUpdates=TRUE)
  }
}
```



## 付録1. Unixの基本的なコマンド



## 付録2. condaの基本的なコマンド

#### パッケージのインストール
```
conda install [パッケージ名]
```
#### パッケージのアンインストール
```
conda uninstall [パッケージ名]
```
#### インストールするパッケージの検索
```
conda search [パッケージ名]
# ワイルドカードも使える
```
#### インストールしたパッケージの一覧
```
conda list
```
#### 仮想環境の作成
```
conda create -n [環境名]

# pythonのバージョンの指定もできる
conda create -n [環境名] python=2.7
```
#### 仮想環境の利用
```
conda activate [環境名]
```
#### 仮想環境の終了
```
conda deactivate
```
#### 仮想環境の一覧
```
conda info -e
```
#### 仮想環境の削除
```
conda remove -n [環境名] --all
```
