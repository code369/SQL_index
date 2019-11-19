###CRM客户指标（MySQL版）11.19

### 一、订单客户数

```sql
-- 订单客户数
SELECT
	count( row_id ) 
FROM
	qy_clue_account 
WHERE
	FILING_DATE IS NOT NULL 
	AND BELONG_ORG IS NOT NULL 
	AND down_pay_time IS NOT NULL 
	AND opty_state = 'Normal' 
	AND consumer_type IN ( 'DepositCustomer', 'ContractualCustomer', 'FinishedProductClue' );
-- 1.时间
-- 
-- （1）每日
SELECT
	DATE_FORMAT( date( FILING_DATE ), '%Y-%m-%d' ) AS day1,
	count( row_id ) AS total 
FROM
	qy_clue_account 
WHERE
	FILING_DATE IS NOT NULL 
	AND BELONG_ORG IS NOT NULL 
	AND down_pay_time IS NOT NULL 
	AND opty_state = 'Normal' 
	AND consumer_type IN ( 'DepositCustomer', 'ContractualCustomer', 'FinishedProductClue' ) 
GROUP BY
	day1;
-- (2)每周
SELECT
	DATE_FORMAT( date( FILING_DATE ), '%Y' ) AS year1,
	WEEK ( FILING_DATE ) AS week1,
	count( row_id ) AS total 
FROM
	qy_clue_account 
WHERE
	FILING_DATE IS NOT NULL 
	AND BELONG_ORG IS NOT NULL 
	AND down_pay_time IS NOT NULL 
	AND opty_state = 'Normal' 
	AND consumer_type IN ( 'DepositCustomer', 'ContractualCustomer', 'FinishedProductClue' ) 
GROUP BY
	DATE_FORMAT( date( FILING_DATE ), '%Y' ),
	WEEK ( FILING_DATE )
	
-- （2）每月
SELECT
	DATE_FORMAT( date( FILING_DATE ), '%Y-%m' ) AS month1,
	count( row_id ) AS total 
FROM
	qy_clue_account 
WHERE
	FILING_DATE IS NOT NULL 
	AND BELONG_ORG IS NOT NULL
	AND down_pay_time IS NOT NULL 
	AND opty_state = 'Normal' 
	AND consumer_type IN ( 'DepositCustomer', 'ContractualCustomer', 'FinishedProductClue' ) 
GROUP BY
	month1;
-- 	（4）每年
SELECT
	DATE_FORMAT( date( FILING_DATE ), '%Y' ) AS year1,
	count( row_id ) AS total 
FROM
	qy_clue_account 
WHERE
	FILING_DATE IS NOT NULL 
	AND BELONG_ORG IS NOT NULL
	AND down_pay_time IS NOT NULL 
	AND opty_state = 'Normal' 
	AND consumer_type IN ( 'DepositCustomer', 'ContractualCustomer', 'FinishedProductClue' ) 
GROUP BY
	year1;
	
-- 2.办事处+时间 
-- （1）每日
SELECT
	DATE_FORMAT( date( FILING_DATE ), '%Y-%m-%d' ) AS day1,
	t4.org_name,
	count( t1.ROW_ID ) AS total 
FROM
	qy_clue_account t1
	JOIN lnk_accnt t3 ON t1.belong_org = t3.ROW_ID
	JOIN lnk_org_ext t4 ON t3.PARENT_ORG_ID = t4.ROW_ID 
WHERE
	FILING_DATE IS NOT NULL 
	AND BELONG_ORG IS NOT NULL 
	AND down_pay_time IS NOT NULL 
	AND opty_state = 'Normal' 
	AND t1.consumer_type IN ( 'DepositCustomer', 'ContractualCustomer', 'FinishedProductClue' ) 
GROUP BY
	DATE_FORMAT( date( FILING_DATE ), '%Y-%m-%d' ),
	t4.org_name
	
-- （2）每周
SELECT
	DATE_FORMAT( date( FILING_DATE ), '%Y' ) AS year1,
	WEEK ( FILING_DATE ) AS week1,
	t4.org_name,
	count( t1.ROW_ID ) AS total 
FROM
	qy_clue_account t1
	JOIN lnk_accnt t3 ON t1.belong_org = t3.ROW_ID
	JOIN lnk_org_ext t4 ON t3.PARENT_ORG_ID = t4.ROW_ID 
WHERE
	FILING_DATE IS NOT NULL 
	AND BELONG_ORG IS NOT NULL 
	AND down_pay_time IS NOT NULL 
	AND opty_state = 'Normal' 
	AND t1.consumer_type IN ( 'DepositCustomer', 'ContractualCustomer', 'FinishedProductClue' ) 
GROUP BY
	DATE_FORMAT( date( FILING_DATE ), '%Y' ) ,
	WEEK ( FILING_DATE ),
	t4.org_name

-- （3）每月
SELECT
	DATE_FORMAT( date( FILING_DATE ), '%Y-%m' ) AS month1,
	t4.org_name,
	count( t1.ROW_ID ) AS total 
FROM
	qy_clue_account t1
	JOIN lnk_accnt t3 ON t1.belong_org = t3.ROW_ID
	JOIN lnk_org_ext t4 ON t3.PARENT_ORG_ID = t4.ROW_ID 
WHERE
	FILING_DATE IS NOT NULL 
	AND BELONG_ORG IS NOT NULL 
	AND down_pay_time IS NOT NULL 
	AND opty_state = 'Normal' 
	AND t1.consumer_type IN ( 'DepositCustomer', 'ContractualCustomer', 'FinishedProductClue' ) 
GROUP BY
DATE_FORMAT( date( FILING_DATE ), '%Y-%m' ),
	t4.org_name

-- 3.办事处+时间+客户经理
-- （1）每日
SELECT
	DATE_FORMAT( date( FILING_DATE ), '%Y-%m-%d' ) AS day1,
	t4.org_name as office_name,
	t5.fst_name as manager_name,
	count( t1.ROW_ID ) AS total 
FROM
	qy_clue_account t1
	JOIN lnk_accnt t3 ON t1.belong_org = t3.ROW_ID
	JOIN lnk_org_ext t4 ON t3.PARENT_ORG_ID = t4.ROW_ID
 join lnk_emp_info t5 on upper(t3.CP_MANAGER_ID) = t5.username	
WHERE
	FILING_DATE IS NOT NULL 
	AND BELONG_ORG IS NOT NULL 
	AND down_pay_time IS NOT NULL 
	AND opty_state = 'Normal' 
	AND t1.consumer_type IN ( 'DepositCustomer', 'ContractualCustomer', 'FinishedProductClue' ) 
GROUP BY
	DATE_FORMAT( date( FILING_DATE ), '%Y-%m-%d' ),
	t4.org_name,
	t5.fst_name

-- （2）每周
SELECT
	DATE_FORMAT( date( FILING_DATE ), '%Y' ) AS year1,
	WEEK ( FILING_DATE ) AS week1,
	t4.org_name AS office_name,
	t5.fst_name AS manager_name,
	count( t1.ROW_ID ) AS total 
FROM
	qy_clue_account t1
	JOIN lnk_accnt t3 ON t1.belong_org = t3.ROW_ID
	JOIN lnk_org_ext t4 ON t3.PARENT_ORG_ID = t4.ROW_ID
	JOIN lnk_emp_info t5 ON upper( t3.CP_MANAGER_ID ) = t5.username 
WHERE
	FILING_DATE IS NOT NULL 
	AND BELONG_ORG IS NOT NULL 
	AND down_pay_time IS NOT NULL 
	AND opty_state = 'Normal' 
	AND t1.consumer_type IN ( 'DepositCustomer', 'ContractualCustomer', 'FinishedProductClue' ) 
GROUP BY
	DATE_FORMAT( date( FILING_DATE ), '%Y' ),
	WEEK ( FILING_DATE ),
	t4.org_name,
	t5.fst_name
-- （3）每月
SELECT
	DATE_FORMAT( date( FILING_DATE ), '%Y-%m' ) AS month1,
	t4.org_name as office_name,
	t5.fst_name as manager_name,
	count( t1.ROW_ID ) AS total 
FROM
	qy_clue_account t1
	JOIN lnk_accnt t3 ON t1.belong_org = t3.ROW_ID
	JOIN lnk_org_ext t4 ON t3.PARENT_ORG_ID = t4.ROW_ID
 join lnk_emp_info t5 on upper(t3.CP_MANAGER_ID) = t5.username	
WHERE
	FILING_DATE IS NOT NULL 
	AND BELONG_ORG IS NOT NULL 
	AND down_pay_time IS NOT NULL 
	AND opty_state = 'Normal' 
	AND t1.consumer_type IN ( 'DepositCustomer', 'ContractualCustomer', 'FinishedProductClue' ) 
GROUP BY
	DATE_FORMAT( date( FILING_DATE ), '%Y-%m' ) ,
	t4.org_name,
	t5.fst_name
	
-- 4.来源渠道（数据来源字段)+时间
-- （1）每日
-- 每种来源每天的线索客户数
SELECT
	t1.belong_dept,
	t2.NAME,
	DATE_FORMAT( date( FILING_DATE ), '%Y-%m-%d' ) AS day1,
	count( t1.ROW_ID ) 
FROM
	qy_clue_account t1
	JOIN lnk_list_of_val t2 ON t1.BELONG_DEPT = t2.val 
WHERE
	FILING_DATE IS NOT NULL 
	AND BELONG_ORG IS NOT NULL 
	AND down_pay_time IS NOT NULL 
	AND opty_state = 'Normal' 
	AND consumer_type IN ( 'DepositCustomer', 'ContractualCustomer', 'FinishedProductClue' ) 
GROUP BY
	t1.belong_dept,
	DATE_FORMAT( date( FILING_DATE ), '%Y-%m-%d' )
	
-- （2）每周
-- 每种来源每周的线索客户数
SELECT
	t1.belong_dept,
	t2.NAME,
	DATE_FORMAT( date( FILING_DATE ), '%Y' ) AS year1,
	WEEK ( FILING_DATE ) AS week1,
	count( t1.ROW_ID ) 
FROM
	qy_clue_account t1
	JOIN lnk_list_of_val t2 ON t1.BELONG_DEPT = t2.val 
WHERE
	FILING_DATE IS NOT NULL 
	AND BELONG_ORG IS NOT NULL 
	AND down_pay_time IS NOT NULL 
	AND opty_state = 'Normal' 
	AND consumer_type IN ( 'DepositCustomer', 'ContractualCustomer', 'FinishedProductClue' ) 
GROUP BY
	t1.belong_dept,
	DATE_FORMAT( date( FILING_DATE ), '%Y' ) ,
	WEEK ( FILING_DATE )
-- （3）每月
-- 每种来源每月的线索客户数
SELECT
	t1.belong_dept,
	t2.NAME,
	DATE_FORMAT( date( FILING_DATE ), '%Y-%m' ) AS month1,
	count( t1.ROW_ID ) 
FROM
	qy_clue_account t1
	JOIN lnk_list_of_val t2 ON t1.BELONG_DEPT = t2.val 
WHERE
	FILING_DATE IS NOT NULL 
	AND BELONG_ORG IS NOT NULL 
	AND down_pay_time IS NOT NULL 
	AND opty_state = 'Normal' 
	AND consumer_type IN ( 'DepositCustomer', 'ContractualCustomer', 'FinishedProductClue' ) 
GROUP BY
	t1.belong_dept,
	DATE_FORMAT( date( FILING_DATE ), '%Y-%m' )
-- 5.渠道+时间+办事处
-- （1）每种渠道每日办事处的线索客户数

SELECT
	t1.belong_dept,
	t2.NAME,
	DATE_FORMAT( date( FILING_DATE ), '%Y-%m-%d' ) AS day1,
	t4.org_name,
	count( t1.ROW_ID ) AS total 
FROM
	qy_clue_account t1
	JOIN lnk_list_of_val t2 ON t1.BELONG_DEPT = t2.val
	JOIN lnk_accnt t3 ON t1.belong_org = t3.ROW_ID
	JOIN lnk_org_ext t4 ON t3.PARENT_ORG_ID = t4.ROW_ID 
WHERE
	FILING_DATE IS NOT NULL 
	AND BELONG_ORG IS NOT NULL 
	AND down_pay_time IS NOT NULL 
	AND opty_state = 'Normal' 
	AND t1.consumer_type IN ( 'DepositCustomer', 'ContractualCustomer', 'FinishedProductClue' ) 
GROUP BY
	t1.belong_dept,
	DATE_FORMAT( date( FILING_DATE ), '%Y-%m-%d' ),
	t4.org_name
	
-- （2）每周

SELECT
	t1.belong_dept,
	t2.NAME,
	DATE_FORMAT( date( FILING_DATE ), '%Y' ) AS year1,
	WEEK ( FILING_DATE ) AS week1,
	t4.org_name,
	count( t1.ROW_ID ) AS total 
FROM
	qy_clue_account t1
	JOIN lnk_list_of_val t2 ON t1.BELONG_DEPT = t2.val
	JOIN lnk_accnt t3 ON t1.belong_org = t3.ROW_ID
	JOIN lnk_org_ext t4 ON t3.PARENT_ORG_ID = t4.ROW_ID 
WHERE
	FILING_DATE IS NOT NULL 
	AND BELONG_ORG IS NOT NULL 
	AND down_pay_time IS NOT NULL 
	AND opty_state = 'Normal' 
	AND t1.consumer_type IN ( 'DepositCustomer', 'ContractualCustomer', 'FinishedProductClue' ) 
GROUP BY
	t1.belong_dept,
	DATE_FORMAT( date( FILING_DATE ), '%Y' ) ,
	WEEK ( FILING_DATE ),
	t4.org_name

-- （3）每月
SELECT
	t1.belong_dept,
	t2.NAME,
	DATE_FORMAT( date( FILING_DATE ), '%Y-%m' ) AS month1,
	t4.org_name,
	count( t1.ROW_ID ) AS total 
FROM
	qy_clue_account t1
	JOIN lnk_list_of_val t2 ON t1.BELONG_DEPT = t2.val
	JOIN lnk_accnt t3 ON t1.belong_org = t3.ROW_ID
	JOIN lnk_org_ext t4 ON t3.PARENT_ORG_ID = t4.ROW_ID 
WHERE
	FILING_DATE IS NOT NULL 
	AND BELONG_ORG IS NOT NULL 
	AND down_pay_time IS NOT NULL 
	AND opty_state = 'Normal' 
	AND t1.consumer_type IN ( 'DepositCustomer', 'ContractualCustomer', 'FinishedProductClue' ) 
GROUP BY
	t1.belong_dept,
DATE_FORMAT( date( FILING_DATE ), '%Y-%m' ),
	t4.org_name

-- 6、渠道+时间+办事处+客户经理
-- （1）每日

SELECT
	t1.belong_dept,
	t2.NAME,
	DATE_FORMAT( date( FILING_DATE ), '%Y-%m-%d' ) AS day1,
	t4.org_name as office_name,
	t5.fst_name as manager_name,
	count( t1.ROW_ID ) AS total 
FROM
	qy_clue_account t1
	JOIN lnk_list_of_val t2 ON t1.BELONG_DEPT = t2.val
	JOIN lnk_accnt t3 ON t1.belong_org = t3.ROW_ID
	JOIN lnk_org_ext t4 ON t3.PARENT_ORG_ID = t4.ROW_ID
 join lnk_emp_info t5 on upper(t3.CP_MANAGER_ID) = t5.username	
WHERE
	FILING_DATE IS NOT NULL 
	AND BELONG_ORG IS NOT NULL 
	AND down_pay_time IS NOT NULL 
	AND opty_state = 'Normal' 
	AND t1.consumer_type IN ( 'DepositCustomer', 'ContractualCustomer', 'FinishedProductClue' ) 
GROUP BY
	t1.belong_dept,
	DATE_FORMAT( date( FILING_DATE ), '%Y-%m-%d' ),
	t4.org_name,
	t5.fst_name
	
-- （2）每周
SELECT
	t1.belong_dept,
	t2.NAME,
	DATE_FORMAT( date( FILING_DATE ), '%Y' ) AS year1,
	WEEK ( FILING_DATE ) AS week1,
	t4.org_name AS office_name,
	t5.fst_name AS manager_name,
	count( t1.ROW_ID ) AS total 
FROM
	qy_clue_account t1
	JOIN lnk_list_of_val t2 ON t1.BELONG_DEPT = t2.val
	JOIN lnk_accnt t3 ON t1.belong_org = t3.ROW_ID
	JOIN lnk_org_ext t4 ON t3.PARENT_ORG_ID = t4.ROW_ID
	JOIN lnk_emp_info t5 ON upper( t3.CP_MANAGER_ID ) = t5.username 
WHERE
	FILING_DATE IS NOT NULL 
	AND BELONG_ORG IS NOT NULL 
	AND down_pay_time IS NOT NULL 
	AND opty_state = 'Normal' 
	AND t1.consumer_type IN ( 'DepositCustomer', 'ContractualCustomer', 'FinishedProductClue' ) 
GROUP BY
	t1.belong_dept,
	DATE_FORMAT( date( FILING_DATE ), '%Y' ),
	WEEK ( FILING_DATE ),
	t4.org_name,
	t5.fst_name
	
	
-- （3）每月
SELECT
	t1.belong_dept,
	t2.NAME,
	DATE_FORMAT( date( FILING_DATE ), '%Y-%m' ) AS month1,
	t4.org_name as office_name,
	t5.fst_name as manager_name,
	count( t1.ROW_ID ) AS total 
FROM
	qy_clue_account t1
	JOIN lnk_list_of_val t2 ON t1.BELONG_DEPT = t2.val
	JOIN lnk_accnt t3 ON t1.belong_org = t3.ROW_ID
	JOIN lnk_org_ext t4 ON t3.PARENT_ORG_ID = t4.ROW_ID
 join lnk_emp_info t5 on upper(t3.CP_MANAGER_ID) = t5.username	
WHERE
	FILING_DATE IS NOT NULL 
	AND BELONG_ORG IS NOT NULL 
	AND down_pay_time IS NOT NULL 
	AND opty_state = 'Normal' 
	AND t1.consumer_type IN ( 'DepositCustomer', 'ContractualCustomer', 'FinishedProductClue' ) 
GROUP BY
	t1.belong_dept,
	DATE_FORMAT( date( FILING_DATE ), '%Y-%m' ) ,
	t4.org_name,
	t5.fst_name

```

