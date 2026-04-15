# バッチファイルにSQLを記述して実行する

## データベース情報

* ユーザ名：postgres
* データベース名：postgres
* テーブル名：test_table（[サンプルテーブルの作成](https://github.com/junichitashiro/Knowledges/blob/master/DB/PostgreSQL/サンプルテーブルの作成.md) 参照）
* パス：C:\Program Files\PostgreSQL\14\bin\

## バッチファイルの共通設定

* 文字コード：SJIS
* 改行コード：CRLF

---

## オプションでSQLを実行し、実行結果を外部ファイルに出力する

### バッチファイルの設定

* -c オプションで" "内にSQLを記述する
* -o オプションで出力先を指定する
  * ここではカレントディレクトリに **result.log** ファイルを出力する

```bat
@echo off
rem --------------------------------------------------
rem DB接続パラメータ
rem --------------------------------------------------
set PGPATH=C:\"Program Files"\PostgreSQL\14\bin\
set HOSTNAME=localhost
set PORTNUM=5432
set DBNAME=postgres
set USERNAME=postgres
set PGPASSWORD=postgres

rem --------------------------------------------------
rem bat実行パラメータ
rem --------------------------------------------------
set LOGFILE=result.log

rem --------------------------------------------------
rem SQL実行
rem --------------------------------------------------
%PGPATH%psql -h %HOSTNAME% -p %PORTNUM% -d %DBNAME% -U %USERNAME% -c "select * from test_table;" -o %LOGFILE%
```

### SQLの記述内容

```sql
select * from test_table;
```

---

## 実行結果をCSV形式で出力する場合

### バッチファイルの設定

* **"\copy ("SQL文") to '出力パス' with csv delimiter ',' ;"** のコマンドを記述する
  * 出力先はカレントディレクトリ
* 追加設定
  * nullを空白する
  * ヘッダ出力あり

```bat
@echo off
rem --------------------------------------------------
rem DB接続パラメータ
rem --------------------------------------------------
set PGPATH=C:\"Program Files"\PostgreSQL\14\bin\
set HOSTNAME=localhost
set PORTNUM=5432
set DBNAME=postgres
set USERNAME=postgres
set PGPASSWORD=postgres

rem --------------------------------------------------
rem bat実行パラメータ
rem --------------------------------------------------
set FILEPATH=%~dp0
set CSVFILE=result.csv

rem --------------------------------------------------
rem SQL実行
rem --------------------------------------------------
%PGPATH%psql -h %HOSTNAME% -p %PORTNUM% -d %DBNAME% -U %USERNAME% -c "\copy (select * from test_table) to '%FILEPATH%%CSVFILE%' with csv delimiter ',' null as '' header;"
```

### SQLの設定

* ( )内に以下のSQLを記述している
* 文末の ; は不要

```sql
select * from test_table
```
