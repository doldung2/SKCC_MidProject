# IntensiveCource Mid Project Report(Team2)
## 1. Map of restaurants across the United States
### Hue에서 제공하는 Map Marker 기능을 이용하여, 위경도 정보를 바탕으로 지도에 표시되도록 함.

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
### city값을 기준으로 group by 후 count필드를 기준으로 descending 정렬함.

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
### 앞선 2번과 동일하게 처리하되 추가로 limit 15 조건 추하여 15개만 추출함.

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
### categories에 Restaurants 값을 포함하는 데이터만 추출하여 stars를 기준으로 group by 함. 

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
### business테이블과 review테이블을 join하여, review테이블의 stars를 기준으로 group by 및 오름차순 정렬 수행.

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

### star를 기준으로 추출할 경우 상위/하위의 star가 불균형 적이어서 다른 방법을 찾기로 결정.
### 하위 카테고리별도로 설정(148개) 후 10%에 해당하는 15개를 기준으로 상위 15개를 좋은 유형, 하위 15개를 하위 유형으로 선정.
<pre>
<code>
하위 카테고리 참고 사이트
https://blog.yelp.com/2018/01/yelp_category_list#section21
</code>
</pre>

### 6.1 Which type of restaurants get good reviews?
#### 6.1.1 SQL
<pre>
<code>
%sql


with category_df (
select business_id, explode(categories) as category from team2_business where array_contains(categories, "Restaurants")
)
select category_df.category
     , round(avg(team2_review.stars),2) as average_rating
     , count(team2_review.stars) as review_cnt
  from category_df
  join team2_review
    on category_df.business_id = team2_review.business_id
 where category_df.category != "Restaurants"
   and category in (
   "Afghan","African","Senegalese","South African","American (New)","American (Traditional)","Arabian","Argentine","Armenian","Asian Fusion","Australian","Austrian",
   "Bangladeshi","Barbeque","Basque","Belgian","Brasseries","Brazilian","Breakfast & Brunch","Pancakes","British","Buffets","Bulgarian","Burgers","Burmese",
   "Cafes","Themed Cafes","Cafeteria","Cajun/Creole","Cambodian","Caribbean","Dominican","Haitian","Puerto Rican","Trinidadian","Catalan","Cheesesteaks","Chicken Shop",
   "Chicken Wings","Chinese","Cantonese","Dim Sum","Hainan","Shanghainese","Szechuan","Comfort Food","Creperies","Cuban","Czech","Delis","Diners","Dinner Theater","Eritrean","Ethiopian",
   "Fast Food","Filipino","FishChips","Fondue","Food Court","Food Stands","French","Mauritius","Reunion","Game Meat","Gastropubs","Georgian","German",
   "Gluten-Free","Greek","Guamanian","Halal","Hawaiian","Himalayan/Nepalese","Honduran","Hong Kong Style Cafe","Hot Dogs","Hot Pot","Hungarian",
   "Iberian","Indian","Indonesian","Irish","Italian","Calabrian","Sardinian","Sicilian","Tuscan","Japanese","Conveyor Belt Sushi","Izakaya","Japanese Curry",
   "Ramen","Teppanyaki","Kebab","Korean","Kosher","Laotian","Latin American","Colombian","Salvadoran","Venezuelan","Live/Raw Food","Malaysian","Mediterranean",
   "Falafel","Mexican","Tacos","Middle Eastern","Egyptian","Lebanese","Modern European","Mongolian","Moroccan","New Mexican Cuisine","Nicaraguan","Noodles","Pakistani","Pan Asia",
   "Persian/Iranian","Peruvian","Pizza","Polish","Polynesian","Pop-Up Restaurants","Portuguese","Poutineries","Russian","Salad","Sandwiches","Scandinavian","Scottish","Seafood","Singaporean",
   "Slovakian","Somali","Soul Food","Soup","Southern","Spanish","Sri Lankan","Steakhouses","Supper Clubs","Sushi Bars","Syrian","Taiwanese","Tapas Bars","Tapas/Small Plates",
   "Tex-Mex","Thai","Turkish","Ukrainian","Uzbek","Vegan","Vegetarian","Vietnamese","Waffles","Wraps"
   )
 group by category_df.category
 order by average_rating desc
 limit 15
