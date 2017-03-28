### 接続の不具合

## 1. 中間サーバーのsshdを再起動する

```shell
user$ ssh sakura
user$ sudo service sshd restart
```

## 2. ACHサーバーを再起動する
```shell
@ACH

root$ service autossh restart
     or
ganka$ sudo service autossh restart
```
