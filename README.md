Example: loading yelp_db.sql - 

log into mysql from the prompt from /usr/local/mysql: bin/mysql -u root -p
- create a new database: create database yelp_db
- exit from mysql: exit
- change directory to the location of yelp_db.sql: cd /Users/eagronin/Documents/"Data Science"/"SQL for Data Science"
- load sql dump: /usr/local/mysql/bin/mysql -u root -p yelp_db < yelp_db.sql
- run batch code: source /Users/eagronin/Documents/Data Science/Portfolio/Project Code/yelp_sql_code.sql



tee /Users/eagronin/Documents/Data Science/Portfolio/Project Code/yelp_output.txt


/* Part 1: Yelp Dataset Profiling and Understanding */

1. Profile the data by finding the total number of records for each of the tables below:

	i. 	Attribute table
	ii. 	Business table
	iii. 	Category table
	iv. 	Checkin table
	v. 	elite_years table
	vi. 	friend table
	vii. 	hours table
	viii. 	photo table
	ix. 	review table
	x. 	tip table
	xi. 	user table



```mysql
select count(*) from Attribute;
select count(*) from Business;
select count(*) from Category;
select count(*) from Checkin;
select count(*) from elite_years;
select count(*) from friend;
select count(*) from hours;
select count(*) from photo;
select count(*) from review;
select count(*) from tip;
select count(*) from user;
```

/* 2. Find the total number of distinct records for each of the keys listed below:

	i. 	Business 
	ii. 	Hours  
	iii. 	Category  
	iv. 	Attribute 
	v. 	Review  
	vi. 	Checkin  
	vii. 	Photo 
	viii. 	Tip 
	ix. 	User 
	x. 	Friend 
	xi. 	Elite_years

*/

select count(distinct id) from Business;
select count(distinct business_id) from Hours;
select count(distinct business_id) from Category;
select count(distinct business_id) from Attribute;

select count(distinct id) from Review;
select count(distinct business_id) from Review;
select count(distinct user_id) from Review;

select count(distinct business_id) from Checkin;

select count(distinct id) from Photo;
select count(distinct business_id) from Photo;

select count(distinct business_id) from Tip;
select count(distinct user_id) from Tip;

select count(distinct id) from User;
select count(distinct user_id) from Friend;
select count(distinct user_id) from Elite_years;


/* 3. Are there any columns with null values in the Users table? */

	select id from user where id is NULL;
	select id from user where name is NULL;
	select id from user where review_count is NULL;
	select id from user where yelping_since is NULL;
	select id from user where useful is NULL;
	select id from user where funny is NULL;
	select id from user where cool is NULL;
	select id from user where fans is NULL;
	select id from user where average_stars is NULL;
	select id from user where compliment_hot is NULL;
	select id from user where compliment_more is NULL;
	select id from user where compliment_profile is NULL;
	select id from user where compliment_cute is NULL;
	select id from user where compliment_list is NULL;
	select id from user where compliment_note is NULL;
	select id from user where compliment_plain is NULL;
	select id from user where compliment_cool is NULL;
	select id from user where compliment_funny is NULL;
	select id from user where compliment_writer is NULL;
	select id from user where compliment_photos is NULL;


/* 4. Find the minimum, maximum, and average value for the following fields:

	i. Table: Review, Column: Stars
	ii. Table: Business, Column: Stars
	iii. Table: Tip, Column: Likes
	iv. Table: Checkin, Column: Count
	v. Table: User, Column: Review_count

*/

select min(stars), max(stars), avg(stars) from review;
select min(stars), max(stars), avg(stars) from business;
select min(likes), max(likes), avg(likes) from tip;
select min(count), max(count), avg(count) from checkin;
select min(review_count), max(review_count), avg(review_count) from user;


/* 5. List the cities with the most reviews in descending order (limit the list to top 25 cities): */

select city, sum(review_count) as all_review_count
from business
group by city
order by all_review_count desc
limit 25;


/* 6. Find the distribution of star ratings to the business in the following cities:

	i. Avon
	ii. Beachwood

*/

