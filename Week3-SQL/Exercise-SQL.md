
# SQL:  Structured Query Language  Exercise

### Getting Started
1. Go to BigQuery UI https://console.cloud.google.com/bigquery
2. Add in the public data sets. 
	3. Click the Add Data icon
	4. Add any dataset
	5. `bigquery-public-data` should become visible and populate in the BigQuery UI. 
3. Add your queries where it says [YOUR QUERY HERE].
4. Make sure you add your query in between the triple tick marks. 
---

For this section of the exercise we will be using the `bigquery-public-data.austin_311.311_service_requests`  table. 

5. Write a query that tells us how many rows are in the table. 
	```
	SELECT
	  count(*) as total_rows
	FROM
	  `bigquery-public-data.austin_311.311_service_requests`
	```

7. Write a query that tells us how many _distinct_ values there are in the complaint_description column.
	``` 
	SELECT
	 COUNT(DISTINCT complaint_description) AS Total_type_of_complaint
	FROM
	  `bigquery-public-data.austin_311.311_service_requests`
	```
  
8. Write a query that counts how many times each owning_department appears in the table and orders them from highest to lowest. 
	``` 
	
	SELECT
	  owning_department ,
    	COUNT(owning_department) as total_
	FROM
	  `bigquery-public-data.austin_311.311_service_requests`
 	 GROUP BY
   	  owning_department
 	 ORDER BY
   	 total_ DESC;
	```

9. Write a query that lists the top 5 complaint_description that appear most and the amount of times they appear in this table. (hint... limit)
	```
	SELECT
	  owning_department ,
    	COUNT(owning_department) as total_
	FROM
	  `bigquery-public-data.austin_311.311_service_requests`
 	 GROUP BY
   	  owning_department
 	 ORDER BY
   	 total_ DESC
   	LIMIT
   	  5
	  ```
10. Write a query that lists and counts all the complaint_description, just for the where the owning_department is 'Animal Services Office'.
	```
	WITH T AS (
	SELECT
    	*
	FROM
	  `bigquery-public-data.austin_311.311_service_requests`
 	WHERE
    owning_department = 'Animal Services Office'
   )
  SELECT 
  complaint_description,
  COUNT(complaint_description) AS complaint
  FROM
  T
  GROUP BY
   	  complaint_description
 	 ORDER BY
   	 complaint DESC
	```

11. Write a query to check if there are any duplicate values in the unique_key column (hint.. There are two was to do this, one is to use a temporary table for the groupby, then filter for values that have more than one count, or, using just one table but including the  `having` function). 
	```
	SELECT *
FROM (
  SELECT
      *,
      ROW_NUMBER()
          OVER (PARTITION BY unique_key)
          row_number
  FROM `bigquery-public-data.austin_311.311_service_requests`
)
WHERE row_number = 1

	```


### For the next question, use the `census_bureau_usa` tables.

1. Write a query that returns each zipcode and their population for 2000 and 2010. 
	```
	[YOUR QUERY HERE]
	```





### For the next section, use the  `bigquery-public-data.google_political_ads.advertiser_weekly_spend` table.
1. Using the `advertiser_weekly_spend` table, write a query that finds the advertiser_name that spent the most in usd. 
	```
	SELECT
	  advertiser_name,
   	 SUM(spend_usd) AS total_spending
	FROM
	 `bigquery-public-data.google_political_ads.advertiser_weekly_spend`
	GROUP BY
  	advertiser_name
	ORDER BY 
 	total_spending DESC 
	LIMIT
  	1
	```
2. Who was the 6th highest spender? (No need to insert query here, just type in the answer.)
	```
		
TOM STEYER 2020
	```

3. What week_start_date had the highest spend? (No need to insert query here, just type in the answer.)
	```
	2020-09-13
	```

4. Using the `advertiser_weekly_spend` table, write a query that returns the sum of spend by week (using week_start_date) in usd for the month of August only. 
	```
	[YOUR QUERY HERE]
	```
6.  How many ads did the 'TOM STEYER 2020' campaign run? (No need to insert query here, just type in the answer.)
	```
	49986
	```
