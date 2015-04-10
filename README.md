# 簡易メールサーバー設置用Ansible

サクッとPOP3/SMTPメールサーバーを構築したい人向けのAnsibleスクリプト集です。

AmazonS3と連携し、メールデータがバックアップされます。

世代管理等のバックアップではなく、現状入れ替えでのバックアップです。

## ※注意

サーバー構築スクリプトは非SSL通信でのメールサーバーが構築されます。

メールアカウント・パスワードの保持も平文での送信・MD5での保持になっています。

当方、いかなる責任も取れません。


## 想定動作環境

- ホスト
 - MacOSX Yosemite
 - Ansible 1.9.0

- ゲスト
 - CentOS 6.5
 - TCP 22(SSH),110(POP3),587(SUBMISSION) 開放済み

- 構築後
 - Postfix
 - Dovecot
 - clam
 - amavis-new
 - Spamassasin

## 使い方

### 設定ファイル
`./vars`内の`*.yml.sample`を`*.yml`にリネームする
```
mv ./vars/*.yml.sample ./vars/*.yml
```

`./hosts.sample`を`hosts`にリネームする

#### main.ymlについて

メールサーバー管理情報やドメインに関しての情報が格納されています。

パラメータ名  | 内容
--------------|------
mailserver | 管理人のメールアドレス
mydomain | Aレコード登録したサーバーのドメイン
domains | MX登録したバーチャルドメイン
s3buckup | amazonS3へバックアップするCronスケジュール

#### mail.ymlについて

登録したいバーチャルドメインの情報が格納されています。

※mailパラメータ以下に配列として格納してください

パラメータ名  | 内容
--------------|------
domain | 登録したいバーチャルドメインのドメイン名
user   | 登録したいユーザー名
pass   | 登録したいパスワード

#### alias.ymlについて

メールのエイリアス(転送)設定の情報が格納されています。

※aliasパラメータ以下に配列として格納してください

パラメータ名  | 内容
--------------|------
receive | 転送元のメールアドレス
foword  | 転送先のメールアドレス
via     | 内部エイリアス用のユーザー名

※内部エイリアス用のユーザー名は受信元の保持の為に必須のようです。

#### aws.ymlについて

AmazonS3への接続設定の情報が格納されています。

※S3へのアクセス設定はこのあたり[Amazon S3 再入門 – AWS IAMでアクセスしてみよう！](http://blog.cloudpack.jp/2014/08/12/revival-amazon-s3-using-aws-iam-with-cyberduck/)を参考にしてみて下さい。

パラメータ名  | 内容
--------------|------
backet | s3のバケット名(s3://[この部分])です
access_key | s3へのアクセスキーです
secret_key | s3への秘密鍵です
region | バケットのリージョンです

#### hostsについて
``[all]``以下に設定したいサーバーを入れてください。

### 実行について
ゲストOSより下記コマンドを実行してください。

```
ansible-playbook playbook.yml -i ./hosts
```

## サーバー環境について
- メールボックス ```/var/spool/virtual```
- バーチャルドメインマップ ```/etc/postfix/virtual-domain```
- エイリアスマップ ```/etc/postfix/virtual-alias```
- スパムのホワイト・ブラックリスト管理 ```./files/spamassasin/local.cf```参照

## ライセンス
好きに使ってください。

当方、メールサーバー構築・セキュリティ方面の知識が殆ど無い為ツッコミやプルリクお待ちしております。
