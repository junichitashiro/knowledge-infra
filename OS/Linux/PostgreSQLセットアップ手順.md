# PostgreSQLのセットアップ手順

* 作業アカウント：vagrant
* インストールバージョン：16

---

## 既存状態の確認

### すでにインストールされているか確認する

```bash
rpm -qa | grep postgres
```

### 存在した場合は削除する

```bash
sudo dnf -y remove postgresql
```

---

## インストール

* 指定するリポジトリのURLはパッケージごとに異なり、下記サイトで確認できる。
* [<https://yum.postgresql.org/repopackages.php#pg16>]

### 公式リポジトリパッケージの追加

```bash
sudo dnf -y install https://download.postgresql.org/pub/repos/yum/reporpms/EL-9-x86_64/pgdg-redhat-repo-latest.noarch.rpm
```

### PostgreSQLの本体をインストールする

```bash
sudo dnf -y install postgresql16-server
```

### OSからバージョンを表示して確認する

```bash
/usr/pgsql-16/bin/postgres --version
```

> postgres (PostgreSQL) 16.13

### 作成されるディレクトリで確認する

```bash
ls -l /usr/pgsql-16/
```

> drwxr-xr-x. 2 root root 4096  3月  2 21:23 bin  
> drwxr-xr-x. 3 root root 4096  3月  2 21:23 lib  
> drwxr-xr-x. 7 root root 4096  3月  2 21:23 share

---

## 初期設定作業

### データベースを初期化する

```bash
sudo /usr/pgsql-16/bin/postgresql-16-setup initdb
```

> Initializing database ... OK

* 初期化をやり直す場合は関連ファイルを削除してから行う

```bash
sudo rm -fr /var/lib/pgsql
```

---

## PostgreSQLの起動

### サービスを起動する

```bash
sudo systemctl start postgresql-16.service
```

### 自動起動の設定をする

```bash
sudo systemctl enable postgresql-16.service
```

> Created symlink /etc/systemd/system/multi-user.target.wants/postgresql-16.service → /usr/lib/systemd/system/postgresql-16.service.

---

## データベースの動作確認

PostgreSQLインストール時にpostgresユーザーがOSに追加されるので、追加されたユーザーで動作確認を行う。

### DBにログイン

```bash
sudo -u postgres psql
```

### 接続確認

```sql
SELECT current_user AS user, current_database() AS db;
```

```
   user   |    db
----------+----------
 postgres | postgres
```

### バージョン確認

```sql
SELECT version();
```

```
                                                  version
------------------------------------------------------------------------------------------------------------
 PostgreSQL 16.13 on x86_64-pc-linux-gnu, compiled by gcc (GCC) 11.5.0 20240719 (Red Hat 11.5.0-11), 64-bit
```

### ポート・データディレクトリ・ソケット配置確認

```sql
SHOW port;
SHOW data_directory;
SHOW unix_socket_directories;
```

> 5432
> /var/lib/pgsql/16/data
> /run/postgresql, /tmp

### 文字コード確認

```sql
SHOW server_encoding;
```

> UTF8

### DB一覧確認

```sql
SELECT datname, datdba::regrole AS owner FROM pg_database ORDER BY datname;
```

```
  datname  |  owner
-----------+----------
 postgres  | postgres
 template0 | postgres
 template1 | postgres
```

---

## OSのpostgresユーザーにログインパスワードを設定する

* インストール時に作成されるpostgresユーザーはパスワードが設定されていない
* DB接続ツールを使うとき等、パスワードが必要になるため設定しておく
* OSのpostgresユーザーとPostgreSQLのpostgresユーザーを混同しないように注意

### postgresユーザーにパスワードを設定する

* rootで実行する

```bash
sudo passwd postgres
```

> Changing password for user postgres.

```bash
New password: # パスワードを入力
Retype new password: # パスワードを再入力
```

> passwd: all authentication tokens updated successfully.

---

## PostgreSQLのpostgresユーザーにパスワードを設定する

### OSのpostgresユーザーに切り替える

```bash
su - postgres
```

### データベースにログインする

```bash
psql
```

> psql (16.13)  
> "help"でヘルプを表示します。

### ユーザーpostgresにパスワード'postgres'を設定する

```sql
alter role postgres with password 'postgres';
```

> ALTER ROLE

### データベースからログアウトして通常ユーザーに戻る

```bash
\q
```
> postgres
  → OSのpostgresユーザに戻る

```bash
exit
```

> logout

### 設定ファイルを編集する

```bash
sudo vi /var/lib/pgsql/16/data/pg_hba.conf
```

### 以下のように編集してパスワード認証を有効にする

```text
local   all    all                    password # peerから変更する箇所
# IPv4 local connections:
host    all    all    127.0.0.1/32    password # scram-sha-256から変更する箇所
# IPv6 local connections:
host    all    all    ::1/128         password # scram-sha-256から変更する箇所
```

### 再起動して設定を反映する

```bash
sudo systemctl restart postgresql-16.service
```

### パスワード認証でログインできることを確認する

```bash
psql -U postgres
Password for user postgres: # 設定したパスワードを入力
```

> psql (16.13)  
> Type "help" for help.

---

## PostgreSQLのアンインストール

不要になったPostgreSQLを削除する手順。

### postgresqlの削除

```bash
sudo dnf -y remove postgresql
```

### 関連ライブラリの削除

```bash
sudo dnf -y remove postgresql-libs
```

### postgresユーザーの削除

* **-r** はユーザーの作成データを削除するオプション

```bash
sudo userdel -r postgres
```
