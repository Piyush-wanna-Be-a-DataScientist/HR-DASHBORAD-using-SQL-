# HR-DASHBORAD-using-SQL- SQL /POWER BI

This project dives deep into data analysis using SQL and Power BI to uncover important  insights about  human resource  that can greatly benefit the company.
Featuring good looking dashboards offer important and benefecial HR metrics like employee turnover, diversity, recruitment efficacy and performance evaluations. These help HR professionals 
make informed decisions and strategic workforce planning.

## Source Data:
The source data contained Human Resource 22000 records from 2000 to 2020. 

## Data Cleaning & Analysis:

This was done on SQL  involving
- Data loading & inspection
- Handling missing values
- Data cleaning and analysis

## Data Visualization:
Power BI Desktop
In a corporate setting, 





![Dashboard](hr_dashboard_page-0001.jpg)
![Dashboard](hr_dashboard_page-0002.jpg)





## Exploratory Data Analysis
### Questions:
1)	What's the age distribution in the company?
2)	What's the gender breakdown in the company?
3)	How does gender vary across departments and job titles?
4)	What's the race distribution in the company?
5)	What's the average length of employment in the company?
6)	Which department has the highest turnover rate?
7)	What is the tenure distribution for each department?
8)	How many employees work remotely for each department?
9)	What's the distribution of employees across different states?
10)	How are job titles distributed in the company?
11)	How have employee hire counts varied over time?


### Findings:

1)Employees age <21 and 50+ years old are the fewest in the company. Most employees are 31-50 years old. And of course, the age group 31-40 have the most employees in the company.

2)	There are more male employees than female or non-conforming employees.

3)	The genders are fairly evenly distributed across departments. There are slightly more male employees overall.

4)	Whites, Africans american and  Mixed race  employees are the majority in the company, followed by Asian, Hispanic, native Americans and native hawaiian

5)	The average length of employment is 7.8 years.

6)	Auditing has the highest turnover rate, followed by Legal, Research & Development and Support ,Sales. Business Development & Marketing have the lowest turnover rates.

7)	Employees tend to stay with the company for 6-8 years. Tenure is quite evenly distributed across departments.

8)	About 25% of employees work remotely.

9)	Most employees are in Ohio (14,788) followed distantly by Pennsylvania (930) and Illinois (730), Indiana (572), Michigan (569), Kentucky (375) and Wisconsin (321).

10)	There are 182 job titles in the company, with Research Assistant II ,Business Analyst taking most of the employees (634) and Assistant Professor, Marketing Manager, Office Assistant IV, Associate Professor and VP of Training and Development taking the just 1 employee each.

11)	Employee hire counts have increased over the years but least hire count is year 2000 and highest is in 2018 .






### Data Cleaning & Analysis Using SQL :

### 1) Create Database
``` SQL
CREATE DATABASE hr;

```
### 2) Import Data to SQL Server

- Right-click on hr  > Import Data
- Use import wizard to import new_hr.csv to hr table.
- Verify that the import worked:

``` SQL
use hr;
```
``` SQL
SELECT *
FROM new_hr;
```
-Now examine the data 

``` SQL
describe new_hr ;
select * from new_hr;
```

### 3) DATA CLEANING

The termdate ,birthdate and hiredate was imported as text.  Hence it needs to be converted to the date format.


``` SQL

-- Change data types 

UPDATE new_hr SET hire_date = STR_TO_DATE(hire_date, "%d-%m-%Y");
UPDATE new_hr SET birthdate = STR_TO_DATE(birthdate, "%d-%m-%Y");

alter table new_hr 
modify hire_date date ;
alter table new_hr 
modify birthdate date ;


-- now for termdate

SELECT termdate FROM new_hr
ORDER BY termdate DESC ;

-- Step 1: Convert termdate to DATE format by removing the time part

UPDATE new_hr
SET termdate = date_format(STR_TO_DATE(LEFT(termdate, 19), '%Y-%m-%d %H:%i:%s'), '%Y-%m-%d')
WHERE termdate IS NOT NULL AND termdate != '';

-- Step 2: If necessary, alter the column type to DATE
UPDATE new_hr
SET termdate = NULL
WHERE termdate = '';

ALTER TABLE new_hr 
MODIFY COLUMN termdate DATE;

#### create new column "age"
``` SQL
ALTER TABLE hr_data
ADD age nvarchar(50)
```


####  -- now for the curent age of the employees 
``` SQL


ALTER table new_hr
drop COLUMN  age  ;

select * from new_hr ;

