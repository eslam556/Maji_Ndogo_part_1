# Maji Ndogo

**Author**: Eslam Mohamed Hafez <br>
**Email**: em4132275@gmail.com <br>
**LinkedIn**: https://www.linkedin.com/in/eslam-mohamed-b828521a9

**1.** Which SQL query will produce the date format "DD Month YYYY" from the time_of_record column in the visits table, as a single column?

````sql
SELECT
    CONCAT(day(time_of_record), " ", monthname(time_of_record), " ", year(time_of_record)) AS time_record
FROM
    visits
LIMIT 1;
````

**Results:**

time_record    |
:-------------:|
1 January 2021|

**2.** You are working with an SQL query designed to calculate the Annual Rate of Change (ARC) for basic rural water services:

SELECT name, wat_bas_r - LAG(wat_bas_r) OVER (PARTITION BY (a) ORDER BY (b)) FROM global_water_access ORDER BY name;

To accomplish this task, what should you use for placeholders (a) and (b)?

````sql
SELECT
    name,
    wat_bas_r - LAG(wat_bas_r) OVER (PARTITION BY name ORDER BY year) AS arc
FROM 
    global_water_access
ORDER BY
    name
LIMIT 5;
````

**Results:**

name	    |  arc |
:----------:|:----:|
Afghanistan	| None
Afghanistan	|13.339405059814453
Albania	    | None
Albania	    |3.4641036987304688
Algeria	    | None

**3.** What are the names of the two worst-performing employees who visited the fewest sites, and how many sites did the worst-performing employee visit?

````sql
SELECT
    assigned_employee_id,
    COUNT(visit_count) AS number_of_visits
FROM
    visits
GROUP BY
    assigned_employee_id
ORDER BY
    COUNT(visit_count)
LIMIT 3;
````

**Results:**

assigned_employee_id | number_of_visits |
:-------------------:|:----------------:|
20 | 15
22 | 143
44 | 218

**4.** What does the following query do?

SELECT location_id, time_in_queue, AVG(time_in_queue) OVER (PARTITION BY location_id ORDER BY visit_count) AS total_avg_queue_time FROM visits WHERE visit_count > 1 -- Only shared taps were visited > 1 ORDER BY location_id, time_of_record;

````sql
SELECT
    visit_count,
    location_id,
    time_in_queue,
    AVG(time_in_queue) OVER (PARTITION BY location_id ORDER BY visit_count) AS total_avg_queue_time
FROM 
    visits
WHERE 
visit_count > 1 -- Only shared taps were visited > 1
ORDER BY 
    location_id, time_of_record
LIMIT 5;
````

**Results:**

visit_count	| location_id | time_in_queue | total_avg_queue_time |
:----------:|:-----------:|:-------------:|:-------------------: |
2           |	AkHa00036 | 113	          | 113.0000
3	        |   AkHa00036 | 229	          | 171.0000
4	        |   AkHa00036 | 400           | 247.3333
5	        |   AkHa00036 | 120	          | 215.5000
6	        |   AkHa00036 | 185	          | 209.4000

**5.** One of our employees, Farai Nia, lives at 33 Angelique Kidjo Avenue. What would be the result if we TRIM() her address?

TRIM('33 Angelique Kidjo Avenue ')

````sql
SELECT
    TRIM('33 Angelique Kidjo Avenue  ') AS employee_name;
````

**Results:**

employee_name             |
:------------------------:|
33 Angelique Kidjo Avenue

**6.** How many employees live in Dahabu?

````sql
SELECT
    town_name,
    COUNT(employee_name) AS employees_residens
FROM
    employee
WHERE
    town_name = 'Dahabu'
GROUP BY
    town_name;
````

**Results:**

town_name | employees_residens |
:--------:|:------------------:|
Dahabu	  |         6

**7.** How many employees live in Harare, Kilimani?

````sql

SELECT
    province_name,
    town_name,
    COUNT(employee_name) AS employees_residens
FROM
    employee
WHERE
    province_name = 'Kilimani'
    and town_name = 'Harare'
GROUP BY
    province_name,
    town_name;
````

**Results:**

province_name | town_name | employees_residens |
:------------:|:---------:|:------------------:|
Kilimani      |  Harare   |         6          |

**8.** How many people share a well on average?

````sql
SELECT
    type_of_water_source,
    ROUND(AVG(number_of_people_served)) AS avg_people_per_source
