### 看板一指标_11.6

操作语言：

hivesql

#### 一、合同金额（求和）

##### 1.时间

（1）每日

```sql
SELECT
	created_day,
	sum( transaction_amount ) AS total 
FROM
	crm_dwd.dwd_lnk_agreement t1
	JOIN crm_dws.dws_lnk_agreement_time t2 ON t1.row_id = t2.row_id 
GROUP BY
	created_day 
ORDER BY
	created_day DESC
```

```
【优化1.0】
SELECT
    t2.created_day, 
	sum( transaction_amount ) AS total 
FROM
	crm_dwd.dwd_lnk_agreement t1
	JOIN crm_dws.dws_lnk_agreement_time t2 ON t1.row_id = t2.row_id
	JOIN crm_dws.dws_lnk_accnt t3 ON t1.STORE_ID = t3.ROW_ID 
WHERE
   t3.PARENT_ACCT_ID != '86604943007420416'
   GROUP BY
	 t2.created_day 
ORDER BY
	 t2.created_day  DESC
```

（2）每周

```sql
select created_week,sum(transaction_amount) as total from crm_dwd.dwd_lnk_agreement  t1 
join crm_dws.dws_lnk_agreement_time t2 on t1.row_id = t2.row_id group by created_week
order by created_week desc 
```

```
【优化1.0】
SELECT
	t2.created_year,
	t2.created_week,
	sum( transaction_amount ) AS total 
FROM
	crm_dwd.dwd_lnk_agreement t1
	JOIN crm_dws.dws_lnk_agreement_time t2 ON t1.row_id = t2.row_id
	JOIN crm_dws.dws_lnk_accnt t3 ON t1.STORE_ID = t3.ROW_ID 
WHERE
	t3.PARENT_ACCT_ID != '86604943007420416' 
GROUP BY
	t2.created_year,
	t2.created_week 
ORDER BY
	t2.created_week DESC
```

（3）每月

```sql
SELECT
	sum( transaction_amount ) AS total,
	created_month 
FROM
	crm_dwd.dwd_lnk_agreement t1
	JOIN crm_dws.dws_lnk_agreement_time t2 ON t1.row_id = t2.row_id 
GROUP BY
	created_month 
ORDER BY
	created_month DESC
```

```
【优化1.0】
SELECT
	t2.created_year,
	t2.created_month,
	sum( transaction_amount ) AS total 
FROM
	crm_dwd.dwd_lnk_agreement t1
	JOIN crm_dws.dws_lnk_agreement_time t2 ON t1.row_id = t2.row_id
	JOIN crm_dws.dws_lnk_accnt t3 ON t1.STORE_ID = t3.ROW_ID 
WHERE
	t3.PARENT_ACCT_ID != '86604943007420416' 
GROUP BY
	t2.created_year,
	t2.created_month 
ORDER BY
	created_month DESC
```



（4）每季

```
select created_quarter,sum(transaction_amount) as total from crm_dwd.dwd_lnk_agreement  t1 join crm_dws.dws_lnk_agreement_time t2 on t1.row_id = t2.row_id group by created_quarter order by created_quarter desc 
```

```
【优化1.0】

```



（5）每年

```
select created_year,sum(transaction_amount) as total from crm_dwd.dwd_lnk_agreement  t1 join crm_dws.dws_lnk_agreement_time t2 on t1.row_id = t2.row_id group by created_quarter order by created_year desc 
```

```
【优化1.0】
SELECT
	created_year,
	sum( transaction_amount ) AS total 
FROM
	crm_dwd.dwd_lnk_agreement t1
	JOIN crm_dws.dws_lnk_agreement_time t2 ON t1.row_id = t2.row_id
	JOIN crm_dws.dws_lnk_accnt t3 ON t1.STORE_ID = t3.ROW_ID 
WHERE
   t3.PARENT_ACCT_ID != '86604943007420416'
   GROUP BY
     t2.created_year
ORDER BY
	created_year DESC
```



##### 2.组织

（1）办事处

```sql
select t4.org_name,sum(t1.transaction_amount) as total from crm_dwd.dwd_lnk_agreement t1 
join crm_dws.dws_shop t2 on t2.row_id = t1.store_id
join crm_dws.dws_dealer t3 on t2.parent_org_id = t3.row_id
join crm_dws.dws_salesoffice t4 on t3.parent_org_id = t4.row_id
group by t4.org_name
```

##### 3.客户经理

