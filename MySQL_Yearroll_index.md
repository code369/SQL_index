### MySQL_yearrollratio_index
统计时间维度的完成率指标，添加了滚动年指标的求法

```sql

-- （1）每月完成率
-- 【写法一】

SELECT
	month1,
	number,
	total,
	round( number / total, 4 ) AS ratio 
FROM
	(
SELECT
	DATE_FORMAT( a.created, '%Y-%m' ) AS month1,
	sum( transaction_amount ) AS number 
FROM
	lnk_agreement a
GROUP BY
	DATE_FORMAT( created, '%Y-%m' ) 
	) t1
	JOIN ( SELECT date, sum( target_value ) AS total 
          FROM crm_target_office GROUP BY date ) t2 
WHERE
	DATE_FORMAT( CONCAT( month1, '-01' ), '%Y-%m' ) = t2.date

-- 【写法二】
SELECT
	t2.date,
	t1.number,
	t2.total,
	round ( t1.number / t2.total, 4 ) AS ratio 
FROM
	(
SELECT
	DATE_FORMAT( a.created, '%Y-%m' ) AS month1,
	sum( transaction_amount ) AS number 
FROM
	lnk_agreement a
GROUP BY
	DATE_FORMAT( created, '%Y-%m' ) 
	) t1,
	( SELECT sum( target_value ) AS total, 
     date FROM crm_target_office 
     GROUP BY date ) t2 
WHERE
	t1.month1 = t2.date 
GROUP BY
	t2.date
```
-- （2）年累计完成率
```
-- 计算逻辑：（当年到当前月合同金额总和）/（全年销售目标值）
-- 方式一

SELECT
	t2.year2,
	t1.number,
	t2.total,
	round ( t1.number / t2.total, 4 ) AS ratio 
FROM
	(
SELECT
	DATE_FORMAT( a.created, '%Y' ) AS year1,
	sum( transaction_amount ) AS number 
FROM
	lnk_agreement a
GROUP BY
	DATE_FORMAT( a.created, '%Y' ) 
	) t1 join
	( SELECT sum( target_value ) AS total, DATE_FORMAT(CONCAT(crm_target_office.date,'-01') ,'%Y') as year2 
     FROM crm_target_office GROUP BY year2 ) t2 
WHERE
	t1.year1 = t2.year2 


-- 【方式二】

SELECT
	t2.year2,
	t1.number,
	t2.total,
	round ( t1.number / t2.total, 4) AS ratio 
FROM
	(
SELECT
	DATE_FORMAT( a.created, '%Y' ) AS year1,
	sum( transaction_amount ) AS number 
FROM
	lnk_agreement a
GROUP BY
	DATE_FORMAT( created, '%Y' ) 
	) t1,
	( SELECT sum( target_value ) AS total, DATE_FORMAT(CONCAT(crm_target_office.date,'-01') ,'%Y') as year2 
     FROM crm_target_office GROUP BY year2 ) t2 
WHERE
	t1.year1 = t2.year2 
GROUP BY
	t2.year2
```
-- （4）年滚动完成率

 ```
-- 计算逻辑：（当年1月到当前月合同金额总和）/（当年1月到当前月总销售目标）
SELECT
	t2.year2,
	t1.number,
	t2.total,
	round ( t1.number / t2.total, 4 ) AS ratio 
FROM
	(
SELECT
	DATE_FORMAT( a.created, '%Y' ) AS year1,
	sum( transaction_amount ) AS number 
FROM
	lnk_agreement a
GROUP BY
	DATE_FORMAT( a.created, '%Y' ) 
	) t1 
	join
	( 	
SELECT
	DATE_FORMAT(CONCAT(crm_target_office.date,'-01') ,'%Y') as year2, 
	sum(target_value) as total 
FROM
	crm_target_office
WHERE DATE_FORMAT(CONCAT(crm_target_office.date,'-01') ,'%m') 
        BETWEEN 1 AND date_format(NOW(), '%m')
GROUP BY
	DATE_FORMAT(CONCAT(crm_target_office.date,'-01') ,'%Y')) t2 
on
	t1.year1 = t2.year2 
 ```

