### B2B业务指标_合同金额

操作语言：

hivesql

#### 一、求和

##### 1.时间

（1）每日

```sql
SELECT
	substr( order_date, 1,10 ) as day,
	sum( total_amount )  as total
FROM
	b2b_dwd.dwd_b2b_sale_order 
group by 
	substr( order_date, 1,10 )
```

（2）每周

```sql
-- 每周合同金额数
SELECT
    year( order_date) as year1,
	weekofyear( order_date) as week1,
	sum( total_amount )  as total
FROM
	b2b_dwd.dwd_b2b_sale_order 
group by
    year( order_date),
	weekofyear( order_date)
	order by
	week1
```

（3）每月

```sql
-- 每月合同金额数
SELECT 
	YEAR( order_date ) AS year1,
	MONTH ( order_date ) AS month1,
	sum( total_amount ) AS total 
FROM
	b2b_dwd.dwd_b2b_sale_order 
GROUP BY
	YEAR ( order_date ),
	MONTH ( order_date ) 
ORDER BY
	year1,
	month1
```

（4）每年

```sql
-- 每年合同金额数
SELECT 
    YEAR( order_date ) AS year1,
	sum( total_amount ) AS total 
FROM
	b2b_dwd.dwd_b2b_sale_order 
GROUP BY
	YEAR ( order_date )
```

##### 2.办事处

每个办事处的合同金额

```sql
-- 每个办事处的合同金额
SELECT
	t2.NAME,
	sum( total_amount ) 
FROM
	b2b_dwd.dwd_b2b_sale_order t1
	JOIN b2b_dws.dws_base_organization t2 ON t1.business_agency_id = t2.id 
GROUP BY
	t2.NAME
```

#####3.时间+办事处

（1）每月每个办事处的合同金额数

```
-- 每个办事处每个月的合同金额
SELECT
	t2.NAME,
	from_unixtime( unix_timestamp(order_date), 'yyyy-MM' ) AS month1,
	sum( total_amount ) AS total 
FROM
	b2b_dwd.dwd_b2b_sale_order t1
	JOIN b2b_dws.dws_base_organization t2 ON t1.business_agency_id = t2.id 
GROUP BY
	t2.NAME,
	from_unixtime( unix_timestamp(order_date), 'yyyy-MM' ) 
ORDER BY
	t2.NAME,
	month1
```

##### 4.办事处+客户经理

（1）每个办事处每个客户经理的合同金额（直营办）


```
-- 每个办事处的客户经理的合同金额（直营办）
SELECT
	t3.NAME,
	t2.NAME,
	sum( t1.TOTAL_AMOUNT ) as total
FROM
	b2b_dwd.dwd_b2b_sale_order t1
	JOIN b2b_dws.dws_b2b_base_person t2 ON t1.SALE_MANAGER_ID = t2.id
	JOIN b2b_dws.dws_base_organization t3 ON t1.BUSINESS_AGENCY_ID = t3.id 
GROUP BY
	t2.NAME,
	t3.NAME 
ORDER BY
	t3.NAME
```


##### 5.时间+办事处+客户经理

（1）每个办事处每个客户经理每月的合同金额数(直营办)

```
-- 每个办事处的客户经理每月的合同金额
SELECT
	t3.NAME,
	t2.NAME,
	from_unixtime( unix_timestamp(order_date), 'yyyy-MM' )  AS month1,
	sum( t1.TOTAL_AMOUNT ) as total
FROM
	b2b_dwd.dwd_b2b_sale_order t1
	JOIN b2b_dws.dws_b2b_base_person t2 ON t1.SALE_MANAGER_ID = t2.id
	JOIN b2b_dws.dws_base_organization t3 ON t1.BUSINESS_AGENCY_ID = t3.id 
GROUP BY
	t2.NAME,
	t3.NAME,
	from_unixtime( unix_timestamp(order_date), 'yyyy-MM' ) 
ORDER BY
	t3.NAME,
	t2.NAME,
	month1
```



