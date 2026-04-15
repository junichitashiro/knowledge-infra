# Cloud9_統合開発環境構築手順

* 作業ユーザーのポリシータイプ：Cloud9User

---

## １．【IAM】での作業

[IAM_ユーザー追加手順](https://github.com/junichitashiro/Knowledges/blob/master/Cloud/AWS/IAM_ユーザー追加手順.md) に従ってユーザーグループとユーザーを追加する

---

## ２．【Cloud9】での作業

英語表記になっている場合は上部メニューからリージョンを **東京** に変える

---

### サーバーを作成する

1. **Create environment** をクリックする

2. Nameフィールドに名前（MyWorkspace）を設定して **次のステップ** へ進む

3. サーバー設定：下記のAWS無料枠を想定した設定をして **Create environment** をクリックする
    * Environment type：Create a new instance for environment (EC2)
    * Instance type：t2.micro (1 GiB RAM + 1 vCPU)
    * 他はデフォルト

---

### 作成したサーバーにログインする

* 上部メニューの **Cloud9アイコン** から **Go To Your Dashboard** をクリックすると作成した環境の一覧が表示される  
ここから追加作成や不要になった環境の削除ができる

---

### ターミナルで各種情報を表示してみる

* カーネル情報

  ```bash
  cat /proc/version
  # Linux version 4.14.181-108.257.amzn1.x86_64 (mockbuild@koji-pdx-corp-builder-64003) (gcc version 7.2.1 20170915 (Red Hat 7.2.1-2) (GCC)) #1 SMP Wed May 27 02:43:03 UTC 2020
  ```

* MySQLのバージョン

  ```bash
  mysql --version
  # mysql  Ver 14.14 Distrib 5.5.62, for Linux (x86_64) using readline 5.1
  ```

* PHPのバージョン

  ```bash
  php -v
  # PHP 5.6.40 (cli) (built: Oct 31 2019 20:35:16)
  # Copyright (c) 1997-2016 The PHP Group
  # Zend Engine v2.6.0, Copyright (c) 1998-2016 Zend Technologies
  #     with Xdebug v2.5.5, Copyright (c) 2002-2017, by Derick Rethans
  ```

* Pythonのバージョン

  ```bash
  python -V
  # Python 3.6.10
  ```

* Node.jsのバージョン

  ```bash
  node -v
  # v10.21.0
  ```

* RubyとRailsのバージョン

  ```bash
  ruby -v
  # ruby 2.6.3p62 (2019-04-16 revision 67580) [x86_64-linux]

  rails -v
  # Rails 5.0.0
  ```

---

### OSを最新化する

* yumで最新化処理を実行する

  ```bash
  sudo yum -y update
  ```

---

### タイムゾーンを設定する

* 設定ファイルを編集する

  ```bash
  sudo vi /etc/sysconfig/clock
  # --------------------------------------------------
  # 以下の指定をする
  ZONE="Asia/Tokyo"
  UTC=true
  # --------------------------------------------------
  ```

* 設定内容をローカルタイムに反映する

  ```bash
  sudo ln -sf /usr/share/zoneinfo/Asia/Tokyo /etc/localtime
  ```

* 再起動する

  ```bash
  sudo reboot
  ```

* 日付を表示して確認する

  ```bash
  date
  ```

---

### MySQLを更新する

* バージョンを表示して確認する

  ```bash
  mysql --version
  # mysql  Ver 14.14 Distrib 5.5.62, for Linux (x86_64) using readline 5.1
  ```

* バージョン5.5を削除する

  ```bash
  sudo yum -y remove mysql-config mysql55-server mysql55-libs mysql55
  ```

* バージョン5.7をインストールする

  ```bash
  sudo yum -y install mysql57-server mysql57
  ```

* 設定ファイルを編集する

  ```bash
  sudo vi /etc/my.cnf
  # --------------------------------------------------
  # 以下の編集をする
  [mysqld]
  character-set-server=utf8mb4

  [client]
  default-character-set=utf8mb4
  # --------------------------------------------------
  ```

* サービスを開始する

  ```bash
  sudo service mysqld start
  ```

* データベースを最新化する

  ```bash
  sudo mysql_upgrade -u root --force
  ```

* データベースを再起動する

  ```bash
  sudo service mysqld restart
  ```

* 自動起動の設定をする

  ```bash
  sudo chkconfig mysqld on
  ```

* バージョンを表示して確認する

  ```bash
  mysql --version
  # mysql  Ver 14.14 Distrib 5.7.28, for Linux (x86_64) using  EditLine wrapper
  ```

* データベースにログインする

  ```bash
  mysql -u root
  ```

---

### PHPを更新する

* バージョンを表示して確認する

  ```bash
  php -v
  # PHP 5.6.40 (cli) (built: Oct 31 2019 20:35:16)
  # Copyright (c) 1997-2016 The PHP Group
  # Zend Engine v2.6.0, Copyright (c) 1998-2016 Zend Technologies
  #     with Xdebug v2.5.5, Copyright (c) 2002-2017, by Derick Rethans
  ```

* PHP7.2をインストールする

  ```bash
  sudo yum -y install php72 php72-devel php72-mysqlnd php72-gd php72-intl php72-mbstring
  ```

* PHPのバージョンを切り替える

  ```bash
  sudo update-alternatives --config php
  ```

* 選択できるバージョンが表示されるので対象の番号（ここでは2）を選んでエンターキーを押下する

  ```bash
  There are 2 programs which provide 'php'.

    Selection    Command
  -----------------------------------------------
  *+ 1           /usr/bin/php-5.6
     2           /usr/bin/php-7.2

  Enter to keep the current selection[+], or type selection number: 2
  ```

* バージョンを表示して確認する

  ```bash
  php -v
  # PHP 7.2.30 (cli) (built: May  7 2020 20:38:40) ( NTS )
  # Copyright (c) 1997-2018 The PHP Group
  # Zend Engine v3.2.0, Copyright (c) 1998-2018 Zend Technologies
  ```

---

### Ruby on Rails を動かしてみる

* バージョンを表示して確認する

  ```bash
  ruby -v
  # ruby 2.6.3p62 (2019-04-16 revision 67580) [x86_64-linux]

  rails -v
  # Rails 5.0.0
  ```

* アプリケーションを新規作成する

  ```bash
  rails new myapp
  # 実行後、各種ディレクトリやファイルが作成される
  ```

* 作成されたアプリケーション用のディレクトリに移動する

  ```bash
  cd myapp
  ```

* 設定ファイルを編集する

  ```bash
  sudo vi Gemfile
  # --------------------------------------------------
  # 以下の箇所を変更する

  # 変更前
  gem 'sqlite3'

  # 変更後
  gem 'sqlite3', '~> 1.3.6'
  # --------------------------------------------------
  ```

* gemをインストールする

  ```bash
  bundle install
  ```

* サーバーを起動する

  ```bash
  rails s
  ```

* 上部メニューの **Preview** から **Preview Running Application** を選択する

* 接続拒否の画面が表示されるが右上の **Pop Out Into New Window** をクリックすると別ウィンドウで正しく表示される

---

### Railsでアプリを作る

* ターミナルからコマンドを実行してメモアプリを作成する

  ```bash
  rails g scaffold Memo title:string body:text
  ```

* データベースに反映する

  ```bash
  rails db:migrate
  ```

* サーバーを起動する

  ```bash
  rails s
  ```

* 上部メニューの **Preview** から **Preview Running Application** を選択する

* アドレスバーの末尾に **/memos** を加えてアクセスする
