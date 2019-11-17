





## Oracle_index

>Auth: 清平乐
>Data：2019-11-17
>Email：qingpingyue01@163.com
>github：https://github.com/code369

####  一、汇总（时间维度）

```sql
-- （1）每日
-- 查询8月份以后每日的合同金额数
-- 【oracle版】
SELECT
	TO_CHAR( t1.ORDER_DATE, 'yyyy-MM-dd' ) AS day1,
	sum( t1.TOTAL_AMOUNT ) AS total 
FROM
	MPLATFORM.b2b_sale_order t1 
WHERE
	TO_CHAR( t1.ORDER_DATE, 'yyyy-MM-dd' ) >= '2019-08-01'  
GROUP BY
	TO_CHAR( t1.ORDER_DATE, 'yyyy-MM-dd' );

-- 方式二
SELECT
	TO_CHAR( t1.ORDER_DATE, 'yyyy-MM-dd' ) AS day1,
	sum( t1.TOTAL_AMOUNT ) AS total 
FROM
	MPLATFORM.b2b_sale_order t1 
WHERE
	t1.ORDER_DATE > to_date ( '2019-08-01', 'yyyy-mm-dd' )  
GROUP BY
	TO_CHAR( t1.ORDER_DATE, 'yyyy-MM-dd' );
```
(2)查询每周的合同金额
```sql
-- （2）每周
SELECT
	TO_CHAR( t1.ORDER_DATE, 'yyyy' ) as year1,
	TO_CHAR( t1.ORDER_DATE, 'WW' ) AS week1,
	sum( t1.TOTAL_AMOUNT ) AS total 
FROM
	MPLATFORM.b2b_sale_order t1 
GROUP BY
	TO_CHAR( t1.ORDER_DATE, 'yyyy' ),
	TO_CHAR( t1.ORDER_DATE, 'WW' ) 
```
-- （3）每月

```sql
-- 【oracle版】
SELECT
	TO_CHAR( t1.ORDER_DATE, 'yyyy-MM' ) AS month1,
	sum( t1.TOTAL_AMOUNT ) AS total 
FROM
	MPLATFORM.b2b_sale_order t1 	
GROUP BY
	TO_CHAR( t1.ORDER_DATE, 'yyyy-MM' )
```

（4）每季度

```sql
SELECT
	TO_CHAR ( t1.ORDER_DATE, 'yyyy' ) AS year1,
	to_char ( t1.order_date, 'q' ) AS quarter1,
	sum( total_amount ) AS total 
FROM
	MPLATFORM.b2b_sale_order t1 
GROUP BY
	order_date,
	TO_CHAR ( t1.ORDER_DATE, 'yyyy' ),
	to_char ( t1.order_date, 'q' )
```

-- （5）每年

```sql
-- 【oracle版】
SELECT
	TO_CHAR ( t1.ORDER_DATE, 'yyyy' ) AS year1,
	sum( TOTAL_AMOUNT ) AS total 
FROM
	MPLATFORM.b2b_sale_order t1 
GROUP BY
	TO_CHAR ( t1.ORDER_DATE, 'yyyy' ); 
```

#### 二、占比

```
--查询各个品类的销量占比

【oracle版本】
SELECT NAME AS 品类名称,
	num AS 销售量,
 round( num / total * 100, 2 ) 占比 
FROM
	(
SELECT
	* 
FROM
	(
SELECT
	a.NAME,
	sum( a.buy_num ) num 
FROM
	BASE_PRODUCT_CATEGORY a
GROUP BY
	a.NAME 
	) t1
	CROSS JOIN ( SELECT sum( a.buy_num ) total FROM B2C_ORDER_PRODUCT a ) t2 
	) t3 
ORDER BY
	占比 DESC;

【MySQL版本】
		
SELECT 
	NAME AS品类名称,
	num AS 销售量,
  concat(	format( num / total * 100, 2 ),'%') 占比 
FROM
	(
SELECT
	* 
FROM
	(
SELECT
	a.NAME,
	sum( a.buy_num ) num 
FROM
	BASE_PRODUCT_CATEGORY a
GROUP BY
	a.NAME 
	) t1
inner join ( SELECT sum( a.buy_num ) total FROM B2C_ORDER_PRODUCT a ) t2 
	on 1=1) t3 
ORDER BY
	占比 DESC;


```

