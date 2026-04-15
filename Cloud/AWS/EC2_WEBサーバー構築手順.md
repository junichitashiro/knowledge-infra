# EC2_インスタンス作成手順

* EC2：Elastic Compute Cloud、AWSクラウド上の仮想サーバーを指す
* インスタンス：EC2から立てられたサーバー
* AMI：Amazon Machine Image、サーバーのテンプレート

## パブリックサブネットにEC2インスタンスを設置してWEBサーバーを構築する

* Webサーバー用のインスタンスを作成する
* 事前に [VPC_ネットワーク構築手順](https://github.com/junichitashiro/Knowledges/blob/master/Cloud/AWS/VPC_ネットワーク構築手順.md) によるネットワークが作成されている前提とする
* 作成したインスタンスに **Apache** をインストールする

---

## １．インスタンスを作成する

### インスタンスの設定

1. EC2ダッシュボードを開く
2. インスタンス画面を開く
3. インスタンス起動に進む

### 名前とタグ

* 名前：magenta-magenta-web

### アプリケーションおよび OS イメージ

* Amazon マシンイメージ：Amazon Linux 2 Kernel 5.10 AMI 2.0.20220805.0 x86_64 HVM gp2

### インスタンスタイプ

* インスタンスタイプ：t2.micro

### キーペア

1. 新しいキーペアの作成画面に進む
2. 以下の設定をしてキーペアを作成する
    * キーペア名：magenta-magenta-ssh-key
    * キーペアのタイプ：RSA
    * プライベートキーファイル形式：.pem
3. キーペアがダウンロードされる
4. キーペア名に作成したキーペアが入力される
    * 作成したキーペアはメニュー **ネットワーク＆セキュリティ** カテゴリの **キーペア** から参照可能

### ネットワーク設定

* VPC：magenta-magenta-vpc
* サブネット：magenta-magenta-public-subnet-1a
* パブリック IP の自動割り当て：有効化

#### ファイアウォール (セキュリティグループ)

* セキュリティグループを作成するを選択
* セキュリティグループ名：magenta-magenta-web

#### インバウンドセキュリティグループのルール

* ルール1
  * タイプ：ssh
  * ソースタイプ：任意の場所
* ルール2
  * タイプ：HTTP
  * ソースタイプ：カスタム
  * ソース：::/0
* ルール3
  * タイプ：HTTP
  * ソースタイプ：任意の場所

#### 高度なネットワーク設定

* ネットワークインターフェイス1
* プライマリIP：10.0.10.10

### ストレージ

#### EBSボリューム

* 以下の設定をする（ルートのみ、ボリュームの追加は不要）
  * サイズ：8
  * ボリュームタイプ：gps（汎用 SSD(GP2)）
  * 終了時に削除：Yes
  * 暗号化済み：No

#### ファイルシステム

* ファイルシステム：EFS

#### ストレージの補足説明

* ストレージはサーバーのデータ保存場所で、以下の２つに分かれる
  * EBS（Elastic Block Store）
    * 可用性と耐久性が高くOSやDB向け
    * EC2と独立しているため他のインスタンスと付け替えが可能
    * SnapshotをS3に保存可能
    * EC2とは別途費用が発生する
  * インスタンスストア
    * インスタンス用の一時的なストレージ
    * インスタンスを停止するとクリアされる
    * 他のインスタンスに付け替えられない
    * 無料

### 高度な詳細

#### 必要な場合のみ設定する

* スポットインスタンスをリクエスト：スポットインスタンスをリクエストする場合
* IAMインスタンスプロフィール：IAMロールを付与したい場合
* インスタンスを⾃動復旧：インスタンスチェックが失敗した場合に⾃動で復旧させるか
* シャットダウン動作：電源を落とした時にインスタンスをどうするか
* 停⽌ - 休⽌動作：インスタンスを停⽌した際に保存している領域をルートボリュームに保存するか（頻度低）
* 終了保護：インスタンスを終了を押した際にインスタンスを終了させないようにする
* 停⽌保護：インスタンスを停⽌させないようにする
  * インスタンスを間違っても停⽌させたくない場合に利⽤
* CloudWatchモニタリングの詳細は：EC2の詳細なメトリクス(稼働情報)をcloudwatchで確認できるようにする

### 概要

* インスタンス数の設定をしてインスタンスの起動を実行する
  * インスタンス数：1

---

## ２．sshでの接続方法

1. 作成したキーペアのディレクトリへ移動する

    ```bash
    cd ~/Desktop/
    ```

2. pemファイル（秘密鍵）の権限を変更して編集不可にする

    ```bash
    chmod 400 magenta-magenta-ssh-key.pem
    ```

3. EC2画面の対象インスタンスから **パブリック IPv4 アドレス** を控えておく

4. sshコマンドで接続する（**XX.XX.XX.XX** に上記3のIPアドレスを入力する）

    ```bash
    ssh -i magenta-magenta-ssh-key.pem ec2-user@XX.XX.XX.XX
    ```

---

## ３．Apacheをインストールする

作成したインスタンスの IPv4 パブリック IP に ssh でログインして作業する

* yum を最新化する

  ```bash
  sudo yum update -y
  ```

* yum からインストールを実行する

  ```bash
  sudo yum install -y httpd
  ```

* サービスを開始する

  ```bash
  sudo systemctl start httpd.service
  ```

* サービスの状態を表示して確認する

  ```bash
  sudo systemctl status httpd.service
  ```

  下記が表示される

  ```bash
  ● httpd.service - The Apache HTTP Server
    Loaded: loaded (/usr/lib/systemd/system/httpd.service; disabled; vendor preset: disabled)
    Active: active (running) since 日 2021-02-07 06:24:18 UTC; 10s ago
      Docs: man:httpd.service(8)
  Main PID: 930 (httpd)
    Status: "Total requests: 0; Idle/Busy workers 100/0;Requests/sec: 0; Bytes served/sec:   0 B/sec"
    CGroup: /system.slice/httpd.service
            ├─930 /usr/sbin/httpd -DFOREGROUND
            ├─931 /usr/sbin/httpd -DFOREGROUND
            ├─932 /usr/sbin/httpd -DFOREGROUND
            ├─933 /usr/sbin/httpd -DFOREGROUND
            ├─934 /usr/sbin/httpd -DFOREGROUND
            └─935 /usr/sbin/httpd -DFOREGROUND

  2月 07 06:24:18 ip-10-0-10-10.ap-northeast-1.compute.internal systemd[1]: Starting The Apache HTTP Server...
  2月 07 06:24:18 ip-10-0-10-10.ap-northeast-1.compute.internal systemd[1]: Started The Apache HTTP Server.
   ```

* プロセスを表示して確認する

  ```bash
  ps -axu | grep httpd
  ```

  下記が表示される

  ```bash
  root       930  0.0  0.9 257348  9464 ?        Ss   06:24   0:00 /usr/sbin/httpd -DFOREGROUND
  apache     931  0.0  0.6 308696  6384 ?        Sl   06:24   0:00 /usr/sbin/httpd -DFOREGROUND
  apache     932  0.0  0.6 538136  6384 ?        Sl   06:24   0:00 /usr/sbin/httpd -DFOREGROUND
  apache     933  0.0  0.6 308696  6384 ?        Sl   06:24   0:00 /usr/sbin/httpd -DFOREGROUND
  apache     934  0.0  0.6 308696  6384 ?        Sl   06:24   0:00 /usr/sbin/httpd -DFOREGROUND
  apache     935  0.0  0.6 308696  6384 ?        Sl   06:24   0:00 /usr/sbin/httpd -DFOREGROUND
  ec2-user   988  0.0  0.0   8860   940 pts/0    R+   06:28   0:00 grep --color=auto httpd
  ```

* 自動起動の設定をする

  ```bash
  sudo systemctl enable httpd.service
  ```

  下記が表示される

  ```bash
  Created symlink from /etc/systemd/system/multi-user.target.wants/httpd.service to /usr/lib/systemd/system/httpd.service.
  ```

* 自動起動が有効になっているか確認する

  ```bash
  sudo systemctl is-enabled httpd.service
  ```

  下記が表示される

  ```bash
  enabled
  ```

---

## ４．HTTP通信を許可する

* パブリックサブネットへのHTTP通信を許可する
* インスタンス作成時に設定していれば不要

1. EC2ダッシュボードを開く
2. セキュリティグループ画面を開く
3. 対象のセキュリティグループにチェックを入れる
4. インバウンドルールを編集する
5. 以下のルールを追加する
    * タイプ：HTTP
    * ソース：任意の場所（**0.0.0.0/0** , **::/0**）
6. ブラウザからアクセスしてApacheの **Test Page** が表示されることを確認する

---

## ５．IPアドレスを固定化する

### 確保したElastic IPアドレスをパブリックIPアドレスに関連付けて固定化する

1. EC2ダッシュボードを開く
2. Elastic IP画面を開く
3. Elastic IPアドレスの割り当てに進む
4. 割り当てを実行する
5. 一覧に割り当てられたIPアドレスが表示る
6. 割り当てたIPアドレスにチェックを入れる
7. アクションメニューから **Elastic IP アドレスの関連付け** を選択する
8. 以下の設定をして関連付けする
    * リソースタイプ：インスタンス
    * インスタンス：magenta-magenta-web
    * プライベートIPアドレス：10.0.10.0
      * magenta-magenta-webのプライベートIPアドレスが表示される
    * 再関連付け：チェックなし

### ※使用していない時は以下の手順で開放する

2. Elastic IP画面を開く
2. アクションメニューから **Elastic IP アドレスの関連付けの解除** を選択する
3. 関連付けを解除する
4. 続けて **Elastic IP アドレスの開放** を選択する
5. 開放を実行する

---

## ６．事後作業

不必要な料金が発生しないように以下の作業を行う

* Elastic IPアドレスの開放
* EC2インスタンスの停止