### 二、订单客户数占比

```sql
-- 订单客户占比
-- 计算逻辑：订单客户÷可分配商家数(时间相同）

-- 求和

select count(row_id) FROM qy_clue_account WHERE FILING_DATE is not null and down_pay_time is not null and opty_state ='Normal' and 
consumer_type in('DepositCustomer','ContractualCustomer','FinishedProductClue');


-- 1.时间
-- 
-- （1）每日
select 
day1,
day2,
number,
total,
round(number/total,4) as ratio
from 
(SELECT
	DATE_FORMAT( date( FILING_DATE ), '%Y-%m-%d' ) AS day1,
	count( row_id ) AS number
FROM
	qy_clue_account 
WHERE
	FILING_DATE IS NOT NULL 
	AND BELONG_ORG IS NOT NULL 
	AND down_pay_time IS NOT NULL 
	AND opty_state = 'Normal' 
	AND consumer_type IN ( 'DepositCustomer', 'ContractualCustomer', 'FinishedProductClue' ) 
GROUP BY
	day1)t1
	left join 
(
	SELECT
	DATE_FORMAT( date( FILING_DATE ), '%Y-%m-%d' ) AS day2,
	count( row_id ) AS total 
FROM
	qy_clue_account 
WHERE
	FILING_DATE IS NOT NULL 
	AND BELONG_ORG IS NOT NULL
	group by DATE_FORMAT( date( FILING_DATE ), '%Y-%m-%d' ))t2 on t1.day1=t2.day2;

-- （2）每周
select 
year1,
week1,
number,
total,
round(number/total,4) as ratio
from 
(SELECT
	DATE_FORMAT( date( FILING_DATE ), '%Y' ) AS year1,
	WEEK ( FILING_DATE ) AS week1,
	count( row_id ) AS number
FROM
	qy_clue_account 
WHERE
	FILING_DATE IS NOT NULL 
	AND BELONG_ORG IS NOT NULL 
	AND down_pay_time IS NOT NULL 
	AND opty_state = 'Normal' 
	AND consumer_type IN ( 'DepositCustomer', 'ContractualCustomer', 'FinishedProductClue' ) 
GROUP BY
	DATE_FORMAT( date( FILING_DATE ), '%Y' ),
	WEEK ( FILING_DATE ))t1
join 
(
	SELECT
	DATE_FORMAT( date( FILING_DATE ), '%Y' ) AS year2,
	WEEK ( FILING_DATE ) AS week2,
	count( row_id ) AS total 
FROM
	qy_clue_account 
WHERE
	FILING_DATE IS NOT NULL 
	AND BELONG_ORG IS NOT NULL 
GROUP BY
	DATE_FORMAT( date( FILING_DATE ), '%Y' ),
	WEEK ( FILING_DATE ))t2 on t1.year1=t2.year2 and t1.week1=t2.week2;
-- （3）每月
SELECT
	month1,
	number,
	total,
	round( number / total, 4 ) AS ratio 
FROM
	(
SELECT
	DATE_FORMAT( date( FILING_DATE ), '%Y-%m' ) AS month1,
	count( row_id ) AS number
FROM
	qy_clue_account 
WHERE
	FILING_DATE IS NOT NULL 
	AND BELONG_ORG IS NOT NULL 
	AND down_pay_time IS NOT NULL 
	AND opty_state = 'Normal' 
	AND consumer_type IN ( 'DepositCustomer', 'ContractualCustomer', 'FinishedProductClue' ) 
GROUP BY
	DATE_FORMAT( date( FILING_DATE ), '%Y-%m' ) 
	) t1
	JOIN (
SELECT
	DATE_FORMAT( date( FILING_DATE ), '%Y-%m' ) AS month2,
	count( row_id ) AS total 
FROM
	qy_clue_account 
WHERE
	FILING_DATE IS NOT NULL 
	AND BELONG_ORG IS NOT NULL 
GROUP BY
	month2) t2 ON t1.month1 = t2.month2 

-- （4）每年
SELECT
	year1,
	number,
	total,
	round( number / total, 4 ) AS ratio 
FROM
	(
SELECT
	DATE_FORMAT( date( FILING_DATE ), '%Y' ) AS year1,
	count( row_id ) AS number 
FROM
	qy_clue_account 
WHERE
	FILING_DATE IS NOT NULL 
	AND BELONG_ORG IS NOT NULL 
	AND down_pay_time IS NOT NULL 
	AND opty_state = 'Normal' 
	AND consumer_type IN ( 'DepositCustomer', 'ContractualCustomer', 'FinishedProductClue' ) 
GROUP BY
	year1 
	) t1
	JOIN (
SELECT
	DATE_FORMAT( date( FILING_DATE ), '%Y' ) AS year2,
	count( row_id ) AS total 
FROM
	qy_clue_account 
WHERE
	FILING_DATE IS NOT NULL 
	AND BELONG_ORG IS NOT NULL 
GROUP BY
	year2
	) t2 ON t1.year1 = t2.year2;
	
-- 2.办事处+时间字段（天）
-- 每天每个办事处的线索客户数占比

	


-- 3、办事处+客户经理+时间字段（天）
-- 
-- 每个办事处下的客户经理

-- 
-- 4.渠道+办事处+时间字段（天）
-- 
-- 
-- 
-- 5.渠道+办事处+客户经理+时间字段（天）（前端取数）



```