每个客户经理的合同金额


```
方式一（合同表-客户表-用户表）：
SELECT
    t4.fst_name,
	sum(t1.transaction_amount ) as total 
FROM
	crm_dwd.dwd_lnk_agreement t1
	LEFT JOIN crm_dws.dws_lnk_accnt t2 ON t1.STORE_ID = t2.ROW_ID
	LEFT JOIN crm_dws.dws_lnk_accnt t3 ON t2.PARENT_ACCT_ID = t3.ROW_ID
	LEFT JOIN crm_dws.dws_lnk_emp_info t4 ON UPPER( t3.CP_MANAGER_ID ) = t4.username
GROUP BY
    t4.fst_name
```

方式二

```
方式二：（合同表-门店表-经销商-办事处-用户表）
select f.fst_name, sum(a.transaction_amount) from crm_dwd.dwd_lnk_agreement a
join crm_dws.dws_shop b on a.store_id=b.row_id
join crm_dws.dws_dealer c on b.parent_org_id = c.row_id
join crm_dws.dws_lnk_accnt e on c.row_id =e.row_id
join crm_dws.dws_lnk_emp_info f on upper(e.cp_manager_id)=f.username
group by f.fst_name
```

#####4.时间+组织

（1）每月每个办事处的合同金额数

```
SELECT
   	t5.org_name,
	t6.created_month,
	sum( t1.transaction_amount )
FROM
	crm_dwd.dwd_lnk_agreement t1
	left join crm_dws.dws_lnk_agreement_time t6 on t1.row_id = t6.row_id
	LEFT JOIN crm_dws.dws_lnk_accnt t2 ON t1.STORE_ID = t2.ROW_ID
	LEFT JOIN crm_dws.dws_lnk_accnt t3 ON t2.PARENT_ACCT_ID = t3.ROW_ID
	LEFT JOIN crm_dws.dws_lnk_emp_info t4 ON UPPER( t3.CP_MANAGER_ID ) = t4.username
	LEFT JOIN crm_dws.dws_salesoffice t5 ON t5.ROW_ID = t3.PARENT_ORG_ID 
GROUP BY
	t6.created_month,
	t5.org_name 
```




##### 5.时间+组织+客户经理

（1）每个办事处每个客户经理每月的合同金额数

```
SELECT
	t5.org_name,
	t4.fst_name,
	t6.created_month,
	sum( t1.transaction_amount ) 
FROM
	crm_dwd.dwd_lnk_agreement t1
	LEFT JOIN crm_dws.dws_lnk_agreement_time t6 ON t1.row_id = t6.row_id
	LEFT JOIN crm_dws.dws_lnk_accnt t2 ON t1.STORE_ID = t2.ROW_ID
	LEFT JOIN crm_dws.dws_lnk_accnt t3 ON t2.PARENT_ACCT_ID = t3.ROW_ID
	LEFT JOIN crm_dws.dws_lnk_emp_info t4 ON UPPER( t3.CP_MANAGER_ID ) = t4.username
	LEFT JOIN crm_dws.dws_salesoffice t5 ON t5.ROW_ID = t3.PARENT_ORG_ID 
GROUP BY
	t4.fst_name,
	t6.created_month,
	t5.org_name
```

【仁】

```
select 
 t6.created_month,
 t5.org_name ,
 t4.fst_name,
 sum( t1.transaction_amount ) over (PARTITION by t4.row_id,t6.created_month ORDER by t6.created_month) as num
FROM
	crm_dwd.dwd_lnk_agreement t1
	left join crm_dws.dws_lnk_agreement_time t6 on t1.row_id = t6.row_id
	LEFT JOIN crm_dws.dws_lnk_accnt t2 ON t1.STORE_ID = t2.ROW_ID
	LEFT JOIN crm_dws.dws_lnk_accnt t3 ON t2.PARENT_ACCT_ID = t3.ROW_ID
	LEFT JOIN crm_dws.dws_lnk_emp_info t4 ON UPPER( t3.CP_MANAGER_ID ) = t4.username
	LEFT JOIN crm_dws.dws_salesoffice t5 ON t5.ROW_ID = t3.PARENT_ORG_ID

```

#### 二、目标值

##### 1.时间

> 日、周目标值暂时没有

（1）月目标值

```
SELECT date, sum( target_value ) AS total FROM crm_dws.dws_crm_target GROUP BY date
```

（2）年目标值

