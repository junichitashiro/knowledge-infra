# Dockerセットアップ手順

* インストール環境：Windows11
* wsl2導入済み
* 使用イメージ：MySQL公式
* コマンドラインはPowerShellから実行

---

## Dockerのインストール

### 公式サイトからインストーラを取得する

#### インストール

* 下記ダウンロードページにアクセスする
* https://docs.docker.com/desktop/install/windows-install/
* インストーラをダウンロードして実行する

### インストール後の確認

#### バージョンを表示して確認する

```powershell
docker --version
```

> Docker version 26.1.1, build 4cf5afa

---

## コンテナイメージの取得

### Docker Hub からコンテナイメージを取得する

#### コンテナイメージを検索する

* 下記コンテナイメージ配布ページへアクセスする
* https://hub.docker.com/
* Docker Official Image をクリックする
* MySQLを検索する
* 検索結果から対象をクリックする
* pull コマンドをコピーする

#### コンテナイメージを取得する

```powershell
docker pull mysql
```

> Using default tag: latest  
  latest: Pulling from library/mysql  
  07bc88e18c4a: Pull complete  
  1a9c1668bf49: Pull complete  
  1021dda8eecf: Pull complete  
  fb61b56acac1: Pull complete  
  0bca83908a5b: Pull complete  
  165e8b3d37ca: Pull complete  
  3e1b086f1295: Pull complete  
  dba651668484: Pull complete  
  ed90f5355e12: Pull complete  
  0412f59ab2b5: Pull complete  
  Digest: sha256:aa021e164da6aacbefc59ed0b933427e4835636be380f3b6523f4a6c9564e1f0  
  Status: Downloaded newer image for mysql:latest  
  docker.io/library/mysql:latest  

#### 取得済みコンテナイメージを表示する

```powershell
docker images
```

> |REPOSITORY|TAG|IMAGE ID|CREATED|SIZE|
> |--|--|--|--|--|
> |mysql|latest|fcd86ff8ce8c|5 weeks ago|578MB

---

## コンテナの起動

### コンテナ起動コマンドを実行する

#### コンテナイメージ配布ページから起動コマンドをコピーする

```powershell
docker run --name some-mysql -e MYSQL_ROOT_PASSWORD=my-secret-pw -d mysql:tag
```

#### 適宜修正して実行する

docker run --name **コンテナ識別子** -p **ローカルPCのport番号:コンテナのport番号** -e MYSQL_ROOT_PASSWORD=**パスワード** -d mysql:**バージョン（省略の場合最新）**

```powershell
docker run --name test-mysql -p 13306:3306 -e MYSQL_ROOT_PASSWORD=root -d mysql
```

> 71240e9e7de6408ddd7fa5de12481780b3d6956c18a5ed36b34f18aef4463dcb

**docker run** コマンドはコンテナイメージからコンテナを作成して起動する時に使用する

#### 起動しているコンテナ情報を表示する

```powershell
docker ps
```

> |CONTAINER ID|IMAGE|COMMAND|CREATED|STATUS|PORTS|NAMES|
> |--|--|--|--|--|--|--|
> |71240e9e7de6|mysql|"docker-entrypoint.s…"|2 minutes ago|Up 2 minutes|33060/tcp, 0.0.0.0:13306->3306/tcp|test-mysql|

---

## MySQLの動作確認

### コンテナからMySQLにログインする

#### コンテナにログインする

```powershell
docker exec -it test-mysql bash
```

> bash-5.1#

#### rootでMySQLにログインする

```powershell
bash-5.1# mysql -u root -p
Enter password:*****
```
> Welcome to the MySQL monitor.  Commands end with ; or \g.  
> Your MySQL connection id is 9  
> Server version: 8.4.0 MySQL Community Server - GPL  
>
> Copyright (c) 2000, 2024, Oracle and/or its affiliates.  
>
> Oracle is a registered trademark of Oracle Corporation and/or its  
> affiliates. Other names may be trademarks of their respective  
> owners.  
>
> Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