###  三、合同客户数

```sql
-- 合同客户数
-- 汇总
-- 写法一
SELECT
	count(
	DISTINCT (
CASE
	WHEN t3.after_stage = 'RepeatRuler' 
	AND NOT EXISTS (
SELECT
	1 
FROM
	qy_optystage_record temp1
	JOIN lnk_opty temp2 ON temp1.OPTY_ID = temp2.row_id 
WHERE
	temp2.HEAD_ID = t0.ROW_ID 
	AND temp1.CREATED < t1.CREATED 
	AND temp1.after_stage = 'RepeatRuler' 
	) THEN
	t0.ROW_ID 
END 
	) 
	) total 
FROM
	qy_clue_account t0
	JOIN lnk_opty t1 ON t1.HEAD_ID = t0.ROW_ID
	JOIN qy_optystage_record t3 ON t1.row_id = t3.OPTY_ID
-- 写法二
SELECT
	count(
	DISTINCT (
CASE
	WHEN t3.after_stage = 'RepeatRuler' 
	AND t0.ROW_ID NOT IN (
SELECT DISTINCT
	t0.ROW_ID 
FROM
	qy_optystage_record temp1
	JOIN lnk_opty temp2 ON temp1.OPTY_ID = temp2.row_id 
WHERE
	temp2.HEAD_ID = t0.ROW_ID 
	AND temp1.CREATED < t3.CREATED 
	AND temp1.after_stage = 'RepeatRuler' 
	) THEN
	t0.ROW_ID 
END 
	) 
	) total 
FROM
	qy_clue_account t0
	JOIN lnk_opty t1 ON t1.HEAD_ID = t0.ROW_ID
	JOIN qy_optystage_record t3 ON t1.row_id = t3.OPTY_ID
	
-- 1.时间
-- 
-- （1）每日
-- 时间取跟进记录里的合同时间
SELECT
	DATE_FORMAT( date( t3.CREATED ), '%Y-%m-%d' ) AS day1,
	count(
	DISTINCT (
CASE
	WHEN t3.after_stage = 'RepeatRuler' 
	AND t0.ROW_ID NOT IN (
SELECT DISTINCT
	t0.ROW_ID 
FROM
	qy_optystage_record temp1
	JOIN lnk_opty temp2 ON temp1.OPTY_ID = temp2.row_id 
WHERE
	temp2.HEAD_ID = t0.ROW_ID 
	AND temp1.CREATED < t3.CREATED 
	AND temp1.after_stage = 'RepeatRuler' 
	) THEN
	t0.ROW_ID 
END 
	) 
	) total 
FROM
	qy_clue_account t0
	JOIN lnk_opty t1 ON t1.HEAD_ID = t0.ROW_ID
	JOIN qy_optystage_record t3 ON t1.row_id = t3.OPTY_ID 
GROUP BY
	DATE_FORMAT( date( t3.CREATED ), '%Y-%m-%d' )
	
-- （2）每周

SELECT
	DATE_FORMAT( date( t3.CREATED ), '%Y' ) AS year1,
	WEEK (t3.CREATED ) AS week1,
	count(
	DISTINCT (
CASE
	WHEN t3.after_stage = 'RepeatRuler' 
	AND t0.ROW_ID NOT IN (
SELECT DISTINCT
	t0.ROW_ID 
FROM
	qy_optystage_record temp1
	JOIN lnk_opty temp2 ON temp1.OPTY_ID = temp2.row_id 
WHERE
	temp2.HEAD_ID = t0.ROW_ID 
	AND temp1.CREATED < t3.CREATED 
	AND temp1.after_stage = 'RepeatRuler' 
	) THEN
	t0.ROW_ID 
END 
	) 
	) total 
FROM
	qy_clue_account t0
	JOIN lnk_opty t1 ON t1.HEAD_ID = t0.ROW_ID
	JOIN qy_optystage_record t3 ON t1.row_id = t3.OPTY_ID 
GROUP BY
	DATE_FORMAT( date( t3.CREATED ), '%Y' ) ,
	WEEK ( t3.CREATED )

-- （3）每月
SELECT
		DATE_FORMAT( date( t3.CREATED ), '%Y-%m' ) AS month1,
	count(
	DISTINCT (
CASE
	WHEN t3.after_stage = 'RepeatRuler' 
	AND t0.ROW_ID NOT IN (
SELECT DISTINCT
	t0.ROW_ID 
FROM
	qy_optystage_record temp1
	JOIN lnk_opty temp2 ON temp1.OPTY_ID = temp2.row_id 
WHERE
	temp2.HEAD_ID = t0.ROW_ID 
	AND temp1.CREATED < t3.CREATED 
	AND temp1.after_stage = 'RepeatRuler' 
	) THEN
	t0.ROW_ID 
END 
	) 
	) total 
FROM
	qy_clue_account t0
	JOIN lnk_opty t1 ON t1.HEAD_ID = t0.ROW_ID
	JOIN qy_optystage_record t3 ON t1.row_id = t3.OPTY_ID 
GROUP BY
		DATE_FORMAT( date( t3.CREATED ), '%Y-%m' ) 

-- （4）每年
SELECT
		DATE_FORMAT( date( t3.CREATED ), '%Y' ) AS year1,
	count(
	DISTINCT (
CASE
	WHEN t3.after_stage = 'RepeatRuler' 
	AND t0.ROW_ID NOT IN (
SELECT DISTINCT
	t0.ROW_ID 
FROM
	qy_optystage_record temp1
	JOIN lnk_opty temp2 ON temp1.OPTY_ID = temp2.row_id 
WHERE
	temp2.HEAD_ID = t0.ROW_ID 
	AND temp1.CREATED < t3.CREATED 
	AND temp1.after_stage = 'RepeatRuler' 
	) THEN
	t0.ROW_ID 
END 
	) 
	) total 
FROM
	qy_clue_account t0
	JOIN lnk_opty t1 ON t1.HEAD_ID = t0.ROW_ID
	JOIN qy_optystage_record t3 ON t1.row_id = t3.OPTY_ID 
GROUP BY
		DATE_FORMAT( date( t3.CREATED ), '%Y' )

-- 2.办事处+时间
-- （1）每日
SELECT
DATE_FORMAT( date( t3.CREATED ), '%Y-%m-%d' ) AS day1,
t5.org_name,
	count(
	DISTINCT (
CASE
	WHEN t3.after_stage = 'RepeatRuler' 
	AND t0.ROW_ID NOT IN (
SELECT DISTINCT
	t0.ROW_ID 
FROM
	qy_optystage_record temp1
	JOIN lnk_opty temp2 ON temp1.OPTY_ID = temp2.row_id 
WHERE
	temp2.HEAD_ID = t0.ROW_ID 
	AND temp1.CREATED < t3.CREATED 
	AND temp1.after_stage = 'RepeatRuler' 
	) THEN
	t0.ROW_ID 
END 
	) 
	) total 
FROM
	qy_clue_account t0
	JOIN lnk_opty t1 ON t1.HEAD_ID = t0.ROW_ID
	JOIN lnk_accnt t4 ON t0.belong_org = t4.ROW_ID
	JOIN lnk_org_ext t5 ON t4.PARENT_ORG_ID = t5.ROW_ID 
	JOIN qy_optystage_record t3 ON t1.row_id = t3.OPTY_ID
	group by
DATE_FORMAT( date( t3.CREATED ), '%Y-%m-%d' ),
t5.org_name

-- （2）每周
SELECT
DATE_FORMAT( date(  t3.CREATED ), '%Y' ) AS year1,
	WEEK (  t3.CREATED ) AS week1,
t5.org_name,
	count(
	DISTINCT (
CASE
	WHEN t3.after_stage = 'RepeatRuler' 
	AND t0.ROW_ID NOT IN (
SELECT DISTINCT
	t0.ROW_ID 
FROM
	qy_optystage_record temp1
	JOIN lnk_opty temp2 ON temp1.OPTY_ID = temp2.row_id 
WHERE
	temp2.HEAD_ID = t0.ROW_ID 
	AND temp1.CREATED < t3.CREATED 
	AND temp1.after_stage = 'RepeatRuler' 
	) THEN
	t0.ROW_ID 
END 
	) 
	) total 
FROM
	qy_clue_account t0
	JOIN lnk_opty t1 ON t1.HEAD_ID = t0.ROW_ID
	JOIN lnk_accnt t4 ON t0.belong_org = t4.ROW_ID
	JOIN lnk_org_ext t5 ON t4.PARENT_ORG_ID = t5.ROW_ID 
	JOIN qy_optystage_record t3 ON t1.row_id = t3.OPTY_ID
	group by
DATE_FORMAT( date(  t3.CREATED ), '%Y' ) ,
	WEEK (  t3.CREATED ) 
t5.org_name
-- （3）每月
SELECT
DATE_FORMAT( date( t3.CREATED ), '%Y-%m' ) AS month1,
t5.org_name,
	count(
	DISTINCT (
CASE
	WHEN t3.after_stage = 'RepeatRuler' 
	AND t0.ROW_ID NOT IN (
SELECT DISTINCT
	t0.ROW_ID 
FROM
	qy_optystage_record temp1
	JOIN lnk_opty temp2 ON temp1.OPTY_ID = temp2.row_id 
WHERE
	temp2.HEAD_ID = t0.ROW_ID 
	AND temp1.CREATED < t3.CREATED 
	AND temp1.after_stage = 'RepeatRuler' 
	) THEN
	t0.ROW_ID 
END 
	) 
	) total 
FROM
	qy_clue_account t0
	JOIN lnk_opty t1 ON t1.HEAD_ID = t0.ROW_ID
	JOIN lnk_accnt t4 ON t0.belong_org = t4.ROW_ID
	JOIN lnk_org_ext t5 ON t4.PARENT_ORG_ID = t5.ROW_ID 
	JOIN qy_optystage_record t3 ON t1.row_id = t3.OPTY_ID
	group by
DATE_FORMAT( date( t3.CREATED ), '%Y-%m' ) ,
t5.org_name

-- 3.时间+办事处+客户经理
-- (1)每日
SELECT
	DATE_FORMAT( date( t3.CREATED ), '%Y-%m-%d' ) AS day1,
	t5.org_name,
	t6.fst_name AS manager_name,
	count(
	DISTINCT (
CASE
	WHEN t3.after_stage = 'RepeatRuler' 
	AND t0.ROW_ID NOT IN (
SELECT DISTINCT
	t0.ROW_ID 
FROM
	qy_optystage_record temp1
	JOIN lnk_opty temp2 ON temp1.OPTY_ID = temp2.row_id 
WHERE
	temp2.HEAD_ID = t0.ROW_ID 
	AND temp1.CREATED < t3.CREATED 
	AND temp1.after_stage = 'RepeatRuler' 
	) THEN
	t0.ROW_ID 
END 
	) 
	) total 
FROM
	qy_clue_account t0
	JOIN lnk_opty t1 ON t1.HEAD_ID = t0.ROW_ID
	JOIN lnk_accnt t4 ON t0.belong_org = t4.ROW_ID
	JOIN lnk_org_ext t5 ON t4.PARENT_ORG_ID = t5.ROW_ID
	JOIN lnk_emp_info t6 ON upper( t4.CP_MANAGER_ID ) = t6.username
	JOIN qy_optystage_record t3 ON t1.row_id = t3.OPTY_ID 
GROUP BY
	DATE_FORMAT( date( t3.CREATED ), '%Y-%m-%d' ),
	t5.org_name,
	t6.fst_name
-- (2)每周
SELECT
DATE_FORMAT( date(  t3.CREATED ), '%Y' ) AS year1,
	WEEK (  t3.CREATED ) AS week1,
	t5.org_name,
	t6.fst_name AS manager_name,
	count(
	DISTINCT (
CASE
	WHEN t3.after_stage = 'RepeatRuler' 
	AND t0.ROW_ID NOT IN (
SELECT DISTINCT
	t0.ROW_ID 
FROM
	qy_optystage_record temp1
	JOIN lnk_opty temp2 ON temp1.OPTY_ID = temp2.row_id 
WHERE
	temp2.HEAD_ID = t0.ROW_ID 
	AND temp1.CREATED < t3.CREATED 
	AND temp1.after_stage = 'RepeatRuler' 
	) THEN
	t0.ROW_ID 
END 
	) 
	) total 
FROM
	qy_clue_account t0
	JOIN lnk_opty t1 ON t1.HEAD_ID = t0.ROW_ID
	JOIN lnk_accnt t4 ON t0.belong_org = t4.ROW_ID
	JOIN lnk_org_ext t5 ON t4.PARENT_ORG_ID = t5.ROW_ID
	JOIN lnk_emp_info t6 ON upper( t4.CP_MANAGER_ID ) = t6.username
	JOIN qy_optystage_record t3 ON t1.row_id = t3.OPTY_ID 
GROUP BY
DATE_FORMAT( date(  t3.CREATED ), '%Y' ) ,
	WEEK (  t3.CREATED ) ,
	t5.org_name,
	t6.fst_name

-- （3）每月
SELECT
DATE_FORMAT( date( t3.CREATED ), '%Y-%m' ) AS month1,
	t5.org_name,
	t6.fst_name AS manager_name,
	count(
	DISTINCT (
CASE
	WHEN t3.after_stage = 'RepeatRuler' 
	AND t0.ROW_ID NOT IN (
SELECT DISTINCT
	t0.ROW_ID 
FROM
	qy_optystage_record temp1
	JOIN lnk_opty temp2 ON temp1.OPTY_ID = temp2.row_id 
WHERE
	temp2.HEAD_ID = t0.ROW_ID 
	AND temp1.CREATED < t3.CREATED 
	AND temp1.after_stage = 'RepeatRuler' 
	) THEN
	t0.ROW_ID 
END 
	) 
	) total 
FROM
	qy_clue_account t0
	JOIN lnk_opty t1 ON t1.HEAD_ID = t0.ROW_ID
	JOIN lnk_accnt t4 ON t0.belong_org = t4.ROW_ID
	JOIN lnk_org_ext t5 ON t4.PARENT_ORG_ID = t5.ROW_ID
	JOIN lnk_emp_info t6 ON upper( t4.CP_MANAGER_ID ) = t6.username
	JOIN qy_optystage_record t3 ON t1.row_id = t3.OPTY_ID 
GROUP BY
DATE_FORMAT( date( t3.CREATED ), '%Y-%m' ) ,
	t5.org_name,
	t6.fst_name
	
-- 4.渠道（数据来源字段)+时间
-- （1）每日
SELECT
	DATE_FORMAT( date( t3.CREATED ), '%Y-%m-%d' ) AS day1,
	count(
	DISTINCT (
CASE
	
	WHEN t3.after_stage = 'RepeatRuler' 
	AND t0.ROW_ID NOT IN (
SELECT DISTINCT
	t0.ROW_ID 
FROM
	qy_optystage_record temp1
	JOIN lnk_opty temp2 ON temp1.OPTY_ID = temp2.row_id 
WHERE
	temp2.HEAD_ID = t0.ROW_ID 
	AND temp1.CREATED < t3.CREATED 
	AND temp1.after_stage = 'RepeatRuler' 
	) THEN
	t0.ROW_ID 
END 
	) 
	) total 
FROM
	qy_clue_account t0
	JOIN lnk_opty t1 ON t1.HEAD_ID = t0.ROW_ID
	JOIN qy_optystage_record t3 ON t1.row_id = t3.OPTY_ID 
GROUP BY
	DATE_FORMAT( date( t3.CREATED ), '%Y-%m-%d' )
	
-- （2）每周
SELECT
	DATE_FORMAT( date( t3.CREATED ), '%Y' ) AS year1,
	WEEK (t3.CREATED ) AS week1,
	count(
	DISTINCT (
CASE
	
	WHEN t3.after_stage = 'RepeatRuler' 
	AND t0.ROW_ID NOT IN (
SELECT DISTINCT
	t0.ROW_ID 
FROM
	qy_optystage_record temp1
	JOIN lnk_opty temp2 ON temp1.OPTY_ID = temp2.row_id 
WHERE
	temp2.HEAD_ID = t0.ROW_ID 
	AND temp1.CREATED < t3.CREATED 
	AND temp1.after_stage = 'RepeatRuler' 
	) THEN
	t0.ROW_ID 
END 
	) 
	) total 
FROM
	qy_clue_account t0
	JOIN lnk_opty t1 ON t1.HEAD_ID = t0.ROW_ID
	JOIN qy_optystage_record t3 ON t1.row_id = t3.OPTY_ID 
GROUP BY
	DATE_FORMAT( date( t3.CREATED ), '%Y' ) ,
	WEEK (t3.CREATED ) 
	
-- （3）每月
SELECT
DATE_FORMAT( date( t3.CREATED ), '%Y-%m' ) AS month1,
	WEEK (t3.CREATED ) AS week1,
	count(
	DISTINCT (
CASE
	
	WHEN t3.after_stage = 'RepeatRuler' 
	AND t0.ROW_ID NOT IN (
SELECT DISTINCT
	t0.ROW_ID 
FROM
	qy_optystage_record temp1
	JOIN lnk_opty temp2 ON temp1.OPTY_ID = temp2.row_id 
WHERE
	temp2.HEAD_ID = t0.ROW_ID 
	AND temp1.CREATED < t3.CREATED 
	AND temp1.after_stage = 'RepeatRuler' 
	) THEN
	t0.ROW_ID 
END 
	) 
	) total 
FROM
	qy_clue_account t0
	JOIN lnk_opty t1 ON t1.HEAD_ID = t0.ROW_ID
	JOIN qy_optystage_record t3 ON t1.row_id = t3.OPTY_ID 
GROUP BY
DATE_FORMAT( date( t3.CREATED ), '%Y-%m' ) 


-- 5.渠道+时间+办事处
-- 
-- （1）每日
SELECT
	t2.NAME,
	DATE_FORMAT( date( t3.CREATED ), '%Y-%m-%d' ) AS day1,
	t5.org_name,
	count(
	DISTINCT (
CASE
	WHEN t3.after_stage = 'RepeatRuler' 
	AND t0.ROW_ID NOT IN (
SELECT DISTINCT
	t0.ROW_ID 
FROM
	qy_optystage_record temp1
	JOIN lnk_opty temp2 ON temp1.OPTY_ID = temp2.row_id 
WHERE
	temp2.HEAD_ID = t0.ROW_ID 
	AND temp1.CREATED < t3.CREATED 
	AND temp1.after_stage = 'RepeatRuler' 
	) THEN
	t0.ROW_ID 
END 
	) 
	) total 
FROM
	qy_clue_account t0
	JOIN lnk_opty t1 ON t1.HEAD_ID = t0.ROW_ID
	JOIN lnk_list_of_val t2 ON t1.BELONG_DEPT = t2.val
	JOIN lnk_accnt t4 ON t0.belong_org = t4.ROW_ID
	JOIN lnk_org_ext t5 ON t4.PARENT_ORG_ID = t5.ROW_ID
	JOIN qy_optystage_record t3 ON t1.row_id = t3.OPTY_ID 
GROUP BY
	DATE_FORMAT( date( t3.CREATED ), '%Y-%m-%d' ),
	t5.org_name,
	t2.NAME

-- （2）每周
SELECT
	t2.NAME,
		DATE_FORMAT( date( t3.CREATED ), '%Y' ) AS year1,
	WEEK ( t3.CREATED ) AS week1,
	t5.org_name,
	count(
	DISTINCT (
CASE
	WHEN t3.after_stage = 'RepeatRuler' 
	AND t0.ROW_ID NOT IN (
SELECT DISTINCT
	t0.ROW_ID 
FROM
	qy_optystage_record temp1
	JOIN lnk_opty temp2 ON temp1.OPTY_ID = temp2.row_id 
WHERE
	temp2.HEAD_ID = t0.ROW_ID 
	AND temp1.CREATED < t3.CREATED 
	AND temp1.after_stage = 'RepeatRuler' 
	) THEN
	t0.ROW_ID 
END 
	) 
	) total 
FROM
	qy_clue_account t0
	JOIN lnk_opty t1 ON t1.HEAD_ID = t0.ROW_ID
	JOIN lnk_list_of_val t2 ON t1.BELONG_DEPT = t2.val
	JOIN lnk_accnt t4 ON t0.belong_org = t4.ROW_ID
	JOIN lnk_org_ext t5 ON t4.PARENT_ORG_ID = t5.ROW_ID
	JOIN qy_optystage_record t3 ON t1.row_id = t3.OPTY_ID 
GROUP BY
	DATE_FORMAT( date( t3.CREATED ), '%Y' ),
	WEEK ( t3.CREATED ) ,
	t5.org_name,
	t2.NAME


-- （3）每月
SELECT
	t2.NAME,
	DATE_FORMAT( date( t3.CREATED ), '%Y-%m' ) AS month1,
	t5.org_name,
	count(
	DISTINCT (
CASE
	WHEN t3.after_stage = 'RepeatRuler' 
	AND t0.ROW_ID NOT IN (
SELECT DISTINCT
	t0.ROW_ID 
FROM
	qy_optystage_record temp1
	JOIN lnk_opty temp2 ON temp1.OPTY_ID = temp2.row_id 
WHERE
	temp2.HEAD_ID = t0.ROW_ID 
	AND temp1.CREATED < t3.CREATED 
	AND temp1.after_stage = 'RepeatRuler' 
	) THEN
	t0.ROW_ID 
END 
	) 
	) total 
FROM
	qy_clue_account t0
	JOIN lnk_opty t1 ON t1.HEAD_ID = t0.ROW_ID
	JOIN lnk_list_of_val t2 ON t1.BELONG_DEPT = t2.val
	JOIN lnk_accnt t4 ON t0.belong_org = t4.ROW_ID
	JOIN lnk_org_ext t5 ON t4.PARENT_ORG_ID = t5.ROW_ID
	JOIN qy_optystage_record t3 ON t1.row_id = t3.OPTY_ID 
GROUP BY
DATE_FORMAT( date( t3.CREATED ), '%Y-%m' ),
	t5.org_name,
	t2.NAME



-- 6、渠道+时间+办事处+客户经理
-- 
-- （1）每日
SELECT
t2.name,
	DATE_FORMAT( date( t3.CREATED ), '%Y-%m-%d' ) AS day1,
	t5.org_name,
	t6.fst_name AS manager_name,
	count(
	DISTINCT (
CASE
	
	WHEN t3.after_stage = 'RepeatRuler' 
	AND t0.ROW_ID NOT IN (
SELECT DISTINCT
	t0.ROW_ID 
FROM
	qy_optystage_record temp1
	JOIN lnk_opty temp2 ON temp1.OPTY_ID = temp2.row_id 
WHERE
	temp2.HEAD_ID = t0.ROW_ID 
	AND temp1.CREATED < t3.CREATED 
	AND temp1.after_stage = 'RepeatRuler' 
	) THEN
	t0.ROW_ID 
END 
	) 
	) total 
FROM
	qy_clue_account t0
	JOIN lnk_opty t1 ON t1.HEAD_ID = t0.ROW_ID
	JOIN lnk_list_of_val t2 ON t1.BELONG_DEPT = t2.val 
	JOIN lnk_accnt t4 ON t0.belong_org = t4.ROW_ID
	JOIN lnk_org_ext t5 ON t4.PARENT_ORG_ID = t5.ROW_ID
	JOIN lnk_emp_info t6 ON upper( t4.CP_MANAGER_ID ) = t6.username
	JOIN qy_optystage_record t3 ON t1.row_id = t3.OPTY_ID 
GROUP BY
	DATE_FORMAT( date( t3.CREATED ), '%Y-%m-%d' ),
	t5.org_name,
	t6.fst_name,
	t2.name

-- （2）每周
SELECT
t2.name,
	DATE_FORMAT( date(  t3.CREATED ), '%Y' ) AS year1,
	WEEK (  t3.CREATED ) AS week1,
	t5.org_name,
	t6.fst_name AS manager_name,
	count(
	DISTINCT (
CASE
	
	WHEN t3.after_stage = 'RepeatRuler' 
	AND t0.ROW_ID NOT IN (
SELECT DISTINCT
	t0.ROW_ID 
FROM
	qy_optystage_record temp1
	JOIN lnk_opty temp2 ON temp1.OPTY_ID = temp2.row_id 
WHERE
	temp2.HEAD_ID = t0.ROW_ID 
	AND temp1.CREATED < t3.CREATED 
	AND temp1.after_stage = 'RepeatRuler' 
	) THEN
	t0.ROW_ID 
END 
	) 
	) total 
FROM
	qy_clue_account t0
	JOIN lnk_opty t1 ON t1.HEAD_ID = t0.ROW_ID
	JOIN lnk_list_of_val t2 ON t1.BELONG_DEPT = t2.val 
	JOIN lnk_accnt t4 ON t0.belong_org = t4.ROW_ID
	JOIN lnk_org_ext t5 ON t4.PARENT_ORG_ID = t5.ROW_ID
	JOIN lnk_emp_info t6 ON upper( t4.CP_MANAGER_ID ) = t6.username
	JOIN qy_optystage_record t3 ON t1.row_id = t3.OPTY_ID 
GROUP BY
	DATE_FORMAT( date(  t3.CREATED ), '%Y' ) ,
	WEEK (  t3.CREATED ),
	t5.org_name,
	t6.fst_name,
	t2.name

-- （3）每月

SELECT
t2.name,
	DATE_FORMAT( date( t3.CREATED  ), '%Y-%m' ) AS month1,
	t5.org_name,
	t6.fst_name AS manager_name,
	count(
	DISTINCT (
CASE
	WHEN t3.after_stage = 'RepeatRuler' 
	AND t0.ROW_ID NOT IN (
SELECT DISTINCT
	t0.ROW_ID 
FROM
	qy_optystage_record temp1
	JOIN lnk_opty temp2 ON temp1.OPTY_ID = temp2.row_id 
WHERE
	temp2.HEAD_ID = t0.ROW_ID 
	AND temp1.CREATED < t3.CREATED 
	AND temp1.after_stage = 'RepeatRuler' 
	) THEN
	t0.ROW_ID 
END 
	) 
	) total 
FROM
	qy_clue_account t0
	JOIN lnk_opty t1 ON t1.HEAD_ID = t0.ROW_ID
	JOIN lnk_list_of_val t2 ON t1.BELONG_DEPT = t2.val 
	JOIN lnk_accnt t4 ON t0.belong_org = t4.ROW_ID
	JOIN lnk_org_ext t5 ON t4.PARENT_ORG_ID = t5.ROW_ID
	JOIN lnk_emp_info t6 ON upper( t4.CP_MANAGER_ID ) = t6.username
	JOIN qy_optystage_record t3 ON t1.row_id = t3.OPTY_ID 
GROUP BY
DATE_FORMAT( date( t3.CREATED  ), '%Y-%m' ),
	t5.org_name,
	t6.fst_name,
	t2.name

```