</code>
</pre>

#### 6.1.2 DATA
<img src=./images/mid_06_01.png>

#### 6.1.3 Chart
<img src=./images/mid_06_02.png>

### 6.2 What would be considered a bad review?
#### 6.2.1 SQL
<pre>
<code>
%sql


with category_df (
select business_id, explode(categories) as category from team2_business where array_contains(categories, "Restaurants")
)
select category_df.category
     , round(avg(team2_review.stars),2) as average_rating
     , count(team2_review.stars) as review_cnt
  from category_df
  join team2_review
    on category_df.business_id = team2_review.business_id
 where category_df.category != "Restaurants"
   and category in (
   "Afghan","African","Senegalese","South African","American (New)","American (Traditional)","Arabian","Argentine","Armenian","Asian Fusion","Australian","Austrian",
   "Bangladeshi","Barbeque","Basque","Belgian","Brasseries","Brazilian","Breakfast & Brunch","Pancakes","British","Buffets","Bulgarian","Burgers","Burmese",
   "Cafes","Themed Cafes","Cafeteria","Cajun/Creole","Cambodian","Caribbean","Dominican","Haitian","Puerto Rican","Trinidadian","Catalan","Cheesesteaks","Chicken Shop",
   "Chicken Wings","Chinese","Cantonese","Dim Sum","Hainan","Shanghainese","Szechuan","Comfort Food","Creperies","Cuban","Czech","Delis","Diners","Dinner Theater","Eritrean","Ethiopian",
   "Fast Food","Filipino","FishChips","Fondue","Food Court","Food Stands","French","Mauritius","Reunion","Game Meat","Gastropubs","Georgian","German",
   "Gluten-Free","Greek","Guamanian","Halal","Hawaiian","Himalayan/Nepalese","Honduran","Hong Kong Style Cafe","Hot Dogs","Hot Pot","Hungarian",
   "Iberian","Indian","Indonesian","Irish","Italian","Calabrian","Sardinian","Sicilian","Tuscan","Japanese","Conveyor Belt Sushi","Izakaya","Japanese Curry",
   "Ramen","Teppanyaki","Kebab","Korean","Kosher","Laotian","Latin American","Colombian","Salvadoran","Venezuelan","Live/Raw Food","Malaysian","Mediterranean",
   "Falafel","Mexican","Tacos","Middle Eastern","Egyptian","Lebanese","Modern European","Mongolian","Moroccan","New Mexican Cuisine","Nicaraguan","Noodles","Pakistani","Pan Asia",
   "Persian/Iranian","Peruvian","Pizza","Polish","Polynesian","Pop-Up Restaurants","Portuguese","Poutineries","Russian","Salad","Sandwiches","Scandinavian","Scottish","Seafood","Singaporean",
   "Slovakian","Somali","Soul Food","Soup","Southern","Spanish","Sri Lankan","Steakhouses","Supper Clubs","Sushi Bars","Syrian","Taiwanese","Tapas Bars","Tapas/Small Plates",
   "Tex-Mex","Thai","Turkish","Ukrainian","Uzbek","Vegan","Vegetarian","Vietnamese","Waffles","Wraps"
   )
 group by category_df.category
 order by average_rating
 limit 15
</code>
</pre>

#### 6.2.2 DATA
<img src=./images/mid_06_03.png>

#### 6.2.3 Chart
<img src=./images/mid_06_04.png>



## 7. Which restaurants have the most reviews?
### business 테이블과 review를 join하여 count가 가장 많은 1건을 추출함.

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
### elite 필드이 년도가 하나라도 있는 경우와 아닌 경우를 구분하여 count 및 average 값을 구함.

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

