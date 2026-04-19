# 100本ノック1-20

1. **customer テーブル内のレコード件数を確認する**

```sql
SELECT COUNT(*) FROM customer;
```

- `COUNT(*)`はNULL値かどうかに関係なく、取得された行の数を返す
- `COUNT(カラム名)`はNULL値でない値の行数を返す

<br>

2. **customer テーブル内のデータを10件表示する** 

```sql
SELECT * FROM customer LIMIT 10;
```
<br>

3. **customer テーブル内のデータを名前順の先頭から10件表示する**

```sql
SELECT * FROM customer ORDER BY c_name LIMIT 10;
```
<br>

4. **customer テーブル内のデータを名前順の末尾から10件表示する**

```sql
SELECT * FROM customer ORDER BY c_name DESC LIMIT 10;
```
<br>

5. **supplier テーブルから nationkey が1のデータのみ表示** 

```sql
SELECT * FROM supplier WHERE s_nationkey = 1;
```
<br>

6. **supplier テーブル内、nationkey が1のレコード件数を表示** 

```sql
SELECT COUNT (*) FROM supplier WHERE s_nationkey = 1;
```
<br>

7. **supplier テーブル内、nationkey が1以外のレコード件数を表示** 

```sql
SELECT COUNT (*) FROM supplier WHERE s_nationkey != 1;
```
<br>

8.  **supplier テーブル内、nationkey が20より大きいデータを表示** 

```sql
SELECT COUNT (*) FROM supplier WHERE s_nationkey > 20;
```
<br>

9. **supplier テーブル内、nationkey が20以上のデータを表示** 

```sql
SELECT COUNT (*) FROM supplier WHERE s_nationkey >= 20;
```
<br>

10.  **s_name に suppliername という別名を付ける** 

```sql
SELECT s_suppkey,s_name, s_name as suppliername FROM supplier;
```
<br>

11. **ordersテーブルに登場する異なるcustkeyの数を求める** 

```sql
SELECT COUNT(DISTINCT o_custkey) FROM orders;
```
<br>

12. **totalprice の平均値を求める** 

```sql
SELECT AVG(o_totalprice) FROM orders;
```
<br>

13. **totalprice の最大値を求める** 

```sql
SELECT MAX(o_totalprice) FROM orders;
```
<br>

14. **totalprice の最大値と最小値の差を求める** 

```sql
SELECT MAX(o_totalprice)-MIN(o_totalprice) FROM orders;
```
<br>

15. **supplier テーブル、s_phone の頭2文字だけ切り出し** 

```sql
SELECT s_name,s_phone, SUBSTRING(s_phone FROM 1 FOR 2) FROM supplier;
```

- `SUBSTRING(文字列  FROM 開始位置 FOR 文字列の長さ)`
- `SUBSTRING(文字列、開始位置、文字列の長さ)`
<br>

16. **s_address と　s_phone を結合してひとつのカラムにする** 

```sql
SELECT s_name, s_address || s_phone AS adressphone FROM supplier;
```
<br>

17.  **s_phoneの頭に TEL: という文字を付ける** 

```sql
SELECT s_name,s_phone, 'TEL:' || s_phone FROM supplier;
```
<br>

18. **s_name で初めて登場する#の位置を表示する**

```sql
SELECT s_name, POSITION('#' IN s_name) AS first_hash FROM supplier;
```