```
SELECT
	substr( date, 1, 4 ) AS year2,
	sum( target_value ) AS total 
FROM
	crm_dws.dws_crm_target 
GROUP BY
	substr( date, 1, 4 ) 
```

2.组织（办事处）

每个办事处的目标值（客户经理的目标汇总）

```
SELECT
	d.row_id,
	sum( target_value ) AS total 
FROM
	crm_dws.dws_crm_target a
	JOIN crm_dws.dws_lnk_emp_info b ON a.username = b.username
	JOIN crm_dws.dws_lnk_accnt c ON b.username = UPPER( c.CP_MANAGER_ID )
	JOIN crm_dws.dws_salesoffice d ON d.ROW_ID = c.PARENT_ORG_ID 
GROUP BY
	d.ROW_ID 
```



3.时间+组织

每个办事处每月的目标值（客户经理的目标汇总）





4.时间+客户经理

```
 --每个客户经理每个月的目标值
 SELECT
	username,
	substr( date, 1, 7 ) AS month2,
	sum( target_value ) AS total 
FROM
	crm_dws.dws_crm_target a 
GROUP BY
	username,
	substr( date, 1, 7 ) 
```





#### 三、差值（实际值-目标值）

#####1.时间+组织

（1）每月办事处的合同金额差值

```
-- 差值
SELECT
	temp1.org_name,
	temp1.month1,
	temp1.total,
	temp2.target,
	( temp1.total - temp2.target ) AS diff_value 
	from
(SELECT
   	t5.org_name,
	t6.created_month as month1,
	sum( t1.transaction_amount ) as total
FROM
	crm_dwd.dwd_lnk_agreement t1
	left join crm_dws.dws_lnk_agreement_time t6 on t1.row_id = t6.row_id
	LEFT JOIN crm_dws.dws_lnk_accnt t2 ON t1.STORE_ID = t2.ROW_ID
	LEFT JOIN crm_dws.dws_lnk_accnt t3 ON t2.PARENT_ACCT_ID = t3.ROW_ID
	LEFT JOIN crm_dws.dws_lnk_emp_info t4 ON UPPER( t3.CP_MANAGER_ID ) = t4.username
	LEFT JOIN crm_dws.dws_salesoffice t5 ON t5.ROW_ID = t3.PARENT_ORG_ID 
GROUP BY
	t6.created_month,
	t5.org_name )temp1
	left join
	(SELECT
 t6.org_name ,
 sum( t7.target_value ) AS target,
 substr( t7.date, 1, 7 ) as month2
FROM
	crm_dwd.dwd_lnk_agreement t1
	left join crm_dws.dws_lnk_agreement_time t2 on t1.row_id = t2.row_id
	LEFT JOIN crm_dws.dws_lnk_accnt t3 ON t1.STORE_ID = t3.ROW_ID
	LEFT JOIN crm_dws.dws_lnk_accnt t4 ON t3.PARENT_ACCT_ID = t4.ROW_ID
	LEFT JOIN crm_dws.dws_lnk_emp_info t5 ON UPPER( t4.CP_MANAGER_ID ) = t5.username
	LEFT JOIN crm_dws.dws_salesoffice t6 ON t6.ROW_ID = t4.PARENT_ORG_ID 
	left join crm_dws.dws_crm_target t7 on t7.username = t5.username   
GROUP BY
	 substr( t7.date, 1, 7 ),
	t6.org_name 
	order by t6.org_name )temp2
	on temp1.org_name = temp2.org_name and temp1.month1=temp2.month2
	order by temp1.org_name
```



#### 四、合同金额完成率

##### 1.时间

> 日、周完成率暂时不计算

（1）每月完成率（左上图）

```
SELECT
	month1,
	number,
	total,
	round( number / total, 2 ) AS ratio 
FROM
	(
SELECT
	created_month AS month1,
	sum( transaction_amount ) AS number 
FROM
	crm_dwd.dwd_lnk_agreement a
	JOIN crm_dws.dws_lnk_agreement_time b ON a.row_id = b.row_id 
GROUP BY
	created_month
	) t1
	JOIN ( SELECT date, sum( target_value ) AS total FROM crm_dws.dws_crm_target GROUP BY date ) t2 
WHERE
	month1 = t2.date 
```

（2）年累计完成率

> 计算逻辑：（当年到当前月合同金额总和）/（全年销售目标值）