##### 6.办事处+客户经理（独资办）

每个办事处每个客户经理的合同金额（独资办）

```
SELECT
	t4.CODE as office_code,
	t4.NAME as office_name,
	t5.NAME as manager_name,
	sum( TOTAL_AMOUNT ) as total
FROM
	b2b_dws.dws_base_account t1
	JOIN b2b_dws.dws_base_cust_doc_def t2 ON t1.account_type = t2.id
	JOIN b2b_dws.dws_base_account_mg_org t3 ON t1.id = t3.ACCOUNT_ID
	JOIN b2b_dws.dws_base_organization t4 ON t3.ORGANIZATION_ID = t4.ID
	JOIN b2b_dws.dws_b2b_base_person t5 ON t3.PERSON_ID = t5.ID
	JOIN b2b_dwd.dwd_b2b_sale_order t6 ON t4.ID = t6.BUSINESS_AGENCY_ID 
WHERE
	t2.CODE != '2' 
	AND t1.DR = 0 
	AND t1.STATUS = 1 
	AND t3.DR = 0 
	AND t4.CODE != '6800' 
	AND t4.CODE != '6805' 
GROUP BY
	t5.NAME,
	t4.NAME,
	t4.CODE
```

##### 7.时间+组织+客户经理（独资办）

每个办事处每个客户经理每月的合同金额数(独资办)

```
SELECT
	t4.CODE as office_code,
	t4.NAME as office_name,
	t5.NAME as manager_name,
	from_unixtime( unix_timestamp(order_date), 'yyyy-MM' ) AS month1,
	sum( TOTAL_AMOUNT ) as total
FROM
	b2b_dws.dws_base_account t1
	JOIN b2b_dws.dws_base_cust_doc_def t2 ON t1.account_type = t2.id
	JOIN b2b_dws.dws_base_account_mg_org t3 ON t1.id = t3.ACCOUNT_ID
	JOIN b2b_dws.dws_base_organization t4 ON t3.ORGANIZATION_ID = t4.ID
	JOIN b2b_dws.dws_b2b_base_person t5 ON t3.PERSON_ID = t5.ID
	JOIN b2b_dwd.dwd_b2b_sale_order t6 ON t4.ID = t6.BUSINESS_AGENCY_ID 
WHERE
	t2.CODE != '2' 
	AND t1.DR = 0 
	AND t1.STATUS = 1 
	AND t3.DR = 0 
	AND t4.CODE != '6800' 
	AND t4.CODE != '6805' 
GROUP BY
	t5.NAME,
	t4.NAME,
	t4.CODE,
	from_unixtime( unix_timestamp(order_date), 'yyyy-MM' ) 
```



#### 二、目标值

##### 1.时间

> 日、周目标值暂时没有

（1）月目标值

```
SELECT
	month,
	sum( target_value ) AS total 
FROM
	b2b_dws.dws_b2b_target_office 
GROUP BY
	month
```

（2）年目标值

```
SELECT
	substr( t1.month, 1, 4 ) AS year1,
	sum( target_value ) AS total 
FROM
	b2b_dws.dws_b2b_target_office t1 
GROUP BY
	substr( t1.month, 1, 4 )
```

##### 2.办事处

每个办事处的目标值

```
SELECT
	name,
	sum( target_value ) AS total 
FROM
	b2b_dws.dws_b2b_target_office 
WHERE
	target_value IS NOT NULL 
GROUP BY
	name
```

每个办事处每月的目标值（源）

```
-- 每个办事处的每月目标值(源)
SELECT 
	CODE,
	NAME,
	MONTH,
	target_value 
FROM
	b2b_dws.dws_b2b_target_office 
WHERE
	target_value IS NOT NULL
```

#####3.客户经理

