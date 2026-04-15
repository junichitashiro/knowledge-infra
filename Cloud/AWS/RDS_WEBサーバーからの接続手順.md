# RDS_WEBサーバーからの接続手順

---

## １．WEBサーバーにMySQLをインストールする

1. EC2ダッシュボードを開く
2. インスタンス画面を開く
3. 対象WEBサーバーのパブリックIPv4アドレスを控える
4. ターミナルからWEBサーバーへ接続する

    ```bash
    # WEBサーバー構築時に作成したキーペアを使用する
    ssh -i ~/Desktop/magenta-magenta-ssh-key ec2-user@ipアドレス
    ```

5. MySQLをインストールする

    ```bash
    sudo yum -y install mysql
    ```

---

## ２．MySQLに接続する

1. RDSダッシュボードを開く
2. データベース画面を開く
3. 対象データベースのエンドポイントを控える
4. ターミナルから接続する

    ```bash
    mysql -h エンドポイント -u root -p
    ```
