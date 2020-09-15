# IntensiveCource Mid Project Report(Team2)
## 1. Map of restaurants across the United States
### 1.1 SQL
<pre>
<code>
select name,
       latitude,
       longitude
from team2_business;
</code>
</pre>

### 1.2 Map
<img src=./images/mid_01.png>



## 2. Which cities have the highest number of restaurants?
### 2.1 SQL
<pre>
<code>
%sql
with topcity_df (
select city, 
       explode(categories) as category 
from team2_business
)
select city, count(*) as city_cnt 
from topcity_df 
where category = "Restaurants" 
group by city 
order by count(*) desc 
limit 10
</code>
</pre>

### 2.2 Data
<img src=./images/mid_02_01.png>

### 2.3 Chart
<img src=./images/mid_02_02.png>



## 3. Which are the top 15 subcategories in Restaurants?
### 3.1 SQL
<pre>
<code>
%sql
with category_df (
select explode(categories) as category 
from team2_business 
where array_contains(categories, "Restaurants")
)
select category, 
       count(*) as category_cnt 
from category_df 
where category != "Restaurants" 
group by category 
order by count(*) desc 
limit 15
</code>
</pre>

### 3.2 Data
<img src=./images/mid_03_01.png>

### 3.3 Chart
<img src=./images/mid_03_02.png>