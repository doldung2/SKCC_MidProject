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



## 4. What ratings do the majority of restaurants have?
### 4.1 SQL
<pre>
<code>
%sql

select stars
     , count(*) as cnt
  from team2_business
 where array_contains(categories, "Restaurants")
 group by stars 
 order by cnt desc
</code>
</pre>

### 4.2 DATA
<img src=./images/mid_04_01_re.png>

### 4.3 Chart
<img src=./images/mid_04_02_re.png>



## 5. What is rating distribution in the restaurant reviews?
### 5.1 SQL
<pre>
<code>
%sql
select team2_review.stars
     , count(*) as cnt
  from team2_business
  join team2_review
    on team2_business.business_id = team2_review.business_id
 where array_contains(team2_business.categories, "Restaurants")
 group by team2_review.stars
 order by team2_review.stars
</code>
</pre>

### 5.2 DATA
<img src=./images/mid_05_01_re.png>

### 5.3 Chart
<img src=./images/mid_05_02_re.png>



## 6. Which type of restaurants get good reviews? How about bad reviews? This will depend on what you consider a good rating. Above 4 star perhaps? You choose. Similarly, for bad reviews. What would be considered a bad review?
### 6.1 Which type of restaurants get good reviews?
#### 6.1.1 SQL
<pre>
<code>

</code>
</pre>

#### 6.1.2 DATA

#### 6.1.3 Chart

### 6.2 How about bad reviews? This will depend on what you consider a good rating. Above 4 star perhaps? You choose. Similarly, for bad reviews.
#### 6.2.1 SQL
<pre>
<code>

</code>
</pre>
#### 6.2.2 DATA
#### 6.1.3 Chart

### 6.3 What would be considered a bad review?
#### 6.3.1 SQL
<pre>
<code>

</code>
</pre>
#### 6.3.2 DATA
#### 6.3.3 Chart



## 7. Which restaurants have the most reviews?
### 7.1 SQL
<pre>
<code>
%sql

select team2_business.name
     , count(*) as cnt
     , round(avg(team2_review.stars),2) as average_stars
  from team2_business
  join team2_review
    on team2_business.business_id = team2_review.business_id
 where array_contains(team2_business.categories, "Restaurants")
 group by team2_business.name
 order by cnt desc
 limit 1
</code>
</pre>

### 7.2 DATA
<img src=./images/mid_07.png>



## 8. What number of yelp users are elite users? Do they rate differently than non-elite users?
### 8.1 What number of yelp users are elite users?
#### 8.1.1 SQL
<pre>
<code>
%sql
select case when size(elite) > 0 then 'elite' else 'non-elite' end as isEliteUser,
       count(*) AS cnt
from team2_user
group by isEliteUser
</code>
</pre>

#### 8.1.2 DATA
<img src=./images/mid_08_01.png>

#### 8.1.3 Chart
<img src=./images/mid_08_02.png>

### 8.2 Do they rate differently than non-elite users?
#### 8.2.1 SQL
<pre>
<code>
%sql

</code>
</pre>

#### 8.2.2 DATA
<img src=./images/mid_08_03.png>

#### 8.2.3 Chart
<img src=./images/mid_08_04.png>