7. Write a query that has, in the US region only, the total spend in usd for each advertiser_name and how many ads they ran. (Hint, you're going to have to join tables for this one). 
	```
	teacher answers
		WITH
	  TABLE_A AS (
	  SELECT
	    advertiser_name,
	    COUNT(DISTINCT ad_id) AS n_ads
	  FROM
	    `bigquery-public-data.google_political_ads.creative_stats`
	  WHERE
	    regions = "US"
	  GROUP BY
	    advertiser_name),
	  TABLE_B AS (
	  SELECT
	    advertiser_name,
	    SUM(spend_usd) AS spend
	  FROM
	    `bigquery-public-data.google_political_ads.advertiser_weekly_spend`
	  GROUP BY
	    advertiser_name )
	SELECT
	  A.*,
	  B.spend,
	FROM
	  TABLE_A AS A
	JOIN
	  TABLE_B AS B
	ON
	  A.advertiser_name = B.advertiser_name

	```
8. For each advertiser_name, find the average spend per ad. 
	```
	SELECT
	  advertiser_name,
   	 AVG(spend_usd) AS total_spending
	FROM
	 `bigquery-public-data.google_political_ads.advertiser_weekly_spend`
	GROUP BY
  	advertiser_name
	ORDER BY 
 	total_spending DESC 
Teacher answers

WITH
	  TABLE_A AS (
	  SELECT
	    advertiser_name,
	    COUNT(DISTINCT ad_id) AS n_ads
	  FROM
	    `bigquery-public-data.google_political_ads.creative_stats`
	  WHERE
	    regions = "US"
	  GROUP BY
	    advertiser_name),
	  TABLE_B AS (
	  SELECT
	    advertiser_name,
	    SUM(spend_usd) AS spend
	  FROM
	    `bigquery-public-data.google_political_ads.advertiser_weekly_spend`
	  GROUP BY
	    advertiser_name )
	SELECT
	  A.*,
	  B.spend,
	FROM
	  TABLE_A AS A
	JOIN
	  TABLE_B AS B
	ON
	  A.advertiser_name = B.advertiser_name
# ANSWER IS JEXAN LLC
	WITH
	  TABLE_A AS (
	  SELECT
	    advertiser_name,
	    COUNT(DISTINCT ad_id) AS n_ads
	  FROM
	    `bigquery-public-data.google_political_ads.creative_stats`
	  WHERE
	    regions = "US"
	  GROUP BY
	    advertiser_name),
	  TABLE_B AS (
	  SELECT
	    advertiser_name,
	    SUM(spend_usd) AS spend
	  FROM
	    `bigquery-public-data.google_political_ads.advertiser_weekly_spend`
	  GROUP BY
	    advertiser_name )
	SELECT
	  A.*,
	  B.spend,
	  B.spend / A.n_ads as avg_spend_per_ad
	FROM
	  TABLE_A AS A
	JOIN
	  TABLE_B AS B
	ON
	  A.advertiser_name = B.advertiser_name
	WHERE B.spend / A.n_ads > 0
	ORDER BY avg_spend_per_ad
	```
10. Which advertiser_name had the lowest average spend per ad that was at least above 0. 
	``` 
	SELECT
	  advertiser_name,
   	 AVG(spend_usd) AS total_spending
	FROM
	 `bigquery-public-data.google_political_ads.advertiser_weekly_spend`
  	WHERE
        spend_usd > 0
	GROUP BY
  	advertiser_name
	ORDER BY
 	total_spending 
	```
## For this next section, use the `new_york_citibike` datasets.

1. Who went on more bike trips, Males or Females?
	```
	SELECT
 	gender,
 	COUNT(gender)
	FROM
 	`bigquery-public-data.new_york_citibike.citibike_trips` 
	GROUP BY
  	gender
    	LIMIT
    	1
	```
2. What was the average, shortest, and longest bike trip taken in minutes?
	```
	SELECT
  AVG(tripduration) AS averagetrip,
  MAX(tripduration) AS maxtrip,
  MIN(tripduration) AS mintrip
FROM
 `bigquery-public-data.new_york_citibike.citibike_trips` 
	```

3. Write a query that, for every station_name, has the amount of trips that started there and the amount of trips that ended there. (Hint, use two temporary tables, one that counts the amount of starts, the other that counts the number of ends, and then join the two.) 
	```
	[YOUR QUERY HERE]
	```
# The next section is the Google Colab section.  
1. Open up this [this Colab notebook](https://colab.research.google.com/drive/1kHdTtuHTPEaMH32GotVum41YVdeyzQ74?usp=sharing).
2. Save a copy of it in your drive. 
3. Rename your saved version with your initials. 
4. Click the 'Share' button on the top right.  
5. Change the permissions so anyone with link can view. 
6. Copy the link and paste it right below this line. 
	* YOUR LINK:  _https://colab.research.google.com/drive/1mbKR7Zh6ws6Vl96dbJ3H-nxKTbXhvU0M?usp=sharing
9. Complete the two questions in the colab notebook file. 
