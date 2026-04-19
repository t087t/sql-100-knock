# 100本ノック61-80

61. **割引後の単価 l_extendedprice * (1 - l_discount) が100000より高いレコードを表示**

```sql
SELECT *
FROM (SELECT *, l_extendedprice*(1-l_discount) AS discounted_price FROM lineitem) sub
WHERE discounted_price >=10000
;
```
<br>

62. **l_shipdateからl_receiptdateまでの間が20日以上のデータを対象にして、受け取りまでにかかった日にちごとに件数を表示** 

```sql
SELECT lag_date, COUNT(*)
FROM (SELECT *, l_receiptdate::DATE - l_shipdate::DATE AS lag_date FROM lineitem) sub
WHERE lag_date >=20
GROUP BY lag_date
;
```
<br>

63. **国名とその国の地域名を横に並べて一覧表示する** 

```sql
SELECT n_nationkey, n_name, n_regionkey, r_name, r_regionkey
FROM nation, region
WHERE n_regionkey=r_regionkey
;
```
<br>

64. **国名とその国の地域名を横に並べて一覧表示する(別の記法)** 

```sql
SELECT n_nationkey, n_name, n_regionkey, r_name, r_regionkey
FROM nation
JOIN region
ON n_regionkey=r_regionkey
;
```
<br>

65. **各supplierの情報と一緒に、国名、地域名も表示する** 

```sql
SELECT s_suppkey, s_name, n_name, r_name
FROM supplier
JOIN nation ON s_nationkey=n_nationkey
JOIN region ON n_regionkey=r_regionkey
;
```
<br>

66. **国名と地域名のすべての組み合わせを表示する。正しい組み合わせの場合は1, 間違った組み合わせの場合には0, の値を持つ TF というカラムを付ける。** 

```sql
SELECT n_name, r_name,
CASE WHEN n_regionkey = r_regionkey THEN 1 ELSE 0 END AS TF
FROM nation,region
;
```
<br>

67. **customerテーブルに登場する全カスタマーに対して、注文した全合計金額を求める。１度も注文がない場合はNULLを表示。100件表示** 

```sql
SELECT c_custkey, c_name, o_custkey, o_sumprice
FROM customer
LEFT JOIN (SELECT o_custkey, sum(o_totalprice) AS o_sumprice FROM orders GROUP BY o_custkey) sub
ON c_custkey = o_custkey
LIMIT 100
;
```
<br>

68. **複数テーブルの単純結合、JOIN, INNER JOIN, LEFT JOIN, RIGHT JOINの違いを調べよ** 

![join_diagram.png](../assets/join_diagram.png)

<br>

69. **各注文レコードに対して、同一カスタマーからの何回目の注文かを番号付けする。一番古い注文が１で新しくなるほど番号が大きくなる。件数が多いので100件表示** 

```sql
SELECT o_orderkey, o_custkey, o_orderdate, row_number() OVER (PARTITION BY o_custkey ORDER BY o_orderdate)
FROM orders
LIMIT 100
;
```

- **ウィンドウ関数**は現在の問い合わせ行に関連した行集合に渡っての計算処理機能を提供する。問い合わせ結果による現在行だけでなく、それ以上の行にアクセスすることができる。

### **主なウィンドウ関数**

| **関数** | **説明** |
| --- | --- |
| SUM() | 合計値を計算 |
| AVG() | 平均値を計算 |
| COUNT() | 行数をカウント |
| MIN() | 最小値を取得 |
| MAX() | 最大値を取得 |
| ROW_NUMBER() | 行番号を付与 |
| RANK() | 同じ値に対して同じランクを付与し、次のランクにはスキップが発生 |
| DENSE_RANK() | 同じ値に対して同じランクを付与し、次のランクにはスキップが発生しない |
| LEAD() | 次の行の値を取得 |
| LAG() | 前の行の値を取得 |
| FIRST_VALUE() | ウィンドウ内で最初の値を取得 |
| LAST_VALUE() | ウィンドウ内で最後の値を取得 |
| NTH_VALUE(,n) | ウィンドウ内でn番目の値を取得 |

<br>

70. **注文レコードに対して、同一カスタマーからの前回の注文金額を並べて表示する。100件表示** 

```sql
SELECT o_orderkey, o_custkey, o_orderdate, o_totalprice, lag(o_totalprice) OVER (PARTITION BY o_custkey ORDER BY o_orderdate)
FROM orders
ORDER BY o_custkey, o_orderdate
LIMIT 100
;
```

<br>

71. **注文レコードに対して、同一カスタマーからの次回の注文金額を並べて表示する。100件表示** 

```sql
SELECT o_orderkey, o_custkey, o_orderdate, o_totalprice, lead(o_totalprice) OVER (PARTITION BY o_custkey ORDER BY o_orderdate)
FROM orders
ORDER BY o_custkey, o_orderdate
LIMIT 100
;
```
<br>

72. **注文レコードに対して、同一カスタマーからの2回前の注文金額を並べて表示する。100件表示** 

