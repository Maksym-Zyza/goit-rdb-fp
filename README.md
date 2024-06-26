# goit-rdb-fp

## task 1
```sql
Create database pandemic;

Use pandemic;

Select * from infectious_cases;
```

## task 2.1
```sql
Create table countries (
id int auto_increment primary key,
country_name varchar(45) unique not null, 
country_code varchar(8) unique not null
);

Insert into countries (country_code, country_name)
select distinct code, entity from infectious_cases;

Select * from countries; 
```

## task 2.2
```sql
Create table infectious (
id int auto_increment primary key,
country_id int not null, 
year int,
number_yaws varchar(45),
polio_cases int,
cases_guinea_worm int,
number_rabies varchar(45),
number_malaria varchar(45),	
number_hiv varchar(45),
number_tuberculosis varchar(45),
number_smallpox varchar(45),
number_cholera_cases varchar(45)
);

Insert into infectious (
country_id, 
year,
number_yaws,
polio_cases,
cases_guinea_worm,
number_rabies,
number_malaria,	
number_hiv,
number_tuberculosis,
number_smallpox,
number_cholera_cases
)
select
distinct c.id,
year,
Number_yaws,
polio_cases,
cases_guinea_worm,
Number_rabies,
Number_malaria,
Number_hiv,
Number_tuberculosis,
Number_smallpox,
Number_cholera_cases
from infectious_cases as ic
inner join countries as c on c.country_code = ic.Code and c.country_name = ic.Entity;

Select * from infectious; 
```

## task 3
```sql
Select 
country_id,
    avg(number_rabies) as average_rabies,
    min(number_rabies) as min_rabies,
    max(number_rabies) as max_rabies,
    sum(number_rabies) as sum_rabies
from infectious
where number_rabies is not null and number_rabies != ''
group by country_id
order by average_rabies desc
limit 10;
```

## task 4
```sql
Alter table infectious 
add date_start date,
add date_now date,
add date_diff int;

Set sql_safe_updates = 0;

Update infectious
set 
date_start = date(concat(year, '-01-01')),
date_now = curdate(),
date_diff = timestampdiff(year, date(concat(year, '-01-01')), curdate())
where year is not null
and id is not null;

Set sql_safe_updates = 1;
```

## task 5.1
```sql
Drop function if exists getYearDiff;

Delimiter //
Create function getYearDiff(year int)
returns int
deterministic
no sql
begin
	declare result int;
    set result = timestampdiff(year, date(concat(year, '-01-01')), curdate());
    return result;
end //
Delimiter ;

Select id, year, date_start, date_now, getYearDiff(year) as date_diff from infectious;
```

## task 5.2
```sql
Drop function if exists getCalculateDeseases;

Delimiter //
Create function getCalculateDeseases(count_deseases float, period int)
returns float
deterministic
no sql
begin
    return count_deseases / period;
end //
Delimiter ;

Set @year = 1;
Set @half_year = 2;
Set @quartal = 4;

Select 
year,
number_rabies,
getCalculateDeseases(number_rabies, @year) as per_year,
getCalculateDeseases(number_rabies, @half_year) as per_half_year,
getCalculateDeseases(number_rabies, @quartal) as per_quartal
from infectious
where number_rabies is not null and number_rabies != '';
```