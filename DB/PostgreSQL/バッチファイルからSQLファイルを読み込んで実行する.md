# バッチファイルからSQLファイルを読み込んで実行する

## データベース情報

* ユーザ名：postgres
* データベース名：postgres
* テーブル名：test_table（[サンプルテーブルの作成](https://github.com/junichitashiro/Knowledges/blob/master/DB/PostgreSQL/サンプルテーブルの作成.md) 参照）
* パス：C:\Program Files\PostgreSQL\14\bin\

## バッチファイルの共通設定

* 文字コード：SJIS
* 改行コード：CRLF

---

## オプションでSQLファイルを実行し、実行結果を外部ファイルに出力する

### バッチファイルの設定

* 出力先はバッチファイルで指定する
* -f オプションで実行するSQLファイルを指定する
* -o オプションで出力先を指定する

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
set SQLFILE=input.sql
set LOGFILE=result.log

rem --------------------------------------------------
rem SQL実行
rem --------------------------------------------------
%PGPATH%psql -h %HOSTNAME% -p %PORTNUM% -d %DBNAME% -U %USERNAME% -f %SQLFILE% -o %LOGFILE%
```

### SQLファイルの設定

* ファイル名：input.sql

```sql
select * from test_table;
```

---

## 実行結果をCSV形式で出力する場合

### バッチファイルの設定

* 出力先はバッチファイルで指定する
* 出力形式はSQLファイルで指定する

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
set SQLFILE=input.sql
set CSVFILE=result.csv

rem --------------------------------------------------
rem SQL実行
rem --------------------------------------------------
%PGPATH%psql -h %HOSTNAME% -p %PORTNUM% -d %DBNAME% -U %USERNAME% -f %SQLFILE% -o %CSVFILE%
```

### SQLファイルの設定

* ファイル名：input.sql
* SQLを **『copy (』** と **『)to stdout with csv delimiter ',' ;』** の中に記述する
* 出力先：標準出力
* 追加設定
  * nullを空白にする
  * ヘッダ出力あり

```sql
copy (
    select * from test_table
) to stdout with csv delimiter ',' null as '' header;
```

---

## SQLファイルに出力パスまで記載する場合

### バッチファイルの設定

* バッチファイルで出力先の記述はしないため -o オプションを指定しない

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
set SQLFILE=input.sql

rem --------------------------------------------------
rem SQL実行
rem --------------------------------------------------
%PGPATH%psql -h %HOSTNAME% -p %PORTNUM% -d %DBNAME% -U %USERNAME% -f %SQLFILE%
```

### SQLファイルの設定

* ファイル名：input.sql
* 出力先をSQLファイルに絶対参照で指定する
* 書き込み権限のあるフォルダを指定すること

```sql
copy (
    select * from test_table
) to 'C:\temp\result.csv' with csv delimiter ',' null as '' header;
```