-- Add the age column
ALTER TABLE new_hr ADD COLUMN age INT;

-- Update the age column
UPDATE new_hr
SET age = TIMESTAMPDIFF(YEAR, birthdate, CURDATE());

```

## QUESTIONS TO ANSWER FROM THE DATA

-- min and max ages
``` SQL

SELECT 
  MIN(age) as Youngest,
  MAX(age) as Oldest 
INTO OUTFILE 'C:\\ProgramData\\MySQL\\MySQL Server 8.0\\Uploads\\age_distribution.csv'
FIELDS TERMINATED BY ',' 
ENCLOSED BY '"' 
LINES TERMINATED BY '\n'
FROM new_hr;
``` 

#### 1) What's the age distribution in the company?
``` SQL
SELECT
  age_group,
  COUNT(*) AS count
 INTO OUTFILE 'C:\\ProgramData\\MySQL\\MySQL Server 8.0\\Uploads\\age_distribution_class.csv'
FIELDS TERMINATED BY ',' 
ENCLOSED BY '"' 
LINES TERMINATED BY '\n' 
FROM (
  SELECT
    CASE
      WHEN age <= 21 THEN '21 and below'
      WHEN age <= 30 THEN '21 to 30'
      WHEN age <= 40 THEN '31 to 40'
      WHEN age <= 50 THEN '41 to 50'
      ELSE '50+'
    END AS age_group
  FROM new_hr
  WHERE termdate IS NULL
) AS Subquery
GROUP BY age_group
ORDER BY count;

```


- Age group by gender

``` SQL
-- distribution by gender 


SELECT
  age_group,
  gender ,
  COUNT(*) AS count
INTO OUTFILE 'C:\\ProgramData\\MySQL\\MySQL Server 8.0\\Uploads\\age_distribution_by_gender.csv'
FIELDS TERMINATED BY ',' 
ENCLOSED BY '"' 
LINES TERMINATED BY '\n'
FROM (
  SELECT
    CASE
      WHEN age <= 21 THEN '21 and below'
      WHEN age <= 30 THEN '21 to 30'
      WHEN age <= 40 THEN '31 to 40'
      WHEN age <= 50 THEN '41 to 50'
      ELSE '50+'
    END AS age_group ,
    gender
  FROM new_hr
  WHERE termdate IS NULL
) AS Subquery
GROUP BY age_group,
gender
ORDER BY age_group ,
gender  ;
```


#### 2) What's the gender breakdown in the company?

``` SQL
SELECT 
  gender,
  COUNT(gender) AS count 
INTO OUTFILE 'C:\\ProgramData\\MySQL\\MySQL Server 8.0\\Uploads\\gender_distribution.csv'
FIELDS TERMINATED BY ',' 
ENCLOSED BY '"' 
LINES TERMINATED BY '\n'
FROM new_hr
WHERE termdate IS NULL
GROUP BY gender 
ORDER BY gender ASC;
;
```

#### 3) How does gender vary across departments and job titles?

``` SQL
SELECT 
  gender,
  department ,
  COUNT(gender) AS count 
INTO OUTFILE 'C:\\ProgramData\\MySQL\\MySQL Server 8.0\\Uploads\\gender_department.csv'
FIELDS TERMINATED BY ',' 
ENCLOSED BY '"' 
LINES TERMINATED BY '\n'
FROM new_hr
WHERE termdate IS NULL
GROUP BY gender ,
department  
ORDER BY gender, department ASC;


```
- job titles

``` SQL
SELECT 
  gender,
  department ,jobtitle ,
  COUNT(gender) AS count 
INTO OUTFILE 'C:\\ProgramData\\MySQL\\MySQL Server 8.0\\Uploads\\gender_department_jobtitle.csv'
FIELDS TERMINATED BY ',' 
ENCLOSED BY '"' 
LINES TERMINATED BY '\n'
FROM new_hr
WHERE termdate IS NULL
GROUP BY gender ,
department , jobtitle
ORDER BY gender, department , jobtitle ASC;

```

#### 4) What's the race distribution in the company?

``` SQL
select race ,
count(race) as count 
INTO OUTFILE 'C:\\ProgramData\\MySQL\\MySQL Server 8.0\\Uploads\\race_count.csv'
FIELDS TERMINATED BY ',' 
ENCLOSED BY '"' 
LINES TERMINATED BY '\n'
from new_hr
WHERE termdate IS NULL
group by race 
order by count desc  ;


