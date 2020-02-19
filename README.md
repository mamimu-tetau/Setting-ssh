## コーディングを始めるためのパソコンのセットアップ
***
1. エディタのインストール(Brackets、SublimeText、Atom、DWなどなど)
2. [Macのプリインストールapacheの設定(静的サイト作成用)](https://github.com/mamimu-tetau/Setting-mac-apache)
3. [sshの設定(サーバ接続、Gitリモート接続用)](https://github.com/mamimu-tetau/Setting-ssh)
4. [nodeのインストール(Gulp用)](https://github.com/mamimu-tetau/Setting-Install-node)
5. [Gulpのインストール](https://github.com/mamimu-tetau/Setting-gulp)
6. [Sorcetreeのインストールと設定](https://github.com/mamimu-tetau/Setting-Sorcetree)
***
<br><br><br>

# 3.sshの設定(サーバ接続、Gitリモート接続用)
- [サーバとssh鍵交換するイメージ](#サーバとssh鍵交換するイメージ)
- [ssh鍵生成からサーバ設置](#ssh鍵生成からサーバ設置)
  - [ローカル側（Mac）で鍵を生成する](#ローカル側（Mac）で鍵を生成する)
  - [configファイルの作成・編集](#configファイルの作成・編集)
- [サーバに公開鍵を登録](#3.サーバに公開鍵を登録)
- [SFTPアプリの設定](#SFTPアプリの設定)
- [Gitクライアントとの接続](#Gitクライアントとの接続)
- [Gitクライアントとのサーバの接続](#Gitクライアントとのサーバの接続)
- [Permission denied (publickey)](#Permission-denied-(publickey))
- [再起動のたびにssh-addした鍵がクリアされる](#再起動のたびにssh-addした鍵がクリアされる)
<br /><br />

## サーバとssh鍵交換するイメージ
***
![ssh](https://github.com/mamimu-tetau/ssh/blob/master/ssh_infographic.png)


## ssh鍵生成からサーバ設置
***
<br /><br />

## 1.ローカル側（Mac）で鍵を生成する
ターミナルでの操作です。<br>

#### .sshディレクトリに移動
```
$ cd ~/.ssh
```
`/.ssh: No such file or directory`が出るようであれば.sshディレクトリが存在しないので作成します。
```
$ mkdir ~/.ssh
$ chmod 700 ~/.ssh
$ cd ~/.ssh
```
#### lsコマンドで中身を確認
名前が被って上書きとかされたらまずいので中身を確認
```
$ ls -la
```
何もなければ↓みたいなのが出るし、あればファイル名が並ぶ
```
total 0
drwx------   2 hacca  staff   64  2 17 11:17 .
drwxr-xr-x+ 27 hacca  staff  864  2 17 11:17 ..
```

#### 鍵生成（4096強度）
毎回作るのではなく基本的にはPC単位とかでいいんじゃないかな（鍵に名前はhaccaiMac_globalみたいにしとけば） <br />
パスフレーズはこの鍵を使うときに必要なので忘れないものに。パスワードみたいなもの。
```
$ ssh-keygen -t rsa -b 4096 -f (ここに鍵の名前カッコはいらん)

Generating public/private rsa key pair.
Enter passphrase (empty for no passphrase): パスフレーズ入力
Enter same passphrase again: パスフレーズ入力
Your identification has been saved in hogehoge.
Your public key has been saved in hogehoge.pub.
The key fingerprint is:
SHA256:kD6KpH40dL/lS8YgeSICbMjUeHI1SuQtM
The key's randomart image is:
+---[RSA 4096]----+
| .+o.o           |
|=oo+o ..         |
|+*++ .o          |
|++o..o..         |
|+.+..++oS        |
| =.+..+oo.       |
|. + E   ++       |
|.o O   .o.       |
| .* o    ..      |
+----[SHA256]-----+
```
鍵が生成されたか確認
```
$ ls -la
-rw-------   1 hacca  staff  3434  2 17 11:44 hogehoge
-rw-r--r--   1 hacca  staff   749  2 17 11:44 hogehoge.pub
```
こんな感じで出来てるはず<br />
Finderからも確認できる`Command + Shift +G`で移動先に`~/.ssh`で確認できる
```
-rw-------   1 hacca  staff  3434  2 17 11:44 hogehoge
-rw-r--r--   1 hacca  staff   749  2 17 11:44 hogehoge.pub
```
hogehoge（これが秘密鍵。絶対誰にも渡しちゃダメ！）<br />
hogehoge.pub（これが公開鍵。サーバとかに置くのはこっち）
<br /><br /><br /><br />

## 2.configファイルの作成・編集
毎回接続コマンド面倒なので.sshディレクトリ直下にconfigファイルを作成して、その中に接続先のホスト名やユーザー名、秘密鍵ファイルのパス等を全て記載しておきます。<br />

#### .sshディレクトリに移動
```
$ cd ~/.ssh
```
#### lsコマンドで中身を確認
configファイルがあれば`config`と表示され、なければ何も起こりません。
```
$ ls
```
configファイルがなければtouchコマンドで作成。viコマンド使えるなら作成から記述まで一気にやっちゃってください。
```
$ touch config
```
#### configファイルのパーミッションを設定
configファイルは誰かに編集されたら困るので、パーミッションを変えます。
```
$ chmod 600 ~/.ssh/config
```
#### configファイルの設定
Finderから`Command + Shift +G`で移動先に`~/.ssh`<br />
configファイルをエディターで開きます。<br />
Mac純正テキストエディターとかメモ帳は使わない！BracketとかSublimeTextとかCotEditorとかで。<br />
SublimeTextだとパスワードを入力すれば保存できましたが、その他エディタではできない模様。その際はファイル自体の権限に自分を追加していただく方法があります。自己責任でお願いします。
[権限追加](https://rensrv.com/macos/grant-of-auth-httpdconf/)
ホスト、ユーザー名、ポートとかはサーバの情報です。<br />
[configのサンプル](https://github.com/mamimu-tetau/ssh/blob/master/config)

```
Host hacca2（ホスト名）
 HostName sabasaba（サーバ名）
 User fugafuga（ユーザー名）
 IdentityFile ~/.ssh/hogehoge（秘密鍵の名前）
 IdentitiesOnly yes
 Port 10022
 AddKeysToAgent yes
 UseKeychain yes
```

|設定名|値|
|---|---|
|Host |ホスト名はなんでもいいわかりやすいの 増えるのでかぶらないように|
|HostName |サーバのホスト名やIPアドレス|
|User |サーバのログインユーザ名|
|IdentityFile |秘密鍵の絶対パス|
|Port |サーバのポートSSH、SFTP用|
|IdentitiesOnly |IdentityFileで指定した秘密鍵に限定するかどうか（yes/no）|
|UseKeychain |キーチェーンに追加（yes/no）|
|AddKeysToAgent |ssh agentに登録（yes/no）|

<br /><br /><br /><br />



## サーバに公開鍵を登録
<br /><br />
どなたかのサーバを使わせてもらう場合や「公開鍵くれや」って言われたときはconfigで登録した該当サーバの秘密鍵とペアの公開鍵（長い英数字の羅列）を渡します。<br />
初めてサーバに公開鍵を登録する場合はパスワードで一旦接続して公開鍵を登録し、パスワード認証を無効にしたりする方法があります。<br /><br />


#### サーバに手動で登録する
```
$ ssh -p (ポート) (ユーザ名)@(サーバ名)
$ cd  ~/.ssh
```
`not found`エラーで.sshディレクトリない場合は以下。
```
$ mkdir ~/.ssh
$ chmod 700 ~/.ssh
```
viコマンドで公開鍵を登録（1行に鍵一つ）
```
vi ~/.ssh/authorized_keys
```
最終行に公開鍵を公録して`:wq`で保存

`not found`エラーでauthorized_keysがない場合は以下。
```
$ touch ~/.ssh/authorized_keys
$ chmod 600 -/.ssh/authorized_keys
$ vi ~/.ssh/authorized_keys
```
<br /><br />

#### すでにsshで接続できるサーバにはssh-copy-idを使用して登録する場合はこちら
```
ssh-copy-id -i ~/.ssh/authorized_keys (ユーザ名)@(サーバ名)
```

<br /><br />

##### 公開鍵で接続できるか確認する
```
ssh （configで設定したhost名）
```
`Last login: Tue Feb 18 11:36:08 2020 from softbank060154125183.bbtec.net`とかが出ると接続完了。`exit`などで出る。




<br /><br /><br /><br />









<br /><br />

## Gitクライアントとの接続

#### Gitクライアント用のSSH鍵を作成<br />

sshディレクトリの移動
```
cd ~/.ssh
```  
鍵生成（そろそろ4096強度で）  
※GitクライアントのにアカウントEmailアドレスを使う（特に-CオプションはなしでもOKやと思う）
```
ssh-keygen -t rsa -b 4096 -f 鍵の名前 -C "your_email@example.com"
Enter file in which to save the key(/User/you/.ssh/鍵の名前):(場所、名前問題なければそのままEnter）
Enter passphrase (empty for no passphrase):(パスフレーズ）
Enter same passphrase again:(もっかいパスフレーズ）
```
秘密鍵の鍵のパーミッションを変更（重要）
```
$chmod 600 鍵の名前
```
秘密鍵のパーミッションを確認
```
$ls -l
-rw-------  1 hacca  staff   1743 12 13 22:28 鍵の名前
```  

<br /><br />

#### Gitクライアント用の公開鍵を登録
ローカルからリモートクライアントのみの接続　→　公開鍵を各リポジトリのデプロイ鍵に登録  
サーバー側更新がある場合の接続など　→　公開鍵をアカウントののSSH鍵に登録  
登録方法：公開鍵をエディタなどで開いて中身をコピペ

<br /><br />

#### configファイルを設定（ない場合は作る）
viエディタで編集もしくはFinderから「フォルダへ移動」Cmd+Shift+G ファイルの場所 ~/.ssh/ 
```
vi ~/.ssh/config
```
```
Host bitbucket
  HostName bitbucket.org
  User git
  Port 22
  IdentityFile ~/.ssh/SSH秘密鍵の名前
  TCPKeepAlive yes
  IdentitiesOnly yes
```

<br /><br />

#### 接続テスト
```
ssh -T bitbucket（configファイルのHostにつけた名前）

// 成功した場合
logged in as *****.
You can use git or hg to connect to Bitbucket. Shell access is disabled.

// 失敗した場合
Permission denied (publickey).
```

<br /><br />

## Gitクライアントとのサーバの接続

<br /><br />

## Permission denied (publickey)

GithubやSourcetreeなどのGitクライアントに接続する場合、configファイルを設定してssh-agentに秘密鍵を登録する必要がある  
SSH接続できるのにcloneできないとか。
```
Permission denied (publickey)
```
こんなエラーで接続できない場合。  
<br />
#### 認証エージェントに秘密鍵を追加する
```
ssh-add -K ~/.ssh/SSH秘密鍵の名前
```
追加できたか確認
```
ssh-add -l
4096 SHA256:DIZbaX35dZN+p8mqQxw2mnALOyYJvKJu4BE474JQQO4  (RSA)
//こんなのでたらOK
```

<br /><br />

## 再起動のたびにssh-addした鍵がクリアされる。。。（ドハマリ中）

##### 解決策1
configファイルに以下を追加。毎回追加するのが面倒なのでワイルドカードで。
```
Host *
  AddKeysToAgent yes
  UseKeychain yes
```
私はこれでも保存しやがらなかったです。 　
<br />
##### 解決策2
plistファイルを作って起動時に```ssh-add -A```を自動実行させる
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>Label</key>
    <string>ssh-add-a</string>
    <key>ProgramArguments</key>
    <array>
        <string>ssh-add</string>
        <string>-A</string>
    </array>
    <key>RunAtLoad</key>
    <true/>
</dict>
</plist>
```
~/ライブラリ/LaunchAgents/フォルダにplistファイルを設置して再起動後```ssh-add -l```で鍵が追加されているか確認。  
https://github.com/jirsbek/SSH-keys-in-macOS-Sierra-keychain

<br /><br />


## windows用Putty


## つまづきポイント（やってみたら治るかも）
```
~/.ssh/config
// リポジトリの記述
url = ssh://git@bitbucket.org/*****/******.git
```
```
Host bitbucket // HostNameと統一するとか
  HostName bitbucket.org
```
```
ssh-add ~/.ssh/鍵
//フルパスで書いてみる
ssh-add User/*****/.ssh/鍵
```
## エラー対応

##### Too many authentication failures
```
Received disconnect from 183.90.238.15 port 10022:2: Too many authentication failures
Disconnected from 183.90.238.15 port 10022
```
Configに
```
IdentitiesOnly yes
```
