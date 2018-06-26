# Data Acquisition

## Overview

This section describes the process of loading Yelp dataset into SQL in order to explore and prepare the data for predictive modeling of the number of stars that users assign in their reviews.

Data exploration and preparation for predictive modeling are discussed in the [next section](https://eagronin.github.io/yelp-prepare/).

This project is based on assignments from SQL for Data Science by University of California, Davis on Coursera.

The analysis for this project was performed in MySQL.

## Description of the Yelp Dataset

The Yelp dataset `yelp_db.sql` has been downloaded from the [Yelp Open Dataset](https://www.yelp.com/dataset) website.

The association and structure of the tables is shown below:

![](https://github.com/eagronin/yelp-acquire/blob/master/yelp_dataset_schema.png?raw=true)

## Loading Yelp Dataset into SQL

We first change the directory to `/usr/local/mysql`, log in to mysql from the command line and create a database `yelp_db`: 

```sql
cd /usr/local/mysql
bin/mysql -u root -p
mysql> create database yelp_db
```

Let's now exit from mysql, change the directory in the command line to the location of the dataset `yelp_db.sql` that we want to load, and then load the data:

```sql
mysql> exit
cd /Users/eagronin/Documents/"Data Science"/"SQL for Data Science"
/usr/local/mysql/bin/mysql -u root -p yelp_db < yelp_db.sql
```

Next step: [Data Preparation](https://eagronin.github.io/yelp-prepare/)