### DBにレコードを追加する

#### DBを作成する

```sql
create database test_db;
```

> Query OK, 1 row affected (0.01 sec)


#### 使用DBを指定する

```sql
use test_db;
```
> Database changed

#### テーブルを作成する

```sql
create table users(
  id int auto_increment not null primary key,
  name varchar(50)
);
```

> Query OK, 0 rows affected (0.07 sec)

#### レコードを追加する

```sql
insert into users(name) values("Test User");
```
> Query OK, 1 row affected (0.02 sec)

#### MySQLからログアウトする

```sql
exit
```
> Bye

#### コンテナからログアウトする

Ctrl + Q（または Ctrl + Q + P）でコンテナからログアウトする

> read escape sequence

#### コンテナを停止する

```powershell
docker stop test-mysql
```
> test-mysql

#### 起動しているコンテナ情報を表示する

```powershell
docker ps
```

> |CONTAINER ID|IMAGE|COMMAND|CREATED|STATUS|PORTS|NAMES|
> |--|--|--|--|--|--|--|

ヘッダのみが表示される

docker ps に **-a** オプションを付与した場合は停止しているコンテナ情報も表示される

#### コンテナを再起動する

```powershell
docker start test-mysql
```

> test-mysql

**docker run** ではないので注意

---

## コンテナの外部からアクセスする

### ローカルからMySQLのデータを取得する

#### データ取得用のコード

```python
import mysql.connector

cnx = mysql.connector.connect(
    user='root',
    password='root',
    host='localhost',
    port='13306'
)
cursor = cnx.cursor()
cursor.execute('select * from test_db.users')

for id, name in cursor:
    print(f'{id}: {name}')
```
> 1: Test User

---

## コンテナイメージの削除

### コンテナを停止して削除する

#### コンテナが起動している場合は停止する

```powershell
docker stop test-mysql
```
> test-mysql

#### コンテナを削除する

```powershell
docker rm test-mysql
```
> test-mysql

#### コンテナイメージを削除する

```powershell
docker rmi mysql:latest
```

> Untagged: mysql:latest  
> Untagged: mysql@sha256:aa021e164da6aacbefc59ed0b933427e4835636be380f3b6523f4a6c9564e1f0  
> Deleted: sha256:fcd86ff8ce8c2d30f02607e184cbfd73eb581e22a451e4a1847a102318bc2926  
> Deleted: sha256:0473b1cea4a7e4fd89e77021d9bd0072d9fe8e88bfa7b2ceb4938374dc89cc72  
> Deleted: sha256:cec8ab251255c88df5a1765571d8d5b3f6f124f5c74da94669df8d69a712bba5  
> Deleted: sha256:1ec27ee04d25505ec12280cda84841c553e26d007d453d05afad31f91ab42c79  
> Deleted: sha256:a927878056aed598dadbb50c1c5d8f8b973f6bceb3562b2168346f93bc033416  
> Deleted: sha256:7cc2dba4d94e1ea28f8e85d457316210d2188afb659888168d1bc3e63204ba23  
> Deleted: sha256:63d0771032fba062bf73f65afd1d24e0948db53d5e721e46eb85af062dedfd62  
> Deleted: sha256:9031ef0ca7a6fa1f7051740d8342cc875feea831fc7cc95ccf931fdf8123c23f  
> Deleted: sha256:e8af93b4c6fe9b15c33180ed63c16a2174ba1a36680bb961fc78c80a9961fece  
> Deleted: sha256:b7e9107eb2df7323cdf4126ecb94412f1067730600e1c49b54cf22b765f86484  
> Deleted: sha256:0e2e8dd6d8eb63eedba9435b25b3d4c3036f9508f45fe608986efd8ac274f2a8  

#### コンテナイメージを表示して確認する

```powershell
docker images
```

> |REPOSITORY|TAG|IMAGE ID|CREATED|SIZE|
> |--|--|--|--|--|