select stars, count(id) as number_of_businesses
from business
where city = 'Avon'
group by stars;

select stars, count(id) as number_of_businesses
from business
where city = 'Beachwood'
group by stars;


/* 7. Find the top 3 users based on their total number of reviews: */

select id
       ,name
       ,review_count
from user
order by review_count desc
limit 3;


/* 8. Does posing more reviews correlate with more fans? */

select avg(fans) as fans_per_user,

case
	when review_count <= 500 then "1. count <= 500"
	when review_count > 500 and useful <= 1000 then "2. 500 < count <= 1000"
	else "3. count > 1000"
end review_category

from user
group by review_category;


/* 9. Are there more reviews with the word "love" or with the word "hate" in them? */

select count(id) as number_of_reviews
from review
where text like '%love%';

select count(id) as number_of_reviews
from review
where text like '%hate%';


/* 10. Find the top 10 users with the most fans: */

select id, name, fans
from user
order by fans desc
limit 10;


/* 11. Is there a strong correlation between having a high number of fans and being listed as "useful" or "funny?" */

-- SQL code for "useful":

select avg(fans) as fans_per_user,

case			-- create a categorical variable indicating when the user is very useful (2), somewhat useful (1), and likely not useful (0)
when useful = 0 then "1. low"
when useful >= 1 and useful <= 3 then "2. medium"
else "3. high"
end useful_category

from user
group by useful_category;

-- SQL code for "funny":

select avg(fans) as fans_per_user,

case			-- create a categorical variable indicating when the user is very funny (2), somewhat funny (1), and likely not funny (0)
when funny = 0 then "1. low"
when funny >= 1 and funny <= 3 then "2. medium"
else "3. high"
end funny_category

from user
group by funny_category;


/* Part 2: Inferences and Analysis */

/* 	1. Group restaurants in New York by their overall star rating. 
	Compare the restaurants with 2-3 stars to the businesses with 4-5 stars. 
	Do the two groups have a different number of reviews? */

select city
       ,category
       ,avg(review_count) as av_rev_count			-- number of reviews per business within each group
       ,count(business.id) as num_of_businesses,  

case								-- add categorical variable for the "2-3 stars" and "4-5 stars" categories
  when stars >= 2 and stars <= 3 then '2-3 stars'
  when stars >= 4 and stars <= 5 then '4-5 stars'
  else NULL
end star_group

from business 							-- city comes from the "business" table and category comes from the "category" table
     left join category
     on business.id = category.business_id

where city = 'Las Vegas' and category = 'Restaurants'
group by star_group;


/* 2.	Group business based on the ones that are open and the ones that are closed. 
	Do the two groups have different number of stars?
	Do the two groups have different number of reviews?

*/

select is_open
       ,count(id) as num_of_businesses
       ,avg(stars) as stars_per_business
       ,avg(review_count) as num_reviews_per_business

from business 
group by is_open;


/* 	Prepare data for the analysis of the effect of user characteristics on the number of stars that 
	users assign in their reviews. The dependent variable in the analysis is the number of stars that 
	the business gets in a review. The features are:

	1. The amount of time that the user has been yelping, defined as the difference between the review date and 
	yelping_since date (the number of stars may depend on the user's experience);
	2. The average number of stars assigned by the user (to multiple businesses), i.e. average_stars (whether the user is 
	a soft or hard grader or, alternatively, whether the user lives in an area with particularly high or particularly 
	low quality businesses);
	3. The total number of reviews provided by the user (again, the user's experience and also the user's engagement level);
	4. The number of fans that the user has (do hard or soft graders have more fans?);
	5. The number of people who found the user useful or funny or cool.

*/

select review.id as review_id           -- review variables
       ,review.stars
       ,review.date as review_date
       ,user.id as user_id              -- user variables
       ,user.review_count
       ,user.yelping_since
       ,user.useful
       ,user.funny
       ,user.cool
       ,user.fans
       ,user.average_stars

from review 
inner join user on review.user_id = user.id
limit 25;

notee
