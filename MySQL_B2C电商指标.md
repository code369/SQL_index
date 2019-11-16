

## B2C电商订单指标

【技术环境】

数据库：Mysql

数据库版本：mysql5.7

操作语言：SQL

操作环境：Windows

### 一、订单指标
#### 1.总量分析

（1）时间维度（日、周、月、季度、年）

日订单量

```
-- 日订单量
-- 方式一：带年月日
SELECT
	DATE_FORMAT( PAY_TIME, '%Y年%m月%d日' ) AS DAY,
	COUNT( DISTINCT ID ) AS 日订单量 
FROM
	B2C_ORDER 
WHERE
	BILL_STATUS = '07' 
GROUP BY
	DAY;
	-- 方式二：
SELECT
	DATE_FORMAT( PAY_TIME, '%Y-%m-%d' ) AS DAY,
	COUNT( DISTINCT ID ) AS 日订单量 
FROM
	B2C_ORDER 
WHERE
	BILL_STATUS = '07' 
GROUP BY
	DAY;
```

-- 查询周订单量
```
-- 查询周订单量
-- 方式一：
SELECT 
	YEAR( PAY_TIME ) AS year1,
	WEEK ( PAY_TIME ) AS week1,
	COUNT( DISTINCT ID ) 
FROM
	B2C_ORDER 
GROUP BY
	year1,
	week1 
ORDER BY
	year1,
	week1;

-- 方式二
SELECT
	DATE_FORMAT( PAY_TIME, '%Y年%u周' ) AS week1,
	COUNT( DISTINCT ID ) AS 周订单量 
FROM
	B2C_ORDER 
GROUP BY
	week1;
	
-- 方式三
SELECT
	DATE_FORMAT( PAY_TIME, '%Y' ) AS year1,
	DATE_FORMAT( PAY_TIME, '%u' ) AS week1,
	COUNT( DISTINCT ID ) AS 周订单量 
FROM
	B2C_ORDER 
GROUP BY
	year1,
	week1 
ORDER BY
	year1,
	week1;
```
周订单量

```

-- 方式一
SELECT 
	YEAR( PAY_TIME ) AS year1,
	MONTH ( PAY_TIME ) AS month1,
	count( ID ) 
FROM
	B2C_ORDER 
GROUP BY
	year1,
	month1 
ORDER BY
	year1,
	month1

-- 方式二
SELECT
	DATE_FORMAT( PAY_TIME, '%Y年%m月' ) AS month1,
	COUNT( DISTINCT ID ) AS 月订单量 
FROM
	B2C_ORDER 
GROUP BY
	month1;
```

-- 季度订单量

```
-- 方式一
-- 直接用quarter函数
SELECT 
	YEAR( PAY_TIME ) AS year1,
	QUARTER ( PAY_TIME ) AS quarter1,
	count( ID ) 
FROM
	B2C_ORDER 
GROUP BY
	year1,
	quarter1
ORDER BY
	year1,
	quarter1
-- 方式二
SELECT
	DATE_FORMAT( PAY_TIME, '%Y' ) AS year1,
	FLOOR( ( date_format( PAY_TIME, '%m' ) + 2 ) / 3 ) AS quarter1,
	count( ID ) AS total 
FROM
	B2C_ORDER 
GROUP BY
	year1,
	quarter1
ORDER BY
	year1,
	quarter1;

-- 方式三
-- 注：floor 函数返回小于X的最大整数（不是四舍五入）
SELECT
	CONCAT( tmp1.QUARTER1, '季度' ) AS 季度,
	count( tmp1.ID ) AS 季度订单量 
FROM
	(
SELECT
	concat( tmp.year1, FLOOR( ( date_format( tmp.PAY_TIME, '%m' ) + 2 ) / 3 ) ) AS QUARTER1,
	tmp.PAY_TIME,
	tmp.ID 
FROM
	( SELECT concat( date_format( PAY_TIME, '%Y' ), '年第' ) AS year1, PAY_TIME, ID FROM B2C_ORDER ) AS tmp 
	) AS tmp1 
GROUP BY
	季度;
```

年度订单量

```
--方式一
SELECT YEAR
	( PAY_TIME ) AS year1,
	count( ID ) 
FROM
	B2C_ORDER 
GROUP BY
	year1

--方式二
SELECT
	DATE_FORMAT( PAY_TIME, '%Y' ) AS year1,
	COUNT( DISTINCT ID ) AS 年度订单量 
FROM
	B2C_ORDER 
GROUP BY
	year1;
```

