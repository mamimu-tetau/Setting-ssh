#### そろそろWindows乗り換えるしMacだけの書き方でいいかな？いいよね？
<br /><br />

## Table of Contents
- [サーバとssh鍵交換するイメージ](#サーバとssh鍵交換するイメージ)
- [ローカル側の鍵の作り方](#ローカル側の鍵の作り方)
- [サーバに公開鍵を登録](#サーバに公開鍵を登録)
- [SFTPアプリの設定](#SFTPアプリの設定)
- [Gitクライアントとの接続](#Gitクライアントとの接続)
- [Gitクライアントとのサーバの接続](#Gitクライアントとのサーバの接続)
- [Permission denied (publickey)](#Permission-denied-(publickey))
- [再起動のたびにssh-addした鍵がクリアされる](#再起動のたびにssh-addした鍵がクリアされる)
<br /><br />

## サーバとssh鍵交換するイメージ

![ssh](https://github.com/mamimu-tetau/ssh/blob/master/ssh_infographic.png)


## ローカル側の鍵の作り方

#### .sshディレクトリに移動
```
cd ~/.ssh
```  

#### 鍵生成（そろそろ4096強度で）  <br>
毎回作るのではなく基本的にはPC単位とかでいいんじゃないかな（haccaiMac_globalみたいな感じ）
```Plain Text
ssh-keygen -t rsa -b 4096 -f //鍵の名前
Enter file in which to save the key(/User/you/.ssh/鍵の名前):(場所、名前問題なければそのままEnter）
Enter passphrase (empty for no passphrase):(パスフレーズ）
Enter same passphrase again:(もっかいパスフレーズ）
```

#### 秘密鍵の鍵(.pubじゃないやつ)のパーミッションを変更（重要）
```
$chmod 600 鍵の名前
```

#### 秘密鍵のパーミッションを確認
```shell
$ls -l
-rw-------  1 hacca  staff   1743 12 13 22:28 # 鍵の名前 // てすと
```  

<br /><br />


## サーバに公開鍵を登録

#### ローカルPC内(.ssh)にconfigファイルを設定（ない場合は作る）
viエディタで編集もしくはFinderから「フォルダへ移動」Cmd+Shift+G ファイルの場所 ~/.ssh/ 
<br>

viエディタは頑張って使ってください。
```
vi ~/.ssh/config
```

```
Host sakura_hacca（ホスト名はなんでもいいわかりやすいの 増えるのでかぶらないように）
HostName hacca.sakura.ne.jp（サーバ名）
User hacca（ユーザー名）
Port 22（ポート）
IdentityFile ~/.ssh/SSH秘密鍵の名前
TCPKeepAlive yes
IdentitiesOnly yes
```
<br /><br />

#### サーバにssh-copy-id を使用して登録
```
ssh-copy-id -i ~/.ssh/hacca_imac__global(ローカル側の公開鍵の場所) sakura_hacca（configに登録したホスト名）

初めて接続する場合は聞かれるかも
RSA key fingerprint is SHA256:gbGQmUesGOXa/Arm1rcX/NK8G93Qak22nc82uRiC8VY.
Are you sure you want to continue connecting (yes/no)? yes <------- ここで yes とタイプ

〜
〜
Number of key(s) added:        1
と出るとOK登録されているはず
```

<br />

##### 公開鍵で接続できるか確認する
```
ssh sakura_hacca
```
##### Too many authentication failuresエラーがでる
```
Received disconnect from 133.242.249.16 port 22:2: Too many authentication failures
```

```
ssh-copy-id -i ~/.ssh/hacca_imac__global(ローカル側の公開鍵の場所) sakura_hacca（configに登録したホスト名） -o PreferredAuthentications=password
パスワード接続で接続してみる
```

<br /><br />
##### 手動で登録する
```
ssh -p 22(ポート) hacca(ユーザ名)@hacca.sakura.ne.jp(サーバ名)
mkdir ~/.ssh (.sshディレクトリなければ作成)
touch -/.ssh/authorized_keys (authorized_keysなければ作成)
chmod 700 ~/.ssh (パーミッション設定大事)
chmod 600 -/.ssh/authorized_keys (パーミッション設定大事)
vi ~/.ssh/authorized_keys
```





## サーバに転送
## SFTPアプリの設定
<br /><br />



鍵を保存したいディレクトリに移動
```
cd ~/.ssh
```  

カレントフォルダに「.ssh」がない場合は、下記コマンドで作成する。
```
install -m 0700 -d ~/.ssh
```  

鍵生成（そろそろ4096強度で）  
置き場所+ローカルのPC名とかで決めてるけど命名規則やけどだれかいい案ください。（Xserver_haccaiMacみたいな感じ？）
```
ssh-keygen -t rsa -b 4096 -f 鍵の名前
Enter file in which to save the key(/User/you/.ssh/鍵の名前):(場所、名前問題なければそのままEnter）
Enter passphrase (empty for no passphrase):(パスフレーズ）
Enter same passphrase again:(もっかいパスフレーズ）
```
秘密鍵の鍵(.pubじゃないやつ)のパーミッションを変更（重要）
```
$chmod 600 鍵の名前
```
秘密鍵のパーミッションを確認
```
$ls -l
-rw-------  1 hacca  staff   1743 12 13 22:28 鍵の名前
```  

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