```

#### 5) What's the average length of employment in the company?

``` SQL
SELECT 
    id,
    hire_date,
    termdate,
    DATEDIFF(COALESCE(termdate, CURDATE()), hire_date) / 365.25 AS tenure_years
FROM 
    new_hr
ORDER BY 
    tenure_years DESC;




SELECT 
    AVG(DATEDIFF(termdate, hire_date) / 365.25) AS tenure
 INTO OUTFILE 'C:\\ProgramData\\MySQL\\MySQL Server 8.0\\Uploads\\tenure_average.csv'
FIELDS TERMINATED BY ',' 
ENCLOSED BY '"' 
LINES TERMINATED BY '\n'
FROM 
    new_hr
WHERE 
    termdate IS NOT NULL AND termdate <= CURDATE();



```

#### 6) Which department has the highest turnover rate?
- get total count
- get terminated count
- terminated count/total count

``` SQL
SELECT 
    department,
    COUNT(*) AS total_count,
    SUM(CASE WHEN termdate IS NOT NULL THEN 1 ELSE 0 END) AS terminated_count,
    ROUND(SUM(CASE WHEN termdate IS NOT NULL THEN 1 ELSE 0 END) / COUNT(*) * 100, 2) AS turnover_rate
INTO OUTFILE 'C:\\ProgramData\\MySQL\\MySQL Server 8.0\\Uploads\\turnover_rates.csv'
FIELDS TERMINATED BY ',' 
ENCLOSED BY '"' 
LINES TERMINATED BY '\n' 
FROM 
    new_hr
GROUP BY 
    department
ORDER BY 
   turnover_rate desc ;

```

#### 7) What is the tenure distribution for each department?

``` SQL
SELECT department ,
    AVG(DATEDIFF(termdate, hire_date) / 365.25) AS tenure
 INTO OUTFILE 'C:\\ProgramData\\MySQL\\MySQL Server 8.0\\Uploads\\tenure_department.csv'
FIELDS TERMINATED BY ',' 
ENCLOSED BY '"' 
LINES TERMINATED BY '\n' 
FROM 
    new_hr
WHERE 
    termdate IS NOT NULL AND termdate <= CURDATE()
    group by department
    order by tenure ;

```


#### 8) How many employees work remotely for each department?

``` SQL
select location , 
count(*) as count 
INTO OUTFILE 'C:\\ProgramData\\MySQL\\MySQL Server 8.0\\Uploads\\rempte_vs_headquater.csv'
FIELDS TERMINATED BY ',' 
ENCLOSED BY '"' 
LINES TERMINATED BY '\n' 
from new_hr
where termdate is null
group by location ;

```

#### 9) What's the distribution of employees across different states?

``` SQL
select 
location_state,
count(*) as count 
INTO OUTFILE 'C:\\ProgramData\\MySQL\\MySQL Server 8.0\\Uploads\\employees_state_disti.csv'
FIELDS TERMINATED BY ',' 
ENCLOSED BY '"' 
LINES TERMINATED BY '\n' 
from new_hr
where termdate is null 
group by  location_state 
order by count desc ;


```

#### 10) How are job titles distributed in the company?

``` SQL
select jobtitle ,
count(*) as count 
INTO OUTFILE 'C:\\ProgramData\\MySQL\\MySQL Server 8.0\\Uploads\\jobtitle_distri.csv'
FIELDS TERMINATED BY ',' 
ENCLOSED BY '"' 
LINES TERMINATED BY '\n' 
from new_hr
where termdate is null
group by jobtitle 
order by count desc ;
```

#### 11) How have employee hire counts varied over time?
- calculate hires
- calculate terminations
- (hires-terminations)/hires percent hire change

``` SQL
CREATE TEMPORARY TABLE temp_hire_data AS
SELECT
    YEAR(hire_date) AS hire_yr,
    COUNT(*) AS hires,
    SUM(CASE WHEN termdate IS NOT NULL AND termdate <= NOW() THEN 1 ELSE 0 END) AS terminations
FROM 
    new_hr
GROUP BY 
    YEAR(hire_date);
    
    
    SELECT
    hire_yr,
    hires,
    terminations,
    hires - terminations AS net_change,
    ROUND((hires - terminations) / hires * 100, 2) AS percent_hire_change
 INTO OUTFILE 'C:\\ProgramData\\MySQL\\MySQL Server 8.0\\Uploads\\employee.csv'
FIELDS TERMINATED BY ',' 
ENCLOSED BY '"' 
LINES TERMINATED BY '\n' 
FROM  
    temp_hire_data
ORDER BY 
    percent_hire_change ASC;


```