（2）物流维度

```
SELECT
	tmp.LOGISTICS_NAME,
	COUNT( tmp.ORDER_ID ) AS 物流订单量 
FROM
	(
SELECT
	a.LOGISTICS_MODE,
	ORDER_ID,
CASE
	
	WHEN a.LOGISTICS_MODE = '0' THEN
	'快递' 
	WHEN a.LOGISTICS_MODE = '1' THEN
	'经销商/三方物流' 
	WHEN a.LOGISTICS_MODE = '2' THEN
	'供应商直发' ELSE '其他' 
	END AS 'LOGISTICS_NAME' 
FROM
	B2C_ORDER_GOODS a
	JOIN B2C_ORDER b ON a.ORDER_ID = b.ID 
WHERE
	BILL_STATUS = '07' 
	) AS tmp 
GROUP BY
tmp.LOGISTICS_MODE;
```

####  2.占比

（1）平台维度

各个平台的订单量占比

```
-- 平台订单占比
-- 方式一：内连接+笛卡尔积
SELECT 
	NAME,
	number ,
	concat( format( number / total * 100, 2 ), '%' ) as ratio
FROM
	(
SELECT
	* 
FROM
	(
SELECT
	b.NAME,
	count( a.id ) number 
FROM
	B2C_ORDER a
	JOIN B2C_PLATFORM b ON a.PLATFORM_ID = b.id 
GROUP BY
	b.NAME 
	) t1
	INNER JOIN ( SELECT count( a.id ) total FROM B2C_ORDER a ) t2 ON 1 = 1 
	) t3 
ORDER BY
	订单量 DESC;
	
-- 方式二 嵌套
SELECT
	t1.NAME,
	number,
	( number / total ) AS ratio 
FROM
	(
SELECT
	b.NAME,
	count( a.id ) number 
FROM
	B2C_ORDER a
	JOIN B2C_PLATFORM b ON a.PLATFORM_ID = b.id 
GROUP BY
	b.NAME 
	) t1,
	( SELECT count( a.id ) total FROM B2C_ORDER a ) t2 
GROUP BY
	t1.NAME
```

#### 3.同比

（1）时间维度

```
--月订单量同比
SELECT
	test.now_time 同比年月,
CASE
	
	WHEN current_order_num IS NULL 
	OR current_order_num = 0 THEN
	0 ELSE current_order_num 
END 本月订单量,
CASE
		
		WHEN last_year_order_num IS NULL 
		OR last_year_order_num = 0 THEN
			0 ELSE last_year_order_num 
		END 上月订单量,
CASE
		
		WHEN last_year_order_num IS NULL 
		OR last_year_order_num = 0 THEN
			0 ELSE ( CONVERT ( ( ( current_order_num - last_year_order_num ) / last_year_order_num ) * 100, DECIMAL ( 10, 2 ) ) ) 
		END 月订单量同比 
FROM
	(
	SELECT
		* 
	FROM
		(
		SELECT
			date_format( DATE_ADD( b.BOOK_TIME, INTERVAL 1 YEAR ), '%Y-%m' ) AS last_year_time,
			count( b.ID ) AS last_year_order_num 
		FROM
			B2C_ORDER b 
		GROUP BY
			date_format( DATE_ADD( b.BOOK_TIME, INTERVAL 1 YEAR ), '%Y-%m' ) 
		ORDER BY
			date_format( DATE_ADD( b.BOOK_TIME, INTERVAL 1 YEAR ), '%Y-%m' ) ASC 
		) last_year_sale
		INNER JOIN (
		SELECT
			date_format( a.BOOK_TIME, '%Y-%m' ) AS now_time,
			date_format( a.BOOK_TIME, '%Y' ) AS now_year,
			count( a.ID ) AS current_order_num 
		FROM
			B2C_ORDER a 
		GROUP BY
			date_format( a.BOOK_TIME, '%Y-%m' ),
			date_format( a.BOOK_TIME, '%Y' ) 
		ORDER BY
			date_format( a.BOOK_TIME, '%Y-%m' ) ASC 
		) now_sale ON 1 = 1 
	) test 
ORDER BY
	test.now_year DESC,
test.now_time ASC;
```

#### 4.环比

（1）时间维度