```
-- 每个办事处客户经理的目标值
SELECT
office_code,
office_name,
manager_name,
sum( target_value ) AS total 
FROM
	b2b_dws.dws_b2b_target_manager
   where target_value is not null
GROUP BY
	office_code,
office_name,
manager_name
```

```
 --每个办事处客户经理每个月的目标值（源）
SELECT
	office_code,
	office_name,
	manager_name,
	MONTH,
	target_value 
FROM
	b2b_dws.dws_b2b_target_manager
```

#### 三、差值（实际值-目标值）

#####1.时间+办事处

（1）每个办事处每月的合同金额差值

```
SELECT
	temp1.name,
	temp1.month1,
	temp1.total,
	temp2.target_value,
	( temp1.total - temp2.target_value ) AS diff 
FROM
	(
SELECT
	t2.NAME,
	from_unixtime ( unix_timestamp ( order_date ), 'yyyy-MM' ) AS month1,
	sum( total_amount ) AS total 
FROM
	b2b_dwd.dwd_b2b_sale_order t1 JOIN b2b_dws.dws_b2b_target_office t2 ON t1.business_agency_id = t2.id 
GROUP BY
	t2.NAME,
	from_unixtime ( unix_timestamp ( order_date ), 'yyyy-MM' )) temp1 join ( SELECT CODE, NAME, MONTH, target_value FROM b2b_dws.dws_b2b_target_office ) temp2 ON 1 = 1 
	AND temp1.name = temp2.name 
	AND temp1.month1 = temp2.month
```



#### 四、合同金额完成率（占比）

##### 1.时间

> 日、周完成率暂时不计算

（1）每月完成率

```
-- 每月完成率
SELECT
	t1.month1,
	t1.total,
	t2.target,
	round( total / target, 4 ) AS month_ratio 
FROM
	(
SELECT
	from_unixtime( unix_timestamp( order_date ), 'yyyy-MM' ) AS month1,
	sum( total_amount ) AS total 
FROM
	b2b_dwd.dwd_b2b_sale_order 
GROUP BY
	from_unixtime( unix_timestamp( order_date ), 'yyyy-MM' ) 
	) t1
	JOIN ( SELECT MONTH, sum( target_value ) AS target FROM b2b_dws.dws_b2b_target_office GROUP BY MONTH ) t2 ON 1 = 1 
	AND t1.month1 = t2.MONTH
```

（2）年累计完成率

> 计算逻辑：（当年到当前月合同金额总和）/（全年销售目标值）

```
-- 年度累计完成率
SELECT
	year1,
	total,
	target,
	round( total / target, 4 ) AS year_ratio 
FROM
	(
SELECT YEAR
	( t1.order_date ) AS year1,
	sum( t1.total_amount ) AS total 
FROM
	b2b_dwd.dwd_b2b_sale_order t1 
GROUP BY
	YEAR ( t1.order_date ) 
	) temp1
	JOIN (
SELECT
	substr( t2.MONTH, 1, 4 ) AS year2,
	sum( target_value ) AS target 
FROM
	b2b_dws.dws_b2b_target_office t2 
GROUP BY
	substr( t2.MONTH, 1, 4 ) 
	) temp2 ON 1 = 1 
	AND temp1.year1 = temp2.year2
```

（3）年滚动完成率

> 计算逻辑：（当年1月到当前月合同金额总和）/（当年1月到当前月总销售目标）

```
SELECT
	year1,
	total,
	target,
	round( total / target, 4 ) AS yearroll_ratio 
FROM
	( SELECT 
		YEAR ( order_date ) AS year1, 
		sum( total_amount ) AS total 
	FROM 
		b2b_dwd.dwd_b2b_sale_order 
	GROUP BY 
		YEAR ( order_date ) ) temp1
	JOIN (
SELECT
	substr( t1.MONTH, 1, 4 ) AS year2,
	sum( target_value ) AS target 
FROM
	b2b_dws.dws_b2b_target_office t1 
WHERE
	substr( t1.MONTH, 6, 7 ) BETWEEN 1 
	AND from_unixtime( unix_timestamp( ), 'MM' ) 
GROUP BY
	substr( t1.MONTH, 1, 4 ) 
	) temp2 ON 1 = 1 
	AND temp1.year1 = temp2.year2
```