```sql
SELECT o_orderkey, o_custkey, o_orderdate, o_totalprice, lag(o_totalprice, 2) OVER (PARTITION BY o_custkey ORDER BY o_orderdate)
FROM orders
ORDER BY o_custkey, o_orderdate
LIMIT 100
;
```
<br>

73. **注文レコードに対して、同一カスタマーからの現在までの全注文の金額の平均値を計算して別カラムで表示。100件表示** 

```sql
SELECT o_orderkey, o_custkey, o_orderdate, o_totalprice, avg(o_totalprice) OVER (PARTITION BY o_custkey ORDER BY o_orderdate)
FROM orders
ORDER BY o_custkey, o_orderdate
LIMIT 100
;
```
<br>

74. **注文レコードに対して、同一カスタマーからの未来を含む全注文の金額の平均値を計算して別カラムで表示。100件表示** 

```sql
SELECT o_orderkey, o_custkey, o_orderdate, o_totalprice, avg(o_totalprice) OVER (PARTITION BY o_custkey)
FROM orders
ORDER BY o_custkey, o_orderdate
LIMIT 100
;
```
<br>

75. **注文レコードに対して、同一カスタマーからの2回前、前回、今回の3回の注文金額の平均値を計算して横に並べる。100件表示** 

```sql
SELECT o_orderkey, o_custkey, o_orderdate, o_totalprice, avg(o_totalprice) OVER (PARTITION BY o_custkey ORDER BY o_orderdate ROWS 2 PRECEDING)
FROM orders
ORDER BY o_custkey, o_orderdate
LIMIT 100
;
```

### **フレーム句**

フレーム句の指定は、ウィンドウ関数においてウィンドウ関数の計算が、どの行に対して行われるか制御するために使用される機能

### **frame_startとframe_end の指定方法**

| **キーワード** | **説明** |
| --- | --- |
| UNBOUNDED PRECEDING | 先頭の行（frame_endでは使えない） |
| UNBOUNDED FOLLOWING | 末尾の行（frame_startでは使えない） |
| CURRENT ROW | 現在行 |
| n PRECEDING | 現在行よりn行前、RANGEの場合はn値前 |
| n FOLLOWING | 現在行よりn行後、RANGEの場合はn値後 |

### **フレームモード**

**■ ROWS**

- 行数に基づいてフレームを定義する
- 例えば、`ROWS BETWEEN 1 PRECEDING AND CURRENT ROW` は、現在の行とその1行前の行を含むフレームを指定する。

**■ RANGE**

- 値に基づいてフレームを定義する

### **フレーム句がない場合の動作**

**■ ORDER BYが指定されている場合**

- `ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW`が適応
- 先頭の行から現在行までが対象

**■ ORDER BYが省略された場合**

- `ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING`が適応
- 全体（先頭の行から末尾の行）が対象

<br>

76. **注文レコードに対して、同一カスタマーからの前回、今回、次回の3回の注文金額の平均値を計算して横に並べる。100件表示** 

```sql
SELECT o_orderkey, o_custkey, o_orderdate, o_totalprice, avg(o_totalprice) OVER (PARTITION BY o_custkey ORDER BY o_orderdate ROWS BETWEEN 1 PRECEDING AND 1 FOLLOWING) 
FROM orders
ORDER BY o_custkey, o_orderdate
LIMIT 100
;
```
<br>

77. **注文レコードに対して、同一カスタマーからのこれまでの注文の中での最大合計金額を各レコードに並べて表示する。100件表示** 

```sql
SELECT o_orderkey, o_custkey, o_orderdate, o_totalprice, MAX(o_totalprice) OVER (PARTITION BY o_custkey ORDER BY o_orderdate) 
FROM orders
ORDER BY o_custkey, o_orderdate
LIMIT 100
;
```
<br>

78. **注文レコードに対して、同一カスタマーからの未来を含む全注文の中での最大合計金額を各レコードに並べて表示する。100件表示** 

```sql
SELECT o_orderkey, o_custkey, o_orderdate, o_totalprice, MAX(o_totalprice) OVER (PARTITION BY o_custkey) 
FROM orders
ORDER BY o_custkey, o_orderdate
LIMIT 100
;
```
<br>

79. **注文レコードに対して、同一カスタマーからの最初の注文の金額を横に並べて表示する。100件表示** 

```sql
SELECT o_orderkey, o_custkey, o_orderdate, o_totalprice, FIRST_VALUE(o_totalprice) OVER (PARTITION BY o_custkey ORDER BY o_orderdate) 
FROM orders
ORDER BY o_custkey, o_orderdate
LIMIT 100
;
```
<br>

80. **注文レコードに対して、同一カスタマーからの未来を含めた最後の注文の金額を横に並べて表示する。100件表示** 

```sql
SELECT o_orderkey, o_custkey, o_orderdate, o_totalprice, FIRST_VALUE(o_totalprice) OVER (PARTITION BY o_custkey ORDER BY o_orderdate DESC) 
FROM orders
ORDER BY o_custkey, o_orderdate
LIMIT 100
;
```

- `ORDER BY` を指定した場合、デフォルトの範囲は **「そのパーティションの開始行から現在の行まで（ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW）」** になるため、LAST_VALUE()ではただ自分の行の金額が表示されるだけになってしまうためNG
