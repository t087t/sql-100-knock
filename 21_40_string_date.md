# 100本ノック21-40

21. **s_phone の最初の-(ハイフン）までを切り出す** 

```sql
SELECT s_name, s_phone, SUBSTRING(s_phone, 1, POSITION('-' IN s_phone))  FROM supplier;
```
<br>

22.  **正規表現を利用してs_phone の最初の-(ハイフン）までを切り出す**  

```sql
SELECT s_name, s_phone, SUBSTRING(s_phone FROM '^.*?-')  FROM supplier;
```

| **記号** | **意味** |
| --- | --- |
| `^` | 行頭（文字列の最初）を指定する |
| `.` | **任意の1文字**を表す（数字、文字、記号なんでも） |
| `*` | 直前の文字（ここでは `.`）が**0回以上繰り返される**ことを表す |
| `?` | **「非貪欲」マッチ**（「条件を満たす**最短**の範囲」でマッチ） |
| `-` | 文字通りのハイフン `-` を表す |

<br>

23. **s_phone の頭2文字が 33 のレコードだけ表示** 

```sql
SELECT s_suppkey, s_name, s_phone FROM supplier WHERE SUBSTRING(s_phone, 1, 2)='33';
```

<br>

24. **s_phone から-(ハイフン)を取り除く**  

```sql
SELECT s_suppkey, s_name, s_phone, REPLACE(s_phone, '-', '') FROM supplier;
```

- `REPLACE(‘対象の文字列’（またはカラム名）, ‘置換前の文字列’, ‘置換後の文字列’)`
<br>

25. **nationkey をテキスト型で表示する** 

```sql
SELECT n_nationkey::TEXT, n_name FROM nation;;
```
<br>

26.  **nationkey をゼロ詰めして二桁で表示**

```sql
SELECT
    LPAD(n_nationkey::text, 2, '0')
    , n_name
FROM
    nation
;
```

- `LPAD(対象となる文字列, 指定した文字数, 文字)` 左詰め
- `RPAD(対象となる文字列, 指定した文字数, 文字)` 右詰め
<br>

27. **今日の日付を表示する** 

```sql
SELECT CURRENT_DATE;
```
<br>

28.  **昨日の日付を表示する** 

```sql
SELECT CURRENT_DATE-'1 day'::INTERVAL;
```

- **INTERVAL型**は、期間（時間差）を表現する型で、 日、時間、分、秒などの単位で期間を指定できる

<br>

29.  **明日の日付を表示する**

```sql
SELECT CURRENT_DATE+'1 day'::INTERVAL;
```
<br>

30.  **１ヶ月前の日付を表示する** 

```sql
SELECT CURRENT_DATE-'1 month'::INTERVAL;
```
<br>

31.  **2015年4月15日の３ヶ月前の日付けを得る** 

```sql
SELECT '2015-04-15'::DATE - '3 month'::INTERVAL;
```
<br>

32.  **2015年4月15日の年月を得る**

```sql
SELECT DATE_TRUNC('month', '2015-04-15'::DATE);
```

- `DATE_TRUNC('datepart', timestamp)`
- DATE_TRUNC 関数は、指定した日付部分 (時、日、月など) に基づいてタイムスタンプの式またはリテラルを切り捨てる
<br>

33. **2015年4月15日の日付けの値を得る** 

```sql
SELECT EXTRACT('day' FROM '2015-04-15'::DATE);
```
<br>

34. **2015年4月15日の曜日に相当する値を取得する** 

```sql
SELECT EXTRACT('dow' FROM '2015-04-15'::DATE);

```

- 日曜日が1

| **日付部分または時刻部分** | **省略形** |
| --- | --- |
| day of week | dayofweek、dow、dw、weekday |
<br>

35. **1998年1月の注文レコードを表示する** 

```sql
SELECT
    *
FROM
    orders
WHERE
    o_orderdate >= '1996-01-01'::DATE
    AND o_orderdate < '1996-02-01'::DATE;
```
<br>

36.  **1998年1月の月曜日の注文レコードを表示する** 

```sql
SELECT
    *
FROM
    orders
WHERE
    o_orderdate >= '1996-01-01'::DATE
    AND o_orderdate < '1996-02-01'::DATE
    AND EXTRACT('dow' FROM o_orderdate)= 1 ;
```
<br>

37.  **int型のid, text型のcomment からなるtestテーブルを作る**  

```sql
CREATE TABLE test(id INT,COMMENT text);
```
<br>

38. **testテーブルに id=1, comment="testtest"　のレコードを追加する**

```sql
INSERT INTO test (id, COMMENT) VALUES (1, 'testtest');
```
<br>

39. **testテーブルを削除する** 

```sql
DROP TABLE test;
```
<br>

40. **customerテーブルから10行抽出してfewcustomerというテーブルを作る** 

```sql
CREATE TABLE fewcustomer AS (SELECT * FROM customer LIMIT 10)
```