#####2.时间+组织

（1）每月办事处实际值目标值完成率

```sql
select
code,
name1,
month1,
total,
target,
round( total / target, 4 ) AS month_ratio
from
(SELECT
	t2.NAME as name1,
	from_unixtime( unix_timestamp(order_date), 'yyyy-MM' ) AS month1,
	sum( total_amount ) AS total 
FROM
	b2b_dwd.dwd_b2b_sale_order t1
	JOIN b2b_dws.dws_base_organization t2 ON t1.business_agency_id = t2.id 
GROUP BY
	t2.NAME,
	from_unixtime( unix_timestamp(order_date), 'yyyy-MM' ) 
ORDER BY
	t2.NAME,
	month1)temp1
join
(SELECT 
	CODE,
	NAME as name2,
	MONTH,
	target_value as target
FROM
	b2b_dws.dws_b2b_target_office)temp2 on 1=1 and temp1.month1=temp2.month and temp1.name1 = temp2.name2
order by name1
```

#####3.客户经理（直营办）

（1）每个客户经理月完成率

```sql
SELECT
	office_name1,
	manager_name1,
	month1,
	total,
	target,
	round( total / target, 4 ) AS month_ratio 
FROM
	(
SELECT
	t3.NAME AS office_name1,
	t2.NAME AS manager_name1,
	from_unixtime( unix_timestamp( order_date ), 'yyyy-MM' ) AS month1,
	sum( t1.TOTAL_AMOUNT ) AS total 
FROM
	b2b_dwd.dwd_b2b_sale_order t1
	JOIN b2b_dws.dws_b2b_base_person t2 ON t1.SALE_MANAGER_ID = t2.id
	JOIN b2b_dws.dws_base_organization t3 ON t1.BUSINESS_AGENCY_ID = t3.id 
GROUP BY
	t2.NAME,
	t3.NAME,
	from_unixtime( unix_timestamp( order_date ), 'yyyy-MM' ) 
ORDER BY
	t3.NAME,
	t2.NAME,
	month1 
	) temp1
	JOIN ( SELECT office_code, office_name, manager_name, MONTH, target_value AS target FROM b2b_dws.dws_b2b_target_manager ) temp2 ON 1 = 1 
	AND temp1.office_name1 = temp2.office_name 
	AND temp1.manager_name1 = temp2.manager_name 
	AND temp1.month1 = temp2.MONTH
```

（2）年累计完成率

每个客户经理的年累计完成率

```sql
SELECT
	year1,
	office_name1,
	manager_name1,
	total,
	target,
	round( total / target, 4 ) AS manager_year_ratio 
FROM
	(
SELECT YEAR
	( t1.order_date ) AS year1,
	t3.NAME AS office_name1,
	t2.NAME AS manager_name1,
	sum( t1.TOTAL_AMOUNT ) AS total 
FROM
	b2b_dwd.dwd_b2b_sale_order t1
	JOIN b2b_dws.dws_b2b_base_person t2 ON t1.SALE_MANAGER_ID = t2.id
	JOIN b2b_dws.dws_base_organization t3 ON t1.BUSINESS_AGENCY_ID = t3.id 
GROUP BY
	YEAR ( t1.order_date ),
	t2.NAME,
	t3.NAME 
	) temp1
	JOIN (
SELECT
	substr( MONTH, 1, 4 ) AS year2,
	office_name,
	manager_name,
	sum( target_value ) AS target 
FROM
	b2b_dws.dws_b2b_target_manager 
WHERE
	target_value IS NOT NULL 
GROUP BY
	substr( MONTH, 1, 4 ),
	office_name,
	manager_name 
	) temp2 ON 1 = 1 
	AND temp1.year1 = temp2.year2 
	AND temp1.office_name1 = temp2.office_name 
	AND temp1.manager_name1 = temp2.manager_name
```

