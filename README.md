## サーバとssh鍵交換するイメージ
## 鍵の作り方
## サーバに転送
## SFTPアプリの設定
## Gitクライアントとの接続
GithubやSourcetreeなどのGitクライアントに接続する場合、configファイルを設定してssh-agentに秘密鍵を登録する必要がある
```
Permission denied (publickey)
```
こんなエラーで接続できない場合。

```
~/.ssh/config
Host bitbucket.org
  HostName bitbucket.org
  User git
  Port 22
  IdentityFile ~/.ssh/bitbucket_rsa // 名前変更したSSH鍵
  TCPKeepAlive yes
  IdentitiesOnly yes
```

## windows用Putty
