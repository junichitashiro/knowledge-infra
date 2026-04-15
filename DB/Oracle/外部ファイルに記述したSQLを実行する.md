# 外部ファイルに記述したSQLを実行する

## 実行方法

* SQL*Plusから **@ファイル名.sql** で実行する

## sqlファイルのサンプル

* テーブルを全選択して実行結果をログファイルに出力する

```sql
-- 出力設定
set linesize      -- 1行に表示する文字の数（指定なしなのでデフォルト）
set wrap      off -- 1行の表示文字数が多くても折り返しをしない
set trimout   on  -- 表示行の終わりに空白を入れない
set trimspool on  -- スプール行の終わりに空白を入れない
set colsep ,      -- 区切り文字にカンマを指定する
set termout   off -- 画面に処理結果を出力しない
set feedback  off -- 結果件数を表示しない
set echo      off -- コンソールメッセージを表示しない
set pagesize 0    -- 全行1ページに表示する

spool sql.log     --出力ファイル名

-- SQL本体
select * from table_name;

spool off
```
