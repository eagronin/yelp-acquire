# Data Acquisition

## Overview

This section describes the process of loading Yelp dataset into SQL in order to explore and prepare the data for predictive modeling of the number of stars that users assign in their reviews.

Data exploration and preparation for predictive modeling are discussed in the [next section](https://eagronin.github.io/yelp-prepare/).

This project is based on assignments from SQL for Data Science by University of California, Davis on Coursera.

The analysis for this project was performed in MySQL.

## Description of the Yelp dataset

The Yelp dataset `yelp_db.sql` has been downloaded from the [Yelp Open Dataset](https://www.yelp.com/dataset) website.

The association and structure of the tables is shown below:

![](https://github.com/eagronin/yelp-acquire/blob/master/yelp_dataset_schema.png?raw=true)

## Loading Yelp dataset into SQL

We first change the directory to `/usr/local/mysql`, log in to mysql from the command line and create a database `yelp_db`: 

```mysql
cd /usr/local/mysql
bin/mysql -u root -p
mysql> create database yelp_db
```

Let's now exit from mysql, change the directory in the command line to the location of the dataset `yelp_db.sql` that we want to load, and then load the data:

```mysql
mysql> exit
cd /Users/eagronin/Documents/"Data Science"/"SQL for Data Science"
/usr/local/mysql/bin/mysql -u root -p yelp_db < yelp_db.sql
```

# Data Preparation

## Overview

This section explores Yelp dataset and prepares the data for predictive modeling of the number of stars that users assign in their reviews.

The description and process of loading the Yelp dataset into MySQL are discussed in the [previous section](https://eagronin.github.io/yelp-acquire/).

This project is based on assignments from SQL for Data Science by University of California, Davis on Coursera.

The analysis for this project was performed in MySQL.

Data exploration was performed by running a SQL script file `yelp_sql_code.sql` in which we included the code discussed in detail below.  In order to run the script, we execute the following lines of code:

```mysql
mysql> use yelp_db
mysql> source /Users/eagronin/Documents/Data Science/Portfolio/Project Code/yelp_sql_code.sql
```

The output was automatically recorded into an output file `yelp_output.txt`.  This was accomplished by adding 

```mysql
tee /Users/eagronin/Documents/Data Science/Portfolio/Project Code/yelp_output.txt
```

in the beginning and `notee` in the end of `yelp_sql_code.sql`.

# Yelp Dataset Profiling and Understanding

The following code profiles the data by finding the total number of records for each of the tables in the database:

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

The number of records for each of the tables is summarized below:

| Table Name | No. Records |
| --- | ---:|
| Attribute table | 1,310,575 | 
| Business table | 174,567 | 
| Category table | 667,527 | 
| Checkin table | 3,911,218 | 
| elite_years table | 187,125 | 
| friend table | 49,626,957 | 
| hours table | 824,595 | 
| photo table | 206,949 | 
| review table | 5,261,669 | 
| tip table | 1,098,325 | 
| user table | 1,326,101 | 


Next we check if there are any NULL values in the Users table:

```mysql
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
```

The output below shows that there are no NULL values in the user table:


| Feature Name | No. NULL Values |
| --- |:---:|
| id | - |
| name | - |
| review_count | - |
| yelping_since | - |
| useful | - |
| funny | - |
| cool | - |
| fans | - |
| average_stars | - |
| compliment_hot | - |
| compliment_more | - |
| compliment_profile | - |
| compliment_cute | - |
| compliment_list | - |
| compliment_note | - |
| compliment_plain | - |
| compliment_cool | - |
| compliment_funny | - |
| compliment_writer | - |
| compliment_photos | - |


To find the minimum, maximum, and average value for selected features listed in the output table below, we run the following code: 

```mysql
select min(stars), max(stars), avg(stars) from review;
select min(stars), max(stars), avg(stars) from business;
select min(likes), max(likes), avg(likes) from tip;
select min(count), max(count), avg(count) from checkin;
select min(review_count), max(review_count), avg(review_count) from user;
```

This generated the following output:

| Table | Column | Min | Max | Average |
| --- | --- |:---:| ---:| ---:|
| Review | Stars | 1 | 5 | 3.7277 |
| Business | Stars | 1 | 5 | 3.6322 |
| Tip | Likes | 0 | 15 | 0.0166 |
| Checkin | Count | 1 | 1,478 | 4.2566 |
| User | Review_count | 0 | 11,954 | 23.1172 |


Identifying the cities with the most reviews, we list top 25 cities in terms of the number of reviews in descending order:

```mysql
select city, sum(review_count) as all_review_count
from business
group by city
order by all_review_count desc
limit 25;
```

This generated the following output:

```
+-----------------+------------------+
| city            | all_review_count |
+-----------------+------------------+
| Las Vegas       |          1605343 |
| Phoenix         |           576709 |
| Toronto         |           430985 |
| Scottsdale      |           308529 |
| Charlotte       |           237118 |
| Pittsburgh      |           179471 |
| Henderson       |           166884 |
| Tempe           |           162772 |
| Mesa            |           134156 |
| Montréal        |           128285 |
| Chandler        |           122343 |
| Gilbert         |            97204 |
| Cleveland       |            92280 |
| Madison         |            86614 |
| Glendale        |            76293 |
| Edinburgh       |            48838 |
| Mississauga     |            43147 |
| Peoria          |            42584 |
| Markham         |            38840 |
| North Las Vegas |            37928 |
| Champaign       |            26260 |
| Surprise        |            25740 |
| Stuttgart       |            25537 |
| Goodyear        |            21508 |
| Richmond Hill   |            18329 |
+-----------------+------------------+
25 rows in set (0.12 sec)
```


6. Find the distribution of star ratings to the business in the following cities:

	i. Avon
	ii. Beachwood



Avon:

```mysql
select stars, count(id) as number_of_businesses
from business
where city = 'Avon'
group by stars;
```

This generated the following output:

```

```


Beachwood:

```mysql
select stars, count(id) as number_of_businesses
from business
where city = 'Beachwood'
group by stars;
```

This generated the following output:

```

```


7. Find the top 3 users based on their total number of reviews: 
```mysql
select id
       ,name
       ,review_count
from user
order by review_count desc
limit 3;
```

This generated the following output:

```

```


8. Does posing more reviews correlate with more fans? 
```mysql
select avg(fans) as fans_per_user,

case
	when review_count <= 500 then "1. count <= 500"
	when review_count > 500 and useful <= 1000 then "2. 500 < count <= 1000"
	else "3. count > 1000"
end review_category

from user
group by review_category;
```

This generated the following output:

```

```


9. Are there more reviews with the word "love" or with the word "hate" in them?
```mysql
select count(id) as number_of_reviews
from review
where text like '%love%';

select count(id) as number_of_reviews
from review
where text like '%hate%';
```

This generated the following output:

```

```


10. Find the top 10 users with the most fans:
```mysql
select id, name, fans
from user
order by fans desc
limit 10;
```

This generated the following output:

```

```


11. Is there a strong correlation between having a high number of fans and being listed as "useful" or "funny?"

-- SQL code for "useful":
```mysql
select avg(fans) as fans_per_user,

case			-- create a categorical variable indicating when the user is very useful (2), somewhat useful (1), and likely not useful (0)
when useful = 0 then "1. low"
when useful >= 1 and useful <= 3 then "2. medium"
else "3. high"
end useful_category

from user
group by useful_category;
```

This generated the following output:

```

```

-- SQL code for "funny":
```mysql
select avg(fans) as fans_per_user,

case			-- create a categorical variable indicating when the user is very funny (2), somewhat funny (1), and likely not funny (0)
when funny = 0 then "1. low"
when funny >= 1 and funny <= 3 then "2. medium"
else "3. high"
end funny_category

from user
group by funny_category;
```

This generated the following output:

```

```


Part 2: Inferences and Analysis

1. Group restaurants in Las Vegas by their overall star rating. 
Compare the restaurants with 2-3 stars to the businesses with 4-5 stars. 
Do the two groups have a different number of reviews? 
	
```mysql
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
```

This generated the following output:

```

```


2. Group business based on the ones that are open and the ones that are closed. 
Do the two groups have different number of stars?
Do the two groups have different number of reviews?


```mysql
select is_open
       ,count(id) as num_of_businesses
       ,avg(stars) as stars_per_business
       ,avg(review_count) as num_reviews_per_business

from business 
group by is_open;
```

This generated the following output:

```

```


Prepare data for the analysis of the effect of user characteristics on the number of stars that 
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

```mysql
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
```

This generated the following output:

```

```

```mysql
notee
```