FROM
    water_source
GROUP BY
    type_of_water_source;
````

**Results:**

type_of_water_source | avg_people_per_source |
:-------------------:|:---------------------:|
tap_in_home	         |          644          |
tap_in_home_broken   |          649          |
well                 |          279          |
shared_tap	         |          2071         |
river	             |          699          |

**9.** Consider the query we used to calculate the total number of people served by each water source:

SELECT type_of_water_source, SUM(number_of_people_served) AS population_served FROM water_source GROUP BY type_of_water_source ORDER BY population_served DESC;

Which of the following lines of code will calculate the total number of people using some sort of tap?

````sql
SELECT
    type_of_water_source,
    SUM(number_of_people_served) AS population_served
FROM
    water_source
WHERE
    type_of_water_source LIKE "%tap%"
GROUP BY
    type_of_water_source
ORDER BY
    population_served DESC;
````

**Results:**

type_of_water_source | population_served |
:-------------------:|:-----------------:|
shared_tap 	         |          11945272 |
tap_in_home          |          4678880  |
tap_in_home_broken   |          3799720  |

**10.** Use the pivot table we created to answer the following question. What are the average queue times for the following times?

Saturday from 12:00 to 13:00 Tuesday from 18:00 to 19:00 Sunday from 09:00 to 10:00

````sql
SELECT
    TIME_FORMAT(TIME(time_of_record), '%H:00') AS hour_of_week,
    ROUND(AVG(
    CASE
        WHEN DAYNAME(time_of_record) = 'Saturday' THEN time_in_queue
        ELSE NULL
    END), 0) AS Saturday,
    ROUND(AVG(
    CASE
        WHEN DAYNAME(time_of_record) = 'Sunday' THEN time_in_queue
        ELSE NULL
    END), 0) AS Sunday,
    ROUND(AVG(
    CASE
        WHEN DAYNAME(time_of_record) = 'Monday' THEN time_in_queue
        ELSE NULL
    END), 0) AS Monday,
    ROUND(AVG(
    CASE
        WHEN DAYNAME(time_of_record) = 'Tuesday' THEN time_in_queue
        ELSE NULL
    END), 0) AS  Tuesday,
    ROUND(AVG(
    CASE
        WHEN DAYNAME(time_of_record) = 'Wednesday' THEN time_in_queue
        ELSE NULL
    END), 0) AS Wednesday,
    ROUND(AVG(
    CASE
        WHEN DAYNAME(time_of_record) = 'Thursday' THEN time_in_queue
        ELSE NULL
    END), 0) AS Thursday,
    ROUND(AVG(
    CASE
        WHEN DAYNAME(time_of_record) = 'Friday' THEN time_in_queue
        ELSE NULL
    END), 0) AS Friday
FROM
    visits
WHERE
    time_in_queue <> 0
GROUP BY
    hour_of_week
ORDER BY
    hour_of_week;
````

**Results:**

hour_of_week | Saturday | Sunday | Monday | Tuesday | Wednesday | Thursday | Friday |
:-----------:|:--------:|:------:|:------:|:-------:|:---------:|:--------:|:------:|
06:00        |    247   |   79   |  190   |	  134   |    112    |    134   |153     |
07:00        |    247   |   82   |	186   |	  128	|    111	|    139   |156     |
08:00        |    247	|   86	 |  183   |	  130	|    119    |	 129   |153     |
09:00        |    252	|   84	 |  127	  |   105	|    94  	|     99   |107     |
10:00        |    259	|   83	 |  119   |	  99	|    89     |	  95   |112     |
11:00        |    236   |   78   |  115   |	  102   |    86     |    99	   |104     |
12:00        |    239   |   78   |  115   |	  97    |    88     |    96	   |109     |
13:00        |    242   |   81   |  122   |	  97    |    98     |    101   |115     |
14:00        |    244   |   83   |  127   |	  104   |    92     |    96	   |110     |
15:00        |    248   |   83   |  126   |	  104   |    88	    |    92	   |110     |
16:00        |    251   |   83   |  127   |	  99    |    90	    |    99	   |109     |
17:00        |    251   |   79   |  181   |	  135   |    121	|    129   |151     |
18:00        |    240   |   80   |  174   |	  122   |    113	|    132   |158     |
19:00        |    282   |   127  |  159   |	  145   |    176	|    137   |103     |