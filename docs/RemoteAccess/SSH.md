# SSH
<span style="color: red; ">
以下の内容をCodespaceで行ってはいけません。情報漏洩のリスクがあります。  
間違えてやってしまった場合は、早急にTAに連絡してください。
</span>


## SSHとは
SSH(Secure SHell)とは、ネットワークを用いてサーバーに遠隔ログインするためのアプリケーションです。
これにより、サーバーがある場所まで行かなくても操作することが可能になり、例えばスパコン富岳を使うために神戸に行く、などといった手間が省けます。
また、全ての通信内容を暗号化するため、盗聴されたとしても内容が第三者に伝わることは基本的にはありません。


## SSHしてみよう
まずはSSHを使うための環境を整えましょう。
コマンドプロンプト・Powershell・ターミナルいずれかで`ssh`と入力してください。
使い方が出てきたらOKです。`Command Not Found`みたいなのが出てきたら別途SSHをインストールする必要があります。インストールできなさそうな場合はTAにご相談ください。

ではSSHで559のdoverサーバーにログインしてみましょう。「dover」はコンピュータ用語ではなく、かつての管理者が今回扱うサーバーにつけた名前です。  

***
接続の前にVPNへの接続が必要です。  
UTokyo-VPNの設定をしていない方は[VPNのページ](VPN.md)を参照して、設定してください。
***

以下のコマンドを入力してください。s123456は自身のアカウント名に変えてください。
```
$ ssh s123456@dover.eps.s.u-tokyo.ac.jp
```
`Are you sure you want to continue connecting (yes/no)?`と聞かれたら`yes`と答えてください。  
上手くいけばパスワードを聞かれるので、メールで送られてきたパスワードを入力してください。  
パスワードを入力する際、入力した文字が画面に表示されないと思いますが、問題ありません。これはショルダーハッキングと呼ばれる攻撃に対抗する仕組みです。

ログインが成功すると、以下のような画面になると思います。
```
# s123456は自身のアカウント名になります。
s123456@dover:~$
```
いろんなディレクトリやファイルを見て、このサーバー内を探検してみてください。
doverには、Debianと呼ばれるLinuxディストリビューションが入っています。特にWindowsとは雰囲気がかなり異なっていると思います。  
探検が終わったらホームディレクトリに戻りましょう。その後、適当にファイルを作ってください。後で使用します。
```
$ cd
$ echo "後で使うテキストファイル" > sample1.txt
```
終わったら`exit`でログアウトしましょう。


## SCPとは
次にSCPについて説明します。このコマンドはファイル等を転送するためのものです。  
通信にSSHを用いるため、盗聴の心配はSSHと同じく殆どありません。


## SCPしてみよう
使い方は以下のとおりです。
```
# リモートサーバーから手元のPCにファイルを持ってくるとき
$ scp ユーザー名@リモートサーバーのアドレス:持ってきたいファイルの場所 ファイルを置きたい場所

# 手元のPCからリモートサーバーにファイルを送るとき
$ scp 送りたいファイルの場所 ユーザー名@リモートサーバーのアドレス:ファイルを置きたい場所
```

早速使ってみましょう。先程作成したファイルを手元のPCに持ってくるには、次のコマンドを入力します。
```
# s123456は自身のアカウント名に変えること
# ファイルを置く場所はカレントディレクトリを示す「.」にしているので、見逃さないように
$ scp s123456@dover.eps.s.u-tokyo.ac.jp:~/sample1.txt .
```
ファイルが送られてきたことを確認してください。

次に手元のPCからファイルを送ってみましょう。適当にファイルを作成して、それを送ってみます。
```
$ echo "送るファイル" > sample2.txt

# s123456は自身のアカウント名に変えること
$ scp sample2.txt s123456@dover.eps.s.u-tokyo.ac.jp:~
```
ちゃんと送られたかどうか、doverにSSHログインして確かめてみてください。


## 公開鍵認証
SSHの認証(ユーザーが本人か確かめる方法)としてパスワード認証と公開鍵認証があります。
今のところパスワード認証を用いていますが、セキュリティ的な問題から公開鍵認証が推奨されています。可能なら公開鍵認証の設定をしましょう。SSHやSCPの際に毎回パスワードを打たなくてもよくなる、といったメリットもあります。