```
SELECT
	year1,
	number,
	total,
	round( number / total, 2 ) AS ratio 
FROM
	(
SELECT
	created_year AS year1,
	sum( transaction_amount ) AS number 
FROM
	crm_dwd.dwd_lnk_agreement a
	JOIN crm_dws.dws_lnk_agreement_time b ON a.row_id = b.row_id 
GROUP BY
	created_year 
	) t1
	JOIN (
SELECT
	substr( date, 1, 4 ) AS year2,
	sum( target_value ) AS total 
FROM
	crm_dws.dws_crm_target 
GROUP BY
	substr( date, 1, 4 ) 
	) t2 
WHERE
	t1.year1 = t2.year2
```

（3）年滚动完成率

> 计算逻辑：（当年1月到当前月合同金额总和）/（当年1月到当前月总销售目标）

```
-- 年滚动完成率

SELECT
	t2.year2,
	t1.number,
	t2.total,
	round ( t1.number / t2.total, 2 ) AS ratio 
FROM
	(
SELECT
	created_year AS year1,
	sum( a.transaction_amount ) AS number 
FROM
	crm_dwd.dwd_lnk_agreement a
	JOIN crm_dws.dws_lnk_agreement_time b ON a.row_id = b.row_id 
GROUP BY
	created_year
	) t1 
	join
	( 	
SELECT
	substr(date,1,4) as year2,sum(target_value) as total 
FROM
	crm_dws.dws_crm_target 
WHERE substr(date,6,7)  BETWEEN 1 AND from_unixtime(unix_timestamp(),'MM')  
GROUP BY
	substr(date,1,4)) t2 
WHERE
	t1.year1 = t2.year2 
```

#####2.时间+组织

（1）每月办事处完成率

```sql

SELECT
	org_name,
	created_month,
	round ( temp1.number / temp2.total, 2 ) AS ratio 
FROM
	(
SELECT
	t4.row_id,
	t4.org_name,
	created_month,
	sum( transaction_amount ) AS number 
FROM
	crm_dwd.dwd_lnk_agreement t1
	JOIN crm_dws.dws_lnk_agreement_time b ON t1.row_id = b.row_id
	LEFT JOIN crm_dws.dws_lnk_accnt t2 ON t1.STORE_ID = t2.ROW_ID
	LEFT JOIN crm_dws.dws_lnk_accnt t3 ON t2.PARENT_ACCT_ID = t3.ROW_ID
	LEFT JOIN crm_dws.dws_salesoffice t4 ON t3.PARENT_ORG_ID = t4.ROW_ID 
GROUP BY
	t4.row_id,
	created_month,
	t4.org_name 
	) temp1
	LEFT JOIN (
SELECT
	d.row_id,
	substr( date, 1, 7 ) AS month2,
	sum( target_value ) AS total 
FROM
	crm_dws.dws_crm_target a
	JOIN crm_dws.dws_lnk_emp_info b ON a.username = b.username
	JOIN crm_dws.dws_lnk_accnt c ON b.username = UPPER( c.CP_MANAGER_ID )
	JOIN crm_dws.dws_salesoffice d ON d.ROW_ID = c.PARENT_ORG_ID 
GROUP BY
	d.ROW_ID,
	substr( date, 1, 7 ) 
	) temp2 ON temp1.created_month = temp2.month2 
	AND temp1.row_id = temp2.row_id
```

#####3.客户经理

（1）月完成率

```sql
SELECT DISTINCT
	fst_name,
	org_name,
	temp1.MONTH,
	round ( temp1.number / temp2.total, 2 ) AS ratio 
FROM
	(
SELECT
	t5.org_name,
	t4.username,
	t4.fst_name,
	created_month AS MONTH,
	sum( t1.transaction_amount ) over ( PARTITION BY t4.row_id, t6.created_month ) AS number 
FROM
	crm_dwd.dwd_lnk_agreement t1
	JOIN crm_dws.dws_lnk_agreement_time t6 ON t1.row_id = t6.row_id
	JOIN crm_dws.dws_lnk_accnt t2 ON t1.STORE_ID = t2.ROW_ID
	JOIN crm_dws.dws_lnk_accnt t3 ON t2.PARENT_ACCT_ID = t3.ROW_ID
	JOIN crm_dws.dws_lnk_emp_info t4 ON UPPER( t3.CP_MANAGER_ID ) = t4.username
	JOIN crm_dws.dws_salesoffice t5 ON t5.ROW_ID = t3.PARENT_ORG_ID 
	) temp1
	JOIN (
SELECT
	username,
	substr( date, 1, 7 ) AS month2,
	sum( target_value ) AS total 
FROM
	crm_dws.dws_crm_target a 
GROUP BY
	username,
	substr( date, 1, 7 ) 
	) temp2 ON temp1.username = temp2.username 
	AND temp1.MONTH = temp2.month2
```

