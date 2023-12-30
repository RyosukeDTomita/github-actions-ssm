# INDEX
- [ABOUT](#ABOUT)
- [ENVIRONMENT](#ENVIRONMENT)
- [PREPARING](#PREPARING)
- [HOW TO USE](#HOW-TO-USE)
- [MEMO](#MEMO)
******


# ABOUT
GitHub ActionsによりAWS CLIを使ってAWS SSM経由でコマンドをEC2インスタンスに対して送信するテストです。
******


# ENVIRONMENT
- GitHub Actions
    - AWS CLI
- AWS EC2
******


# PREPARING
## EC2にSSM経路でアクセスできるようにする
- IAMロールAmazonSSMManagedInstanceCoreをつける。
- EC2インスタンスのAMIはAmazonLinux2推奨?2023はデフォルトでSSM Agentが入ってないのかも。
- すぐには使えるようにならないのでIAMロールアタッチ後に10~20分待つ。
> [SSMを利用してEC2へ接続する](https://baresupport.jp/blog/2022/03/14/82/)

> [SSM Agentのインストール](https://docs.aws.amazon.com/ja_jp/systems-manager/latest/userguide/sysman-manual-agent-install.html)

### SSMで接続できない時は
- ssh接続してサービスが起動しているか確かめる。

```
# sshのconfig例
Host house
	HostName 52.69.186.76
	User ec2-user
	Port 22
	IdentityFile ~/.ssh/house.pem
	ProxyCommand "C:\Program Files\Git\mingw64\bin\connect.exe" -H tomita@172.23.95.65:81 %h %p
	ForwardAgent yes
```

```shell
sudo systemctl status amazon-ssm-agent # ssh経由でログインできないなら
```
- ちょい待つといける!!
******


## GitHub Actionsでの設定
- ~/.aws/credentialsにあるアクセスキーとシークレットを`AWS_ACCESS_KEY_ID`，`AWS_SECRET_ACCESS_KEY`としてActions --> Secretから登録する
![image](https://github.com/RyosukeDTomita/github-actions-ssm/assets/50137312/c0b718fe-5916-478d-97b5-c83d9e28554f)

- [.github/workflows/run-commnnd-ssm.yml](.github/workflows/run-commnnd-ssm.yml)のEC2インスタンスのIDを書き換えする
- push時にコマンドがEC2上で実行された(/tmp/test.txtが生成されているか)をEC2にログインして確認する(一応，コマンドのoutputはGitHubのリポジトリのページからも見れるが)。
******


## HOW TO USE
just merge `master` branch or push `master` branch.
masterブランチにmergeしたり，masterにpushしたらGitHub Actionsが起動します。
