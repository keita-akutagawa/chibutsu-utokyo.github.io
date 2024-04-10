# 課題

!!! Warning
    課題１と２は、Codespaceで行ってはいけません。情報漏洩のリスクがあります。  
    間違えてやってしまった場合は、早急にTAに連絡してください。

## 課題１（当日のみUTokyo-VPN不要）
559のサーバーであるdover.eps.s.u-tokyo.ac.jpにSSHでログインしてください。  
次に、課題１で手元に持ってきたkadai.txtに対する答えを、doverの/home0/akutagawa2024/submitディレクトリに送ってください。  
ファイルの名前は、(アカウント名).txtでお願いします。  
※3日後にはUTokyo-VPN経由でのみアクセス可能な設定に戻します。


## おまけ課題：パスワード認証SSHサーバーに対する攻撃
簡単なサイバー攻撃をしてみましょう。  
ハッカー(正確にはクラッカー)になったつもりで挑戦してみてください。  
この課題は、Codespaceで行っても大丈夫です。

!!! Warning
    許可無く他のサーバーに以下のことを行った場合、ほぼ間違いなく法に触れることになります。  
    決して行ってはいけません。  

ログインサーバー(559で言うところのdover)へ遠隔ログインを試みます。  
この際、あなたはOSINT等によりユーザー名は知っているものの、パスワードは知らないことが前提です。  
どのようにパスワードを入手しますか？



今回は軽量コンテナであるdockerを用いることにします。  
自分のPCにdockerをインストールするか、Codespaceを使ってください。559のMac miniにはインストールされていません。
以下はCodespaceで行う場合の手順です。  
まずは、`/workspaces/chibutsu-utokyo.github.io/docs/RemoteAccess`に移動して、dockerコンテナを起動します。少し時間がかかるかもしれません。
```
$ cd /workspaces/chibutsu-utokyo.github.io/docs/RemoteAccess
$ docker build -t ssh_container .
$ docker run -tid --rm -p 22222:22 ssh_container
```




