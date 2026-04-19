# 100本ノック41-60

41. **fewcusomerテーブルの全レコードを削除する** 

```sql
DELETE FROM fewcustomer;
```

- deleteはテーブル内のデータの行を指定して削除することが可能
<br>

42. **もう一度customerテーブルから10行抽出してfewcustomerというテーブルを作る (40本目と同様の準備が必要)**

```sql
CREATE TABLE fewcustomer AS (
    SELECT * FROM customer LIMIT 10
)
```
<br>

43. **fewcusomerテーブルの全レコードを削除する** 

```sql
TRUNCATE TABLE fewcustomers;
```

- テーブル内のデータを全て削除する
- "DELETE"よりも"TRUNCATE TABLE"の方が早く削除ができる
<br>

44. **カスタマーごとの注文件数を表示する** 

```sql
SELECT o_custkey, COUNT(*) FROM orders GROUP BY o_custkey;
```
<br>

45. **カスタマーごとの注文合計金額，平均金額，最大金額をそれぞれ求める** 

```sql
SELECT o_custkey, SUM(o_totalprice), AVG(o_totalprice), MAX(o_totalprice)
FROM orders
GROUP BY o_custkey
;
```
<br>

46. **カスタマーごとの注文金額の標準偏差を求める** 

```sql
SELECT o_custkey, STDDEV(o_totalprice)
FROM orders
GROUP BY o_custkey
;
```
<br>

47. **注文日，orderstatus ごとに分類して合計注文金額を求める** 

```sql
SELECT o_orderdate, o_orderstatus, SUM(o_totalprice)
FROM orders
GROUP BY o_orderdate, o_orderstatus
;
```
<br>

48. **注文日，orderstatus ごとに分類して合計注文金額を求める．o_orderdate, o_orderstatus 順に並べる** 

```sql
SELECT o_orderdate, o_orderstatus, SUM(o_totalprice)
FROM orders
GROUP BY o_orderdate, o_orderstatus
ORDER BY o_orderdate, o_orderstatus
;
```
<br>

49. **注文金額の平均が100000より大きいcustkeyを表示する** 

```sql
SELECT o_custkey, AVG(o_totalprice) 
FROM orders
GROUP BY o_custkey
HAVING AVG(o_totalprice) > 100000
;
```
<br>

50. **orderstatus = O のレコードのみを対象にして、注文金額の平均が100000より大きいcustkeyを表示する** 

```sql
SELECT o_custkey, AVG(o_totalprice) 
FROM orders
WHERE o_orderstatus ='O'
GROUP BY o_custkey
HAVING AVG(o_totalprice) > 100000
;
```
<br>

51. **totalprice が100000より大きければ"High"，そうでなければ"Low"の値を持つ pricerange というカラムを追加して100件のレコードを表示する** 

```sql
SELECT o_orderkey, o_custkey, o_totalprice, o_orderdate, 
	CASE WHEN o_totalprice > 100000 THEN 'High' 
		ELSE 'Low' END AS pricerange 
FROM orders 
LIMIT 100
;
```
<br>

52. **totalprice が200000より大きければ"Higher"，100000より大きければ"High"，そうでなければ"Low"の値を持つ pricerange というカラムを追加して100件のレコードを表示する** 

```sql
SELECT o_orderkey, o_custkey, o_totalprice, o_orderdate, 
    CASE WHEN o_totalprice > 200000 THEN 'Higher' 
        WHEN o_totalprice BETWEEN 100000 AND 200000 THEN 'High'
        ELSE 'Low' END AS pricerange 
FROM orders 
LIMIT 100
;
```
<br>

53. **CASE文を使用して、custkey ごとにtotalpriceが100000より大きい注文が何件あるかを求める** 

```sql
SELECT o_custkey, 
    COUNT(CASE WHEN o_totalprice > 100000 THEN 1 ELSE NULL END)
FROM orders 
GROUP BY o_custkey
;
```
<br>

54. **orderdate から 注文年だけを抜き出した o_orderyear というカラムを作り， o_orderyear = 1994 のレコードのみ100件を表示** 

```sql
SELECT o_orderkey, o_custkey, o_totalprice, o_orderdate, o_orderyear
FROM (
    -- ① まずはこの中で o_orderyear を作成した一時的な表を作る（サブクエリ）
    SELECT *, EXTRACT('year' FROM o_orderdate) AS o_orderyear FROM orders
) subtable
-- ② 外側のWHERE句では、すでに o_orderyear が存在しているので使える！
WHERE o_orderyear = 1994
LIMIT 100
;
```

誤答例：

```sql
SELECT EXTRACT('year' FROM o_orderdate::DATE) AS o_orderyear
FROM orders
WHERE o_orderyear = 1994 --o_orderyearなんてカラムはない！（EXTRACT('year' FROM o_orderdate) = 1994としても良い）
LIMIT 100
;
```

- SELECT句で作った別名（エイリアス）は、同じレベルのWHERE句では使えない！

**SQLの処理順序**

1. **FROM句**
2. **JOIN句**
3. **WHERE句**
4. **GROUP BY句**
5. **HAVING句**
6. **SELECT句**
7. **ORDER BY句**
8. **LIMIT句**

（PostgreSQLではSELECT句で作った別名（エイリアス）は、GROUP BY句、ORDER BY句で使える！）

<br>

55. **サブクエリを使用してtotalprice が最大のレコードを1行まるまる表示する** 

```sql
SELECT * 
FROM orders 
WHERE o_totalprice = (SELECT max(o_totalprice) FROM orders)
;
```

<br>

56. **サブクエリを使用して、注文金額の平均が100000より大きいcustkeyを表示する**

```sql
SELECT * FROM (SELECT o_custkey, AVG(o_totalprice) AS avgprice
FROM orders GROUP BY o_custkey) subtable 
WHERE avgprice > 100000
;
```

<br>

57. **lineitemテーブルを使用。L_shipdateの年が1995年のレコードに限定してreturnflagごとにl_quantityを合計する** 

```sql
SELECT l_returnflag, SUM(l_quantity) AS sum_qty
FROM lineitem
WHERE '1994-12-31'::DATE < l_shipdate AND l_shipdate < '1996-01-01'::DATE
GROUP BY l_returnflag
;
```

- `WHERE EXTRACT('year' FROM L_shipdate) = 1995`はデータの件数分、年を取り出す必要があるので遅い。**範囲指定の方が高速！**

<br>

58. **returnflagと年月ごとにl_quantityの合計と l_extendedprice×(1-l_discount) の合計を計算する**

```sql
SELECT l_returnflag, l_shipmonth, SUM(l_quantity), SUM(l_extendedprice*(1 - l_discount))
FROM (SELECT *, date_trunc('month', l_shipdate) AS l_shipmonth FROM lineitem) sub
GROUP BY l_returnflag, l_shipmonth
;
```
<br>

59. **suppkeyごとにレコード件数を求めて件数の多い順に並べる** 

```sql
SELECT l_suppkey, COUNT(*) AS count_itemyy
FROM lineitem
GROUP BY l_suppkey
ORDER BY count_item DESC
;
```

- レコード数を見るときは`count(*)`

- `COUNT(l_orderkey)`はNULLでない行数を求めるとき！

<br>

60. **suppkeyごとにレコード件数と l_extendedpriceの平均を求めて平均が40000以上のsuppkeyを件数の多い順に並べる** 

```sql
SELECT l_suppkey, COUNT(*) AS count_item, AVG(l_extendedprice)
FROM lineitem
GROUP BY l_suppkey
HAVING AVG(l_extendedprice) >=40000
ORDER BY count_item DESC
;
```