（3）年滚动完成率

```sql
SELECT
	year1,
	office_name1,
	manager_name1,
	total,
	target,
	round( total / target, 4 ) AS manager_yearroll_ratio 
FROM
	(
SELECT YEAR
	( t1.order_date ) AS year1,
	t3.NAME AS office_name1,
	t2.NAME AS manager_name1,
	sum( t1.TOTAL_AMOUNT ) AS total 
FROM
	b2b_dwd.dwd_b2b_sale_order t1
	JOIN b2b_dws.dws_b2b_base_person t2 ON t1.SALE_MANAGER_ID = t2.id
	JOIN b2b_dws.dws_base_organization t3 ON t1.BUSINESS_AGENCY_ID = t3.id 
GROUP BY
	YEAR ( t1.order_date ),
	t2.NAME,
	t3.NAME 
	) temp1
	JOIN (
SELECT
	substr( MONTH, 1, 4 ) AS year2,
	office_name,
	manager_name,
	sum( target_value ) AS target 
FROM
	b2b_dws.dws_b2b_target_manager t1 
WHERE
	substr( t1.MONTH, 6, 7 ) BETWEEN 1 
	AND from_unixtime( unix_timestamp( ), 'MM' ) 
GROUP BY
	substr( MONTH, 1, 4 ),
	office_name,
	manager_name 
	) temp2 ON 1 = 1 
	AND temp1.year1 = temp2.year2 
	AND temp1.office_name1 = temp2.office_name 
	AND temp1.manager_name1 = temp2.manager_name
```

##### 4.客户经理（独资办）

（1）每个客户经理的月完成率

```sql
SELECT
	office_code1,
	office_name1,
	manager_name1,
	month1,
	total,
	target,
	round( total / target, 4 ) AS manager_month_ratio 
FROM
	(
SELECT
	t4.CODE AS office_code1,
	t4.NAME AS office_name1,
	t5.NAME AS manager_name1,
	from_unixtime( unix_timestamp( order_date ), 'yyyy-MM' ) AS month1,
	sum( TOTAL_AMOUNT ) AS total 
FROM
	b2b_dws.dws_base_account t1
	JOIN b2b_dws.dws_base_cust_doc_def t2 ON t1.account_type = t2.id
	JOIN b2b_dws.dws_base_account_mg_org t3 ON t1.id = t3.ACCOUNT_ID
	JOIN b2b_dws.dws_base_organization t4 ON t3.ORGANIZATION_ID = t4.ID
	JOIN b2b_dws.dws_b2b_base_person t5 ON t3.PERSON_ID = t5.ID
	JOIN b2b_dwd.dwd_b2b_sale_order t6 ON t4.ID = t6.BUSINESS_AGENCY_ID 
WHERE
	t2.CODE != '2' 
	AND t1.DR = 0 
	AND t1.STATUS = 1 
	AND t3.DR = 0 
	AND t4.CODE != '6800' 
	AND t4.CODE != '6805' 
GROUP BY
	t5.NAME,
	t4.NAME,
	t4.CODE,
	from_unixtime( unix_timestamp( order_date ), 'yyyy-MM' ) 
	) temp1
	JOIN ( SELECT office_code, office_name, manager_name, MONTH, target_value AS target
FROM b2b_dws.dws_b2b_target_manager ) temp2 ON 1 = 1 
	AND temp1.office_name1 = temp2.office_name 
	AND temp1.manager_name1 = temp2.manager_name 
	AND temp1.month1 = temp2.MONTH
```

（2）年累计完成率

每个客户经理的年累计完成率

