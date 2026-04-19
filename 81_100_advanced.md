# 100本ノック81-100

81. **全ての注文レコードに、古いほうから順に時系列で番号づけ。同一日の注文にはランダムで順番付け。1000件表示** 

```sql
SELECT o_orderkey,  o_orderdate, ROW_NUMBER() OVER (ORDER BY o_orderdate) 
FROM orders
LIMIT 100
;
```
<br>

82. **全ての注文レコードに、古いほうから順に時系列で番号づけ。同一日の注文には同じ番号。同一日の件数に応じて番号は飛ぶ。1000件表示** 

```sql
SELECT o_orderkey,  o_orderdate, RANK() OVER (ORDER BY o_orderdate) 
FROM orders
LIMIT 100
;
```

- `rank()`は「1，2，2，4」といった順位となる

<br>

83. **全ての注文レコードに、古いほうから順に時系列で番号づけ。同一日の注文には同じ番号。番号は飛ばない。1000件表示** 

```sql
SELECT o_orderkey,  o_orderdate, DENSE_RANK() OVER (ORDER BY o_orderdate) 
FROM orders
LIMIT 100
;
```

- `dense_rank()`は「1，2，2，3」といった順位となる

<br>

84. **各注文レコードに対して、同一カスタマー＆同一 orderstatus の過去の注文金額を合計する。その情報をレコードに別カラムで追加。100件表示** 

```sql
SELECT o_orderkey,  o_custkey, o_orderdate, o_totalprice, SUM(o_totalprice) OVER (PARTITION BY o_custkey, o_orderstatus ORDER BY o_orderdate) 
FROM orders
LIMIT 100
;
```
<br>

85. **56本目の内容をWITH句を用いて書く(56本目: サブクエリを使用して、注文金額が100000より大きいcustkeyを表示する)**

```sql
WITH subtable AS (
    SELECT o_custkey, AVG(o_totalprice) AS avgprice
    FROM orders
    GROUP BY o_custkey
)

SELECT * 
FROM subtable
WHERE avgprice > 100000
;
```
<br>

86. **UNIONを使って1995年以前と1996年以降のo_totalpriceの平均を比べる**

```sql
SELECT AVG(o_totalprice)
FROM orders
WHERE o_orderdate::DATE<='1995-12-31'

UNION

SELECT AVG(o_totalprice)
FROM orders
WHERE o_orderdate::DATE>'1995-12-31'
;
```
<br>

87. **ordersテーブルから注文日(o_orderdate)ごとに、注文総金額（o_totalprice）の平均を計算し、その平均が160,000を超えるものを日付順に並べて表示**

```sql
SELECT o_orderdate, AVG(o_totalprice) AS average_total_price
FROM orders
GROUP BY o_orderdate
HAVING AVG(o_totalprice) > 160000
ORDER BY o_orderdate
;
```

**窓関数**

**顧客（o_cust）ごとに、累計の総金額SUM(o_totalprice)を計算**

```sql
SELECT o_custkey, o_orderdate, o_totalprice, SUM(o_totalprice) OVER (PARTITION BY o_custkey ORDER BY o_orderdate) AS running_total
FROM orders
;
```