```
--月订单量环比
SELECT
	now_sale.now_year as 年份,
	now_sale.now_time 具体时间,
CASE
		
		WHEN current_month_order_num IS NULL 
		OR current_month_order_num = 0 THEN
			0 ELSE current_month_order_num 
		END 本月订单量,
CASE
		
		WHEN last_month_order_num IS NULL 
		OR last_month_order_num = 0 THEN
			0 ELSE last_month_order_num 
		END 上个月订单量,

CASE
		
		WHEN last_month_order_num IS NULL 
		OR last_month_order_num = 0 THEN
			0 ELSE ( CONVERT ( ( ( current_month_order_num - last_month_order_num ) / last_month_order_num ) * 100, DECIMAL ( 10, 2 ) ) ) 
		END 月订单量环比
FROM
	(
	SELECT
		date_format( a.BOOK_TIME, '%Y-%m' ) AS now_time,
		date_format( a.BOOK_TIME, '%Y' ) AS now_year,
		count(a.ID) as current_month_order_num
	FROM
		B2C_ORDER a 
	GROUP BY
		date_format( a.BOOK_TIME, '%Y-%m' ),
		date_format( a.BOOK_TIME, '%Y' ) 
	ORDER BY
		date_format( a.BOOK_TIME, '%Y-%m' ) ASC 
	) now_sale
	
	LEFT JOIN (
	SELECT
		date_format( DATE_ADD( b.BOOK_TIME, INTERVAL 1 MONTH ), '%Y-%m' ) AS now_time,
		count(b.ID) as last_month_order_num
	FROM B2C_ORDER b 
	GROUP BY
		date_format( DATE_ADD( b.BOOK_TIME, INTERVAL 1 MONTH ), '%Y-%m' ) 
	ORDER BY
		date_format( DATE_ADD( b.BOOK_TIME, INTERVAL 1 MONTH ), '%Y-%m' ) ASC 
	) old_sale ON now_sale.now_time = old_sale.now_time
ORDER BY
	now_sale.now_year DESC,
	now_sale.now_time ASC
;
```



### 二、客户指标

#### 1.环比

（1）时间维度

```
--月客户量环比
SELECT
	now_sale.now_year as 年份,
	now_sale.now_time 具体时间,
CASE
		
		WHEN current_month_order_num IS NULL 
		OR current_month_order_num = 0 THEN
			0 ELSE current_month_order_num 
		END 本月客户量,
CASE
		
		WHEN last_month_order_num IS NULL 
		OR last_month_order_num = 0 THEN
			0 ELSE last_month_order_num 
		END 上个月客户量,

CASE
		
		WHEN last_month_order_num IS NULL 
		OR last_month_order_num = 0 THEN
			0 ELSE ( CONVERT ( ( ( current_month_order_num - last_month_order_num ) / last_month_order_num ) * 100, DECIMAL ( 10, 2 ) ) ) 
		END 月客户量环比
FROM
	(
	SELECT
		date_format( a.BOOK_TIME, '%Y-%m' ) AS now_time,
		date_format( a.BOOK_TIME, '%Y' ) AS now_year,
		count(DISTINCT a1.RECEIVER_PHONE) as current_month_order_num
	FROM
	 B2C_ORDER_RECEIVER a1 JOIN	B2C_ORDER a on a1.ORDER_ID=a.ID
	GROUP BY
		date_format( a.BOOK_TIME, '%Y-%m' ),
		date_format( a.BOOK_TIME, '%Y' ) 
	ORDER BY
		date_format( a.BOOK_TIME, '%Y-%m' ) ASC 
	) now_sale
	
	LEFT JOIN (
	SELECT
		date_format( DATE_ADD( b.BOOK_TIME, INTERVAL 1 MONTH ), '%Y-%m' ) AS now_time,
		count(DISTINCT b1.RECEIVER_PHONE) as last_month_order_num
	FROM B2C_ORDER b JOIN B2C_ORDER_RECEIVER b1 on b.ID=b1.ORDER_ID
	GROUP BY
		date_format( DATE_ADD( b.BOOK_TIME, INTERVAL 1 MONTH ), '%Y-%m' ) 
	ORDER BY
		date_format( DATE_ADD( b.BOOK_TIME, INTERVAL 1 MONTH ), '%Y-%m' ) ASC 
	) old_sale ON now_sale.now_time = old_sale.now_time
ORDER BY
	now_sale.now_year DESC,
	now_sale.now_time ASC
;
```

