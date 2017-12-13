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
秘密鍵の鍵のパーミッションを確認
```
$ls -l
-rw-------  1 hacca  staff   1743 12 13 22:28 鍵の名前
```  

#### Gitクライアント用の公開鍵を登録
##### ローカルからリモートクライアントのみの接続　→　公開鍵を各リポジトリのデプロイ鍵に登録
##### サーバー側更新がある場合の接続など　→　公開鍵をアカウントののSSH鍵に登録  

#### SSH接続できるのにcloneできないとか。

GithubやSourcetreeなどのGitクライアントに接続する場合、configファイルを設定してssh-agentに秘密鍵を登録する必要がある  
SSH接続できるのにcloneできないとか。
```
Permission denied (publickey)
```
こんなエラーで接続できない場合。  



#### Configファイルを設定（ない場合は作る）
Finderから「フォルダへ移動」Cmd+Shift+G ファイルの場所 ~/.ssh/
```
Host bitbucket.org
  HostName bitbucket.org
  User git
  Port 22
  IdentityFile ~/.ssh/SSH秘密鍵の名前
  TCPKeepAlive yes
  IdentitiesOnly yes
```

## windows用Putty
