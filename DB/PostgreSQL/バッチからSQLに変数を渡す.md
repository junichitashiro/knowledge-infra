# バッチからSQLに変数を渡す

## データベース情報

* ユーザ名：postgres
* データベース名：postgres
* テーブル名：test_table（[サンプルテーブルの作成](https://github.com/junichitashiro/Knowledges/blob/master/DB/PostgreSQL/サンプルテーブルの作成.md) 参照）
* パス：C:\Program Files\PostgreSQL\14\bin\

## バッチファイルの共通設定

* 文字コード：SJIS
* 改行コード：CRLF

---

## バッチファイルで設定した変数をSQLファイルに渡す方法

### 通常のSQL実行

```sql
select * from test_table where price = 270;
```

### 実行結果

| menu            | category | price | cal |
| --------------- | -------- | ----- | --- |
| Blended coffee  | drink    | 270   | 7   |
| American coffee | drink    | 270   | 7   |
| Espresso coffee | drink    | 270   | 10  |
| (3 行)          |

### バッチファイルの設定

* -v オプションを指定する
* SQLに渡す変数名（PRICE）と値（270）を設定する

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
%PGPATH%psql -h %HOSTNAME% -p %PORTNUM% -d %DBNAME% -U %USERNAME% -f %SQLFILE% -v PRICE=270
```

### SQLファイルの設定

* ファイル名：input.sql
* 変数格納先：'変数名'で記述する

```sql
select * from test_table where price = :'PRICE';
```

### 実行結果

| menu            | category | price | cal |
| --------------- | -------- | ----- | --- |
| Blended coffee  | drink    | 270   | 7   |
| American coffee | drink    | 270   | 7   |
| Espresso coffee | drink    | 270   | 10  |
| (3 行)          |

---

## テキストファイルから読み込んだ値を変数としてSQLファイルに渡す方法

### 通常のSQL実行

```sql
select * from test_table where price = 270 and cal = 7;
select * from test_table where price = 270 and cal = 10;
```

### 実行結果1

| menu            | category | price | cal |
| --------------- | -------- | ----- | --- |
| Blended coffee  | drink    | 270   | 7   |
| American coffee | drink    | 270   | 7   |
| (2 行)          |

### 実行結果2

| menu            | category | price | cal |
| --------------- | -------- | ----- | --- |
| Espresso coffee | drink    | 270   | 10  |
| (1 行)          |

### 実行元となるバッチファイルの設定

* input.txtから値を読み込んでSQLを繰り返し実行する
  * 1,2,3列目の値をバッチの変数 i, j, k に格納している
  * ヘッダの読み込みはスキップする
* 結果は実行したSQLごとに外部ファイル（result_**.log）に出力する

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
set TXTFILE=input.txt
set SQLFILE=input.sql

rem --------------------------------------------------
rem SQL実行
rem --------------------------------------------------
for /f "tokens=1,2,3 skip=1 delims=," %%i in (%TXTFILE%) do (
    %PGPATH%psql -h %HOSTNAME% -p %PORTNUM% -d %DBNAME% -U %USERNAME% -f %SQLFILE% -v PRICE=%%j -v CAL=%%k -o result_%%i.log
)
```

### 変数を記述するinput.txtの設定

```txt
no,price,cal
1,270,7
2,270,10
```

### 変数を受け取るinput.sqlの設定

* **:'PRICE'** と **:'CAL'** で変数値を受け取る

```sql
select * from test_table where price = :'PRICE' and cal = :'CAL';
```

### 実行結果1（result_1.log）

| menu            | category | price | cal |
| --------------- | -------- | ----- | --- |
| Blended coffee  | drink    | 270   | 7   |
| American coffee | drink    | 270   | 7   |
| (2 行)          |

### 実行結果2（result_2.log）

| menu            | category | price | cal |
| --------------- | -------- | ----- | --- |
| Espresso coffee | drink    | 270   | 10  |
| (1 行)          |
