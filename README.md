## サーバとssh鍵交換するイメージ
## 鍵の作り方
## サーバに転送
## SFTPアプリの設定
## Gitクライアントとの接続  
#### Gitクライアント用のSSH鍵を作成
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

#### Gitクライアント用の公開鍵を登録
ローカルからリモートクライアントのみの接続　→　公開鍵を各リポジトリのデプロイ鍵に登録
サーバー側更新がある場合の接続など　→　公開鍵をアカウントののSSH鍵に登録
登録方法：公開鍵をエディタなどで開いて中身をコピペ

#### configファイルを設定（ない場合は作る）
Finderから「フォルダへ移動」Cmd+Shift+G ファイルの場所 ~/.ssh/
```
Host bitbucket
  HostName bitbucket.org
  User git
  Port 22
  IdentityFile ~/.ssh/SSH秘密鍵の名前
  TCPKeepAlive yes
  IdentitiesOnly yes
```

#### 接続テスト
```
ssh -T bitbucket（configファイルのHostにつけた名前）

// 成功した場合
logged in as *****.
You can use git or hg to connect to Bitbucket. Shell access is disabled.

// 失敗した場合
Permission denied (publickey).
```


#### Permission denied (publickey)

GithubやSourcetreeなどのGitクライアントに接続する場合、configファイルを設定してssh-agentに秘密鍵を登録する必要がある  
SSH接続できるのにcloneできないとか。
```
Permission denied (publickey)
```
こんなエラーで接続できない場合。  

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

#### 再起動のたびにssh-addした鍵がクリアされる。。。（ドハマリ中）

##### 解決策1
configファイルに以下を追加。毎回追加するのが面倒なのでワイルドカードで。
```
Host *
  AddKeysToAgent yes
  UseKeychain yes
```
私はこれでも保存しやがらなかったです。 　

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
