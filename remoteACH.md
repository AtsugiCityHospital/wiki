ACH　linux へのリモートアクセス
===

- mac対応
- Winは、opensshを使用

https://blogs.msdn.microsoft.com/miyamam/2016/04/09/powershell%E3%81%A7ssh%E3%82%92%E5%88%A9%E7%94%A8%E3%81%97%E3%81%A6%E3%81%BF%E3%81%9F/

を参考に。

https://github.com/PowerShell/Win32-OpenSSH

https://github.com/PowerShell/Win32-OpenSSH/releases

64

##1. ssh鍵を作成する

- 作業環境: ローカルPC
- 作業ユーザー: ローカルユーザー

病院のマシンおよび中間サーバーに接続するためにssh鍵を作成します。
github利用者など、既に鍵を作成している場合は特にこの作業は必要ありません。

鍵が作成済みか確認します。

```shell
user$ test -f $HOME/.ssh/id_rsa && echo "Already exists" || "No such file"
Already Created
```

`No such file`と表示された場合は鍵が存在しません。
下記の手順で鍵の作成を行ないます。

```shell
user$ mkdir -pm 0700 $HOME/.ssh
user$ ssh-keygen -t rsa -N '' -f $HOME/.ssh/id_rsa
```

`id_rsa`と`id_rsa.pub`の2つのファイルが生成されます。このうち`id_rsa`ファイルは秘密鍵ですので，紛失したり他人に渡したりしないようにしてください。


##2.公開鍵の受け渡し
１.で生成した公開鍵（`id_rsa.pub`）ファイルを`authorized_keys`として病院のマシンと中間サーバに登録します。

公開鍵を、shmp0722@gmail.comへ送って下さい。

公開鍵は秘密鍵とは違いメール、facebook、lineなどで送信しても問題ありません。


##3.公開鍵の中間サーバー、ACHへの登録

送って頂いた公開鍵を、中間サーバーsakura、ACHにこちらで登録致します。

アカウント名は、ogawa、yoshimineなど苗字と致します。またパスワードは便宜上こちらで設定させて頂きますので、

後日、自分でpasswdをつかって変更して下さい。　** root のパスワードは個別に問い合わせをお願い致します。
```
ex) root# passwd yoshimine

```

## 3. 公開鍵を中間サーバーsakuraとACHに登録する（管理者）

2.の手順で生成した公開鍵（`id_rsa.pub`）ファイルを`authorized_keys`として病院のマシンと中間サーバに登録します。
この手順は既に病院のマシンに接続できるPCで作業すると簡単に登録が行なえます。


### 3-1. 公開鍵を中間サーバーに登録する（管理者）

- 作業場所: 中間サーバー
- 作業ユーザー: ganka

公開鍵を病院のマシンに転送します。以下の手順は既に設定が完了しているPCで作業することを想定しています。

まずメールなどで転送した公開鍵を中間サーバーに転送します。

```shell
user$ scp id_rsa.pub ganka@tk2-204-11581.vs.sakura.ne.jp:
id_rsa.pub                                    100%  399    54.5KB/s   00:00
```

次に，中間サーバーにログインし，公開鍵を登録します。

```shell
user$ ssh ganka@tk2-204-11581.vs.sakura.ne.jp
ganka$ cat id_rsa.pub >> .ssh/authorized_keys
```

ローカルPCから接続できることを確認します。

```shell
user$ ssh ganka@tk2-204-11581.vs.sakura.ne.jp
```

### 3-2. 公開鍵を病院のマシンに登録する（管理者）

- 作業場所: 病院のマシン
- 作業ユーザー: ganka

中間サーバーに転送した公開鍵をさらに病院のマシンに転送します。

```
ganka$ scp -p id_rsa ganka@localhost:
id_rsa.pub                                    100%  399    54.5KB/s   00:00
```

同様に公開鍵を登録します。

```shell
ganka$ cat id_rsa.pub >> .ssh/authorized_keys
```



##4.ローカルマシンを設定する

- 作業環境: ローカルPC
- 作業ユーザー: ローカルユーザー