まず`ssh-keygen`コマンドを用いて公開鍵と秘密鍵のペアを作ります。無い場合は別途対応してください。  
以下はPowershellを用いて鍵を生成したときのものです。
```
PS C:\Users\dareka> ssh-keygen.exe
Generating public/private rsa key pair.
Enter file in which to save the key (C:\Users\dareka/.ssh/id_rsa): C:\Users\dareka/.ssh/id_rsa_dover # 鍵の名前は自由です。置き場所は.ssh下が良いと思います。
Enter passphrase (empty for no passphrase): # パスワードは空でも大丈夫です。
Enter same passphrase again:
Your identification has been saved in C:\Users\dareka/.ssh/id_rsa_dover
Your public key has been saved in C:\Users\dareka/.ssh/id_rsa_dover.pub
The key fingerprint is:
SHA256:df5KwL8V4h09b7IC865557oP11mYGnZI1TFANix2nzQ dareka@dareka
The key's randomart image is:
+---[RSA 3072]----+
|            o=o+.|
|           o.+.Eo|
|          o = o o|
|         o + . * |
|        S o * *.o|
|          o= B +=|
|           +* *.+|
|           o+*.+ |
|          o+*B+  |
+----[SHA256]-----+
```
これで、id_rsa_dover(秘密鍵)とid_rsa_dover.pub(公開鍵)が生成されました。

公開鍵の方をリモートサーバーにおけば公開鍵認証が完了します。  
置き場所は`~/.ssh`で、名前は`authorized_keys`にする必要があります。これは、doverに入っているSSHを管理するソフトウェアOpenSSHが、`~/.ssh/authorized_keys`から公開鍵を読み込むようになっているためらしいです。  
```
# 公開鍵(.pubファイル)があるディレクトリに移動
$ cd ~/.ssh
$ scp id_rsa_dover.pub s123456@dover.eps.s.u-tokyo.ac.jp:~

# doverにログインする。ここではまだパスワードが必要
$ ssh s123456@dover.eps.s.u-tokyo.ac.jp 

# .sshディレクトリが無い場合は作成する
$ mkdir .ssh

# .sshディレクトリに先程送った公開鍵を移動させる
$ mv id_rsa_dover.pub .ssh/

# 名前を変える
$ mv .ssh/id_rsa_dover.pub .ssh/authorized_keys

# 権限を設定する
$ chmod 700 .ssh
```
これで設定は完了です。  

ログインするコマンドは次のとおりです。
```
# s123456は自身のアカウント名に変えること
$ ssh -i 秘密鍵の場所 s123456@dover.eps.s.u-tokyo.ac.jp
```
パスワードを聞かれずにログインできるか確認してください。


## configファイル
公開鍵認証によって毎回パスワードを入力しなくてもよくなりましたが、その代わりに秘密鍵の場所を指定する必要がありました。  
ここではconfigファイルを作って、さらに簡単にSSHやSCPが使えるようにしてみます。  

まず、手元のPCで`~/.ssh`に移動ます。configファイルが無い場合は新たに作成してください。
```
$ cd ~/.ssh
$ touch config
```

次にconfigに以下の内容を書き込みます。メモ帳やVSCode等のエディタを用いると楽です。
```
# s123456は自身のアカウント名に変えること
Host dover
	HostName dover.eps.s.u-tokyo.ac.jp
	User s123456
	IdentityFile ~\.ssh\id_rsa_dover
```

これで完了です。  
改めてdoverにSSHログインします。入力するコマンドは以下になります
```
$ ssh dover
```
ログインできるか確認してください。


## おわりに
研究生活が始まると、多かれ少なかれ外部のコンピュータ(サーバー)を使用することになります。  
利用するためにSSHキーを作れと言われることがあるはずです。

<span style="color: red; "> 
秘密鍵は決して誰にも見せてはいけません。
秘密鍵が自分以外の人に見られる状況になると、セキュリティ上とても危険です。
必ず手元に保管してください。
</span>

公開鍵は他の人に見られても大丈夫です。 
恐らく公開鍵を送るように指示されると思います。その際は、.pubファイルの中身を送ってください。
