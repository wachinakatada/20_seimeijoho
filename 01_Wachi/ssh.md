和智仲是（戦略的研究プロジェクトセンター）



2020年12月18日版



### 1. SSH接続ツールの導入

リモートログインクライアントを使ってSSHで共有サーバーへ接続する

#### 1.1. Windowsの場合

**1.1.1. WSL**

デスクトップの `Ubuntu` を起動

"host.server"のアドレスは当日お知らせします。

```
$ ssh [共有サーバーのアカウント名]@host.server
xxxxx@host.server's password: 
# [パスワードの入力]
Last login: Thu Dec 17 15:17:20 2020 from XX

[xxxxx@host.server ~]$

# 共有フォルダの確認
[xxxxx@host.server ~]$ ls /mnt/bioInfo2020_share
```



**1.1.2. Putty**

参照: https://www.ep.sci.hokudai.ac.jp/~epnetfan/tebiki/server-login/putty.html

参照: https://web.kudpc.kyoto-u.ac.jp/manual/ja/install/putty

```
login as: [共有サーバーのアカウント名]
xxxxx@host.server's password: 
# [パスワードの入力]

[xxxxx@host.server ~]$

# 共有フォルダの確認
$ ls /mnt/bioInfo2020_share
```



#### 1.2. Macの場合

**ターミナル**

`アプリケーション > ユーティリティ > ターミナル`

Dockに追加しておいたほうが便利



```
$ ssh [共有サーバーのアカウント名]@host.server
xxxxx@host.server's password: 
# [パスワードの入力]
Last login: Thu Dec 17 15:17:20 2020 from XX

[xxxxx@host.server ~]$

# 共有フォルダの確認
[xxxxx@host.server ~]$ ls /mnt/bioInfo2020_share
```



### 2. ファイル転送ツールの導入

共有フォルダの `sshLogin_ver1_1.pdf` 参照



#### 2.1. Windowsの場合

**WinSCP**

参照: https://www.media.hiroshima-u.ac.jp/services/web/login/ftp/winscp/



#### 2.2. Macの場合

#### （Windowsでもできるはず）

**Cyberduck**

参照: https://www.media.hiroshima-u.ac.jp/services/web/login/ftp/cyberduck/



**サーバ:** host.server（当日にお知らせします）

**ポート:** 22

**アカウント名:** [共有サーバーのアカウント名]

**パスワード:** [共有サーバーのパスワード]



