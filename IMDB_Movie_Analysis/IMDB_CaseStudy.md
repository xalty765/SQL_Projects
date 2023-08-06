# 🎬IMDB-Case_Study_Answers

## 📌 Solution

###  Q1. Find the total number of rows in each table of the schema?

There are two ways to solve this 
1) we can take count of the table individually 
2)  or we can fetch the names from information schema and sum the table row to get the count

Approach 1: 
````sql
SELECT 
    COUNT(*)
FROM
    director_mapping;
SELECT 
    COUNT(*)
FROM
    genre;
SELECT 
    COUNT(*)
FROM
    movie;
SELECT 
    COUNT(*)
FROM
    ratings;
SELECT 
    COUNT(*)
FROM
    role_mapping;
````
Appraoch 2:
````sql
SELECT 
    table_name, SUM(table_rows) AS total_rows
FROM
    information_schema.Tables
WHERE
    table_schema = 'imdb'
GROUP BY table_name;
````
**Answer:**

![1](https://user-images.githubusercontent.com/95348482/231700757-a8eaad82-beb6-492a-aa67-cb7122ad7b39.png)

### Q2. Which columns in the movie table have null values?

````sql
SELECT 
(SELECT count(*) FROM movie WHERE id is NULL) as id,
(SELECT count(*) FROM movie WHERE title is NULL) as title,
(SELECT count(*) FROM movie WHERE year  is NULL) as year,
(SELECT count(*) FROM movie WHERE date_published  is NULL) as date_published,
(SELECT count(*) FROM movie WHERE duration  is NULL) as duration,
(SELECT count(*) FROM movie WHERE country  is NULL) as year, 
(SELECT count(*) FROM movie WHERE worlwide_gross_income  is NULL) as worlwide_gross_income,
(SELECT count(*) FROM movie WHERE languages  is NULL) as languages,
(SELECT count(*) FROM movie WHERE production_company  is NULL) as production_company;
````
**Answer:**

![image](https://user-images.githubusercontent.com/95348482/231701399-aabab8b0-8e4a-4a03-8dff-38f1ea712615.png)

### Q3. Find the total number of movies released each year? How does the trend look month wise?

#### Year-Wise Trend
````sql
SELECT 
    `year`, COUNT(*) AS number_of_movies
FROM
    movie
GROUP BY `year`;
````
**Answer:**

![image](https://user-images.githubusercontent.com/95348482/231702712-10183fe8-4f1a-4382-af91-352e9bc20f50.png)

#### Month-Wise Trend
````sql
SELECT 
    MONTHNAME(date_published) AS month_name,
    COUNT(*) AS number_of_movies
FROM
    movie
GROUP BY MONTHNAME(date_published)
ORDER BY COUNT(*) DESC;
````
**Answer:**

![image](https://user-images.githubusercontent.com/95348482/231703268-438bb9f1-b496-4ada-8d75-5e855b0fecc8.png)

### Q4. How many movies were produced in the USA or India in the year 2019?
````sql
WITH cte AS  (
SELECT  * FROM movie 
WHERE country LIKE '%India%' OR country LIKE '%USA%')
SELECT count(DISTINCT id) 
FROM cte 
WHERE `year` = 2019;
````
**Answer:**

![image](https://user-images.githubusercontent.com/95348482/231703766-24e2b854-cf93-4d2b-927a-a2d348da7d34.png)

### Q5. Find the unique list of the genres present in the data set?
````sql
SELECT DISTINCT
    GENRE
FROM
    GENRE;
````
**Answer:**

![image](https://user-images.githubusercontent.com/95348482/231704136-0da87480-afb5-401d-abf1-12727113fbf9.png)


### Q6.Which genre had the highest number of movies produced overall?

````sql
SELECT 
    genre, COUNT(DISTINCT id) AS no_of_movies
FROM
    movie m
        JOIN
    genre g ON g.movie_id = m.id
GROUP BY genre
ORDER BY COUNT(DISTINCT id) DESC
LIMIT 1;
````
**Answer:**

![image](https://user-images.githubusercontent.com/95348482/231704596-5f1de52c-869c-44e0-be97-7e6f258d5e7c.png)


### Q7. How many movies belong to only one genre?

````sql
WITH cte AS (SELECT 
    id, COUNT(genre)
FROM
    movie m
        JOIN
    genre g ON g.movie_id = m.id
GROUP BY id
HAVING COUNT(genre) = 1)
SELECT  count(id) AS no_of_movies FROM cte;
````
**Answer:**

![image](https://user-images.githubusercontent.com/95348482/231705036-a733365e-8831-4856-a858-1833b3ff86db.png)

### Q8.What is the average duration of movies in each genre? 

````sql
SELECT 
    genre, ROUND(AVG(duration), 2) AS average_duration
FROM
    movie m
        JOIN
    genre g ON g.movie_id = m.id
GROUP BY genre
ORDER BY ROUND(AVG(duration), 2) DESC;
````
**Answer:**
 
![image](https://user-images.githubusercontent.com/95348482/231705426-7b1b9c50-0b0a-465d-b1f6-f0f222989af4.png)

### Q9.What is the rank of the ‘thriller’ genre of movies among all the genres in terms of number of movies produced?
````sql
WITH cte AS (SELECT 
    genre,COUNT(id),
    RANK() OVER(ORDER BY COUNT(id) DESC) AS rnk
FROM
    movie m
        JOIN
    genre g ON g.movie_id = m.id
  GROUP BY GENRE)
SELECT rnk FROM cte 
WHERE genre ='Thriller';
````
**Answer:**
 
 ![image](https://user-images.githubusercontent.com/95348482/231708415-155ca3b8-d46c-473b-ad13-be652ef4d980.png)
 
 ### Q10.  Find the minimum and maximum values in  each column of the ratings table except the movie_id column?
 
 ````sql
 SELECT 
    MIN(avg_rating) AS min_avg_rating,
    MAX(avg_rating) AS max_avg_rating,
    MIN(total_votes) AS min_total_votes,
    MAX(total_votes) AS max_total_votes,
    MIN(median_rating) AS min_median_rating,
    MAX(median_rating) AS max_median_rating
FROM
    ratings;  
````
**Answer:**

![image](https://user-images.githubusercontent.com/95348482/231708887-a97e5fc4-621f-4002-a374-b4bca291598f.png)

### Q11. Which are the top 10 movies based on average rating?
````sql
SELECT title,avg_rating, 
DENSE_RANK() OVER(ORDER BY avg_rating DESC) AS rnk_of_movie
FROM
    ratings r
        JOIN
    movie m ON m.id = r.movie_id
    LIMIT 10;
  ````
**Answer:**

![image](https://user-images.githubusercontent.com/95348482/231709253-895cdaab-f457-45ff-8b90-d6950d23eedf.png)

### Q12. Summarise the ratings table based on the movie counts by median ratings.
````sql
SELECT median_rating,
       Count(movie_id) AS movie_count
FROM ratings
GROUP BY median_rating
ORDER BY movie_count DESC;
````
**Answer:**

![image](https://user-images.githubusercontent.com/95348482/231709604-5664b1f4-fd44-473a-ad0f-1f288f9d38d9.png)

 ### Q13. Which production house has produced the most number of hit movies (average rating > 8)?
 ````sql
  WITH cte AS (
 select * FROM
    movie m
        JOIN
    ratings r ON r.movie_id = m.id
    WHERE avg_rating > 8 )
 SELECT 
    production_company,count(id),
    RANK() OVER (ORDER BY count(id) DESC) AS rnk 
    FROM cte WHERE production_company IS NOT NULL
    GROUP BY production_company;
````
**Answer:**

![image](https://user-images.githubusercontent.com/95348482/231710264-832dd020-5cd4-494c-a78a-6057c4ac01f6.png)

### Q14. How many movies released in each genre during March 2017 in the USA had more than 1,000 votes?
````SQL
SELECT genre,
       Count(mov.id) AS movie_count
FROM movie AS m
     JOIN genre AS gen
           ON g.movie_id = m.id
     JOIN ratings AS r
           ON r.movie_id = m.id
WHERE year = 2017
	  AND Monthname(date_published) = 'March'
	  AND country LIKE '%USA%'
	  AND total_votes > 1000
GROUP BY genre
ORDER BY movie_count DESC;
````

**Answer:**

![image](https://user-images.githubusercontent.com/95348482/231710951-28190ee1-3260-457f-b665-609763a8fa13.png)

### Q15. Find movies of each genre that start with the word ‘The’ and which have an average rating > 8?
````sql
SELECT 
    title, genre
FROM
    movie m
        JOIN
    genre g ON g.movie_id = m.id
        JOIN
    ratings r ON r.movie_id = m.id
WHERE
    title LIKE 'The%' AND avg_rating > 8;
````

**Answer:**

![image](https://user-images.githubusercontent.com/95348482/231713080-f6d6310f-898d-4026-8ec8-336732c2f455.png)

### Q16. Of the movies released between 1 April 2018 and 1 April 2019, how many were given a median rating of 8?
````sql
WITH CTE AS (
SELECT 
    *
FROM
    movie m
        JOIN
    ratings r ON r.movie_id = m.id
    WHERE date_published  BETWEEN '2018-04-01' AND '2019-04-01')
SELECT count(id) FROM cte
WHERE median_rating =8 ;
````
**Answer:**

![image](https://user-images.githubusercontent.com/95348482/231713417-66c43c76-e964-4274-970a-467d1944dab2.png)


### Q17. Do German movies get more votes than Italian movies? 
````sql
SELECT 
    country, SUM(total_votes) AS total_votes
FROM
    movie AS m
         JOIN
    ratings AS r ON m.id = r.movie_id
WHERE
    LOWER(country) = 'germany'
        OR LOWER(country) = 'italy'
GROUP BY country;
````

**Answer:**

![image](https://user-images.githubusercontent.com/95348482/231718691-c24298df-de88-4f3f-98ec-c47b3dd4e204.png)

### Q18. Which columns in the names table have null values??
````sql
SELECT
(SELECT COUNT(*) FROM names WHERE id is null) AS id,
(SELECT COUNT(*) FROM names WHERE `name` is null) AS name,
(SELECT COUNT(*) FROM names WHERE height is null) AS height,
(SELECT COUNT(*) FROM names WHERE date_of_birth is null) AS date_of_birth,
(SELECT COUNT(*) FROM names WHERE known_for_movies is null) AS known_for_movies;
````
**Answer:**

![image](https://user-images.githubusercontent.com/95348482/231719421-658ef686-f564-4e9c-91df-9ccaca6fc202.png)

### Q19. Who are the top five actors whose movies have a median rating >= 8?
````sql
SELECT 
    n.name AS actor_name, COUNT(movie_id) AS movie_count
FROM
    role_mapping AS rm
         JOIN
    movie AS m ON m.id = rm.movie_id
         JOIN
    ratings AS r USING (movie_id)
         JOIN
    names AS n ON n.id = rm.name_id
WHERE
    r.median_rating >= 8
        AND category = 'actor'
GROUP BY actor_name
ORDER BY movie_count DESC
LIMIT 5;
````
**Answer:**

![image](https://user-images.githubusercontent.com/95348482/231720113-a05d3679-d1c1-4d91-995e-605ff8ba823a.png)

### Q20. Which are the top three production houses based on the number of votes received by their movies?
````sql
SELECT 
    production_company, SUM(total_votes)
FROM
    movie m
        JOIN
    ratings r ON r.movie_id = m.id
GROUP BY production_company
ORDER BY SUM(total_votes) DESC
LIMIT 3;
````
**Answer:**

![image](https://user-images.githubusercontent.com/95348482/231720417-b62bcdac-741e-46b0-9ce9-97948870dd7c.png)

### Q21. Select thriller movies as per avg rating and classify them in the following category: 
			Rating > 8: Superhit movies
			Rating between 7 and 8: Hit movies
			Rating between 5 and 7: One-time-watch movies
			Rating < 5: Flop movies
````sql
with thriller_movies as (
    select  
       distinct title, 
       avg_rating
    from movie as mov inner join ratings as rat
         on mov.id = rat.movie_id 
         inner join genre as gen on gen.movie_id = mov.id
	where genre like 'THRILLER')
select *, 
       case 
         when avg_rating > 8 then 'superhit movies'
         when avg_rating between 7 and 8  then 'Hit movies'
         when avg_rating between 5 and 7 then 'one-time-watch movies'
         else 'Flop movies'
		end as avg_rating_category
from thriller_movies
LIMIT 10;
````
**Answer:**

![image](https://user-images.githubusercontent.com/95348482/231720924-cae45495-cf23-42a3-a7da-f1e992495a54.png)

### Q22. What is the genre-wise running total and moving average of the average movie duration? 
````sql
SELECT genre,
       ROUND(AVG(duration),2) AS avg_duration,
       SUM(ROUND(AVG(duration),2)) OVER(ORDER BY genre ROWS UNBOUNDED PRECEDING) AS running_total_duration,
       ROUND(AVG(AVG(duration)) OVER(ORDER BY genre ROWS 10 PRECEDING),2) AS moving_avg_duration
FROM movie AS m 
JOIN genre AS g 
ON m.id= g.movie_id
GROUP BY genre
ORDER BY genre;
````
**Answer:**

![image](https://user-images.githubusercontent.com/95348482/231721278-4b9e104f-07c2-4180-909c-24d4bb360cbe.png)