ローカルマシンの設定します。この手順は必須ではありませんが、ssh接続を簡単にするために作業をおこなうことをお勧めします。

```
user$ cat << __EOF__ >> $HOME/.ssh/config
Host tk2-204-11581.vs.sakura.ne.jp
        User ganka
        IdentityFile %d/.ssh/id_rsa
Host sakura
        Hostname tk2-204-11581.vs.sakura.ne.jp
        User ganka
        IdentityFile %d/.ssh/id_rsa
Host atsugi
        Hostname localhost
        User ganka
        Port 10022
        IdentityFile %d/.ssh/id_rsa
        ProxyCommand ssh -W %h:%p tk2-204-11581.vs.sakura.ne.jp
__EOF__
```

正しく設定できていることを確認します。

```shell
user$ ssh atsugi
Last login: Sun Dec 18 09:25:23 2016 from localhost
-------- freesurfer-Linux-centos4_x86_64-stable-pub-v5.1.0 --------
Setting up environment for FreeSurfer/FS-FAST (and FSL)
FREESURFER_HOME   /home/ganka/bin/freesurfer-5.1
FSFAST_HOME       /home/ganka/bin/freesurfer-5.1/fsfast
FSF_OUTPUT_FORMAT nii.gz
SUBJECTS_DIR      /home/ganka/bin/freesurfer-5.1/subjects
MNI_DIR           /home/ganka/bin/freesurfer-5.1/mni
(anaconda3-4.1.0) [ganka@jikei ~]$
```
※初めて接続する場合は，フィンガープリントがローカルに保存されていないため接続を続行するか確認が行われます。

最後にリモートデスクトップで接続するため，病院のマシンに接続するためのコマンドを登録します。
使用しているシェル（デフォルトはbash）に応じて行なってください。

```
user$ echo "alias atsugi='ssh -fNL 3389:localhost:3389 atsugi'" >> .bash_profile
```

こっちの方がいいかも。 
 
```
user$ echo "atsugi='ps x | grep -e ssh | grep -e 3389 > /dev/null && echo Already Connected || ( ssh -fNL 3389:localhost:3389 atsugi && echo Connected )'" >> .bash_profile

##5.Microsoft Remote DesktopのDL
- 作業環境: ローカルPC
- 作業ユーザー: ローカルユーザー

ローカルマシンに、Microsoft remote desktopをApple storeからDLします。

```
https://itunes.apple.com/jp/app/microsoft-remote-desktop/id715768417?mt=12
```

正しくインストールされたことを確認して下さい。


##6.リモートアクセス

まずsakuraへのssh接続を行います。4.の設定が正しく行われていれば、terminal上で、

```
atsugi
```
中間サーバーsakuraへ接続されます。

初回のみ、Microsoft remote desktopの接続設定を行います。

```
Connection name; As you like
PC name; localhost
Gateway; No gateway configured
User name; YOUR_FAMILINAME(lower case)
Password; YOURS
```

設定に任意の名前をつけ、保存して下さい。

Startをクリックするか、ダブルクリックで接続後、再度パスワードを入力するとACHに入れます。

##7.使用上の注意点　

- matlab使用権限
- 

ACHへは個人のアカウントで接続されます。matlabを含む多くの解析ソフトはgankaアカウントに紐付けされているため、terminal上でgankaに変更する必要があります。** 個人アカウントでは、使用できません。
```
ogawa# su - ganka
password ganka1
```
現在は2015bがdefaultに設定されています。

```
alias matlab=.../MATLAB2015b/bin/matlab
```

- HDDの使用について
- 

```
/media/USB_HDD1
```
を使用して下さい。

```
USB_HDD2
```
は現在バックアップとしての使用を行っています。原則として使用しないで下さい。

- github、その他のアカウント
- 

現在は多くのアカウントがogawaの設定となっています。（個人のメールも丸見えです）。matlab権限が個人使用のためです。気持ちの良い運用をお願い致します。

- githubをどうしても使いたい上級者
https://github.com/youkinjoh/TrainingWebSocket/wiki/同一端末で、複数のGitHubアカウントを使い分ける方法