select case when size(team2_user.elite) > 0 then 'elite' else 'non-elite' end as isEliteUser     
     , round(avg(team2_review.stars), 2) as average_rating
from team2_user
join team2_review
on team2_user.user_id = team2_review.user_id 
group by isEliteUser

</code>
</pre>

#### 8.2.2 DATA
<img src=./images/mid_08_03.png>

#### 8.2.3 Chart
<img src=./images/mid_08_04.png>

#### 8.2.4 추가확인
#### 각 business_Id별 elite와 non-elite간의 차이는 단순 차이보다 클거라는 가정으로 추가로 분석을 해본 결과 21%(0.21)로 단순 비교한 결과 8%(0.08)보다 큰 차이가 나는 것을 확인 할 수 있었습니다.
#### SQL
<pre>
<code>
%sql
with aa as (
select case when size(team2_user.elite) > 0 then 'elite' else 'non-elite' end as isEliteUser
      ,team2_review.business_Id
      ,team2_review.stars
from team2_user
join team2_review
on team2_user.user_id = team2_review.user_id 
), bb as (
select isEliteUser
      ,business_Id
      ,round(avg(stars),2) as avgStars
from   aa
where  isEliteUser = 'non-elite'
group by isEliteUser ,business_Id
), cc as (
select isEliteUser
      ,business_Id
      ,round(avg(stars),2) as avgStars
from   aa
where  isEliteUser = 'elite'
group by isEliteUser ,business_Id
)
select round(avg(bb.avgStars - cc.avgStars),2) as gapStars
from   bb
      ,cc
where  bb.business_Id = cc.business_Id
</code>
</pre>



## 조 과제
### 1. 착한 유저, 나쁜 유저 식별
### 리뷰 수가 10개 이상인 유저 그룹에서 유저별 별점 수를 식별.

### 주요 불만 고객 식별

#### SQL
<pre>
<code>
%sql

select team2_user.user_id
     , avg(stars) as average_rating
     , count(*) as review_cnt
  from team2_review
  join team2_user
    on team2_review.user_id = team2_user.user_id
 group by team2_user.user_id
having review_cnt >= 10
 order by average_rating
        , review_cnt desc
</pre>
</code>

#### 결과
<img src=./images/add_01.png>

### 최고 불만고객의 리뷰들

#### SQL
<pre>
<code>
%sql

select stars, 
       text 
from team2_review
where user_id = "boVYlNwPXT7xTGCotNtUjw"
</pre>
</code>

#### 결과
<img src=./images/add_02.png>


### 주요 칭찬고객 식별

#### SQL
<pre>
<code>
select team2_user.user_id
     , avg(stars) as average_rating
     , count(*) as review_cnt
  from team2_review
  join team2_user
    on team2_review.user_id = team2_user.user_id
 group by team2_user.user_id
having review_cnt >= 10
 order by average_rating desc
        , review_cnt desc
</pre>
</code>

#### 결과
<img src=./images/add_03.png>

### 최고 칭찬고객의 리뷰들

#### SQL
<pre>
<code>
%sql
select stars, 
       text 
from team2_review 
where user_id = "8JwSmvviX2dEAgaPRZ70nQ"
</pre>
</code>

#### 결과
<img src=./images/add_04.png>

### 결론
<pre>
<code>
리뷰의 별점을 바탕으로 고객의 리뷰 성향을 구분할 수 있다. 
</pre>
</code>


### 2. 리뷰 수와 별점의 관계 분석
### 리뷰가 많다고 좋은 레스토랑인지에 대한 검증을 위해서 과제 선택.

#### SQL
<pre>
<code>
select corr(review_count, stars) as corr 
from team2_business
</pre>
</code>

#### 결과
<img src=./images/add_05.png>

### 결론
<pre>
<code>
리뷰가 많다고 꼭 별점이 높은 것은 아니다. (상관관계 0.03)
</pre>
</code>