（2）年累计完成率

每个客户经理的年累计完成率

```sql
SELECT DISTINCT
	fst_name,
	org_name,
	temp1.year1,
	round ( temp1.number / temp2.total, 2 ) AS ratio 
FROM
	(
SELECT
	t5.org_name,
	t4.username,
	t4.fst_name,
	created_year AS year1,
	sum( t1.transaction_amount ) over ( PARTITION BY t4.row_id, t6.created_year ) AS number 
FROM
	crm_dwd.dwd_lnk_agreement t1
	JOIN crm_dws.dws_lnk_agreement_time t6 ON t1.row_id = t6.row_id
	JOIN crm_dws.dws_lnk_accnt t2 ON t1.STORE_ID = t2.ROW_ID
	JOIN crm_dws.dws_lnk_accnt t3 ON t2.PARENT_ACCT_ID = t3.ROW_ID
	JOIN crm_dws.dws_lnk_emp_info t4 ON UPPER( t3.CP_MANAGER_ID ) = t4.username
	JOIN crm_dws.dws_salesoffice t5 ON t5.ROW_ID = t3.PARENT_ORG_ID 
	) temp1
	JOIN (
SELECT
	username,
	substr( date, 1, 4 ) AS year2,
	sum( target_value ) AS total 
FROM
	crm_dws.dws_crm_target a 
GROUP BY
	username,
	substr( date, 1, 4 ) 
	) temp2 ON temp1.username = temp2.username 
	AND temp1.year1 = temp2.year2
```

（3）年滚动完成率

```sql
-- 年滚动完成率
SELECT DISTINCT
	fst_name,
	org_name,
	temp1.year1,
	round ( temp1.number / temp2.total, 2 ) AS ratio 
FROM
	(
SELECT
	t5.org_name,
	t4.username,
	t4.fst_name,
	created_year AS year1,
	sum( t1.transaction_amount ) over ( PARTITION BY t4.row_id, t6.created_year ) AS number 
FROM
	crm_dwd.dwd_lnk_agreement t1
	LEFT JOIN crm_dws.dws_lnk_agreement_time t6 ON t1.row_id = t6.row_id
	LEFT JOIN crm_dws.dws_lnk_accnt t2 ON t1.STORE_ID = t2.ROW_ID
	LEFT JOIN crm_dws.dws_lnk_accnt t3 ON t2.PARENT_ACCT_ID = t3.ROW_ID
	LEFT JOIN crm_dws.dws_lnk_emp_info t4 ON UPPER( t3.CP_MANAGER_ID ) = t4.username
	LEFT JOIN crm_dws.dws_salesoffice t5 ON t5.ROW_ID = t3.PARENT_ORG_ID 
	) temp1
	LEFT JOIN (
SELECT
	username,
	substr( date, 1, 4 ) AS year2,
	sum( target_value ) AS total 
FROM
	crm_dws.dws_crm_target 
WHERE
	substr( date, 6, 7 ) BETWEEN 1 
	AND from_unixtime( unix_timestamp( ), 'MM' ) 
GROUP BY
	username,
	fst_name,
	substr( date, 1, 4 ) 
	) temp2 ON temp1.username = temp2.username 
	AND temp1.year1 = temp2.year2

```

#### *五、合同金额同比

#### * 六、合同金额环比

##### 1.时间

（1）月环比

```sql
select
t2.month,
t2.total,
t2.last_num,
t2.difference_value,
round(t2.difference_value/t2.last_num,2) as radio
from
(
SELECT t1.month,t1.total,
    t1.total-(lag(t1.total,1) OVER(ORDER BY t1.month)) as difference_value,
    lag(t1.total,1) OVER(ORDER BY t1.month) as last_num
FROM 
(
select sum(transaction_amount) as total,created_month as month  from crm_dwd.dwd_lnk_agreement  t1 
join crm_dws.dws_lnk_agreement_time t2 on t1.row_id = t2.row_id group by created_month
) t1
) t2 
;
```

##### 2.客户经理

（1）月环比