```sql
select 
year1,
office_name1,
manager_name1,
total,
target,
round( total / target, 4 ) AS manager_year_ratio 
from
(
SELECT
YEAR( t6.order_date ) AS year1,
	t4.CODE as office_code1,
	t4.NAME as office_name1,
	t5.NAME as manager_name1,
	sum( TOTAL_AMOUNT ) as total
FROM
	b2b_dws.dws_base_account t1
	JOIN b2b_dws.dws_base_cust_doc_def t2 ON t1.account_type = t2.id
	JOIN b2b_dws.dws_base_account_mg_org t3 ON t1.id = t3.ACCOUNT_ID
	JOIN b2b_dws.dws_base_organization t4 ON t3.ORGANIZATION_ID = t4.ID
	JOIN b2b_dws.dws_b2b_base_person t5 ON t3.PERSON_ID = t5.ID
	JOIN b2b_dwd.dwd_b2b_sale_order t6 ON t4.ID = t6.BUSINESS_AGENCY_ID 
WHERE
	t2.CODE != '2' 
	AND t1.DR = 0 
	AND t1.STATUS = 1 
	AND t3.DR = 0 
	AND t4.CODE != '6800' 
	AND t4.CODE != '6805' 
GROUP BY
YEAR( t6.order_date ),
	t5.NAME,
	t4.NAME,
	t4.CODE)temp1
join(
SELECT
	office_name,
	manager_name,
	substr(MONTH,1,4) as year2,
	sum(target_value) as target
FROM
	b2b_dws.dws_b2b_target_manager
	group by 
	substr(MONTH,1,4),
	office_name,
	manager_name,
	substr(MONTH,1,4)
)temp2 on 1=1 
    AND temp1.office_name1 = temp2.office_name 
	AND temp1.manager_name1 = temp2.manager_name 
	AND temp1.year1 = temp2.year2
```

（3）年滚动完成率

```sql
SELECT
	year1,
	office_name1,
	manager_name1,
	total,
	target,
	round( total / target, 4 ) AS manager_year_ratio 
FROM
	(
SELECT YEAR
	( t6.order_date ) AS year1,
	t4.CODE AS office_code1,
	t4.NAME AS office_name1,
	t5.NAME AS manager_name1,
	sum( TOTAL_AMOUNT ) AS total 
FROM
	b2b_dws.dws_base_account t1
	JOIN b2b_dws.dws_base_cust_doc_def t2 ON t1.account_type = t2.id
	JOIN b2b_dws.dws_base_account_mg_org t3 ON t1.id = t3.ACCOUNT_ID
	JOIN b2b_dws.dws_base_organization t4 ON t3.ORGANIZATION_ID = t4.ID
	JOIN b2b_dws.dws_b2b_base_person t5 ON t3.PERSON_ID = t5.ID
	JOIN b2b_dwd.dwd_b2b_sale_order t6 ON t4.ID = t6.BUSINESS_AGENCY_ID 
WHERE
	t2.CODE != '2' 
	AND t1.DR = 0 
	AND t1.STATUS = 1 
	AND t3.DR = 0 
	AND t4.CODE != '6800' 
	AND t4.CODE != '6805' 
GROUP BY
	YEAR ( t6.order_date ),
	t5.NAME,
	t4.NAME,
	t4.CODE 
	) temp1
	JOIN (
SELECT
	office_name,
	manager_name,
	substr( MONTH, 1, 4 ) AS year2,
	sum( target_value ) AS target 
FROM
	b2b_dws.dws_b2b_target_manager 
WHERE
	substr( MONTH, 6, 7 ) BETWEEN 1 
	AND from_unixtime( unix_timestamp( ), 'MM' ) 
GROUP BY
	substr( MONTH, 1, 4 ),
	office_name,
	manager_name,
	substr( MONTH, 1, 4 ) 
	) temp2 ON 1 = 1 
	AND temp1.office_name1 = temp2.office_name 
	AND temp1.manager_name1 = temp2.manager_name 
	AND temp1.year1 = temp2.year2
```

##### 

