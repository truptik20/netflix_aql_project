# Netflix Movies and TV Shows Data Analysis using SQL

![Netflix Logo](https://github.com/truptik20/netflix_aql_project/blob/main/netflix_logo.jpg)


## Overview
This project presents a comprehensive **SQL-based analysis of Netflix Movies and TV Shows data**. The primary goal is to explore the dataset, uncover meaningful patterns, and answer business-related questions about Netflix's content library.

Using SQL queries, the project analyzes content distribution, ratings, release trends, countries, durations, genres, and keywords to generate useful insights from the dataset.

## Objectives

- Analyze the distribution of Movies vs. TV Shows on Netflix.
-  Identify the most common ratings for Movies and TV Shows.
- Examine content trends based on release year and addition date.
- Analyze Netflix content across different **countries**.
- Explore the duration of Movies and TV Shows.
- Identify the longest-running and longest-duration content.
- Categorize and filter content using genres, descriptions, and keywords.
- Solve practical business questions using SQL queries.
- Generate meaningful insights about Netflix's overall content library.

## Dataset

The data for this project is sourced from the Kaggle Dataset:

- **Dataset Link:** [Netflix Dataset](https://www.kaggle.com/datasets/shivamb/netflix-shows?resource=download)

## Schema

```sql
DROP TABLE IF EXISTS netflix;
CREATE TABLE netflix
(
	show_id VARCHAR(7),
	type VARCHAR(10),
	title VARCHAR(150),
	director VARCHAR(208),
	castS VARCHAR(1000),
	country VARCHAR(150),
	date_added VARCHAR(50),
	release_year INT,
	rating VARCHAR(10),
	duration VARCHAR(15),
	listed_in VARCHAR(100),
	description VARCHAR(250)
);
```

## Business Problems and Solutions

### 1. Count the number of Movies vs TV shows

```sql
SELECT type, COUNT (*) as total_content
FROM netflix
GROUP BY type;
```

**Objective:** Compare the total number of Movies and TV Shows available on Netflix.

### 2. Find the most common rating for movies and TV shows.

```sql
SELECT type, rating, COUNT (*) FROM netflix
GROUP BY type, rating
ORDER BY type, COUNT(*) DESC;
-- LIMIT 6;

SELECT 
	type, rating, count (*), RANK() OVER(PARTITION BY type ORDER BY COUNT(*) DESC) as ranking
FROM netflix
GROUP BY type, rating
ORDER BY type, COUNT(*) DESC;

-- Sub-Query
SELECT type, rating 
FROM
(SELECT 
	type, rating, count (*), RANK() OVER(PARTITION BY type ORDER BY COUNT(*) DESC) as ranking
FROM netflix
GROUP BY type, rating
ORDER BY type, COUNT(*) DESC) as t1
WHERE ranking = 1;
```

### 3. List all movies released in a specific year (e.g., 2020).

```sql
SELECT type, title, release_year
FROM netflix
WHERE type = 'Movie' AND release_year = 2020;
```

### 4. Find the top 5 countries with the most content on netflix.

```sql
SELECT UNNEST(STRING_TO_ARRAY(country, ', ' )) as new_country , COUNT(show_id)
FROM netflix
GROUP BY 1
ORDER BY COUNT(*) DESC
LIMIT 5;
```

### 5. Identify the logest movie.

```sql
SELECT * FROM netflix
WHERE 
	type = 'Movie' AND duration = (SELECT MAX(duration) FROM netflix);
```

### 6. Find content added in the last 5 years.

```sql
SELECT *
FROM netflix
WHERE TO_DATE(date_added, 'Month DD, YYYY') >= CURRENT_DATE - INTERVAL ' 5 YEARS';
```

### 7. Find all the movies/tv shows by director 'Rajiv Chilaka'.

```sql
SELECT *
FROM netflix
WHERE director = 'Rajiv Chilaka';

SELECT *
FROM netflix
WHERE director LIKE '%Rajiv Chilaka%';
```

### 8. List all tv shows with more than 5 seasons

```sql
SELECT * FROM netflix
WHERE type = 'TV Show' AND SPLIT_PART(duration, ' ', 1)::numeric > 5;
```

### 9. Count the number of content items in each genre.

```sql
SELECT COUNT(show_id), UNNEST(STRING_TO_ARRAY(listed_in, ',')) as genre
FROM netflix
GROUP BY 2;
```

### 10. Find each year and the average numbers of content release by India on netflix. Return top 5 year with highest avg Content release.

```sql
SELECT 
	EXTRACT(YEAR FROM TO_DATE(date_added, 'Month DD, YYYY')), 
	COUNT(*), 
	ROUND(COUNT(*)::numeric/(SELECT COUNT(*) FROM netflix WHERE country = 'India')::numeric * 100, 2) as avg_content_per_year
FROM netflix
WHERE country = 'India'
GROUP BY 1;
```

### 11. List all movies that are documentaries.

```sql
SELECT * FROM netflix
WHERE type = 'Movie' AND listed_in LIKE '%Documentaries%';
```

### 12. Find all content without a director.

```sql
SELECT * FROM netflix
WHERE director IS NULL;
```

### 13. Find how many movies actor 'Salman Khan' appeared in last 10 years.

```sql
SELECT *
FROM netflix
WHERE release_year > EXTRACT(YEAR FROM CURRENT_DATE) - 10 AND casts LIKE '%Salman Khan%';
```

### 14. Find the top 10 actors who have appeared in the highest number of movies produced in India.

```sql
SELECT UNNEST(STRING_TO_ARRAY(casts, ',')) as actors, COUNT(*) as total_content
FROM netflix
WHERE country LIKE '%India%'
GROUP BY 1
ORDER BY 2 DESC
LIMIT 10;
```

### 15. Categorize the content based on the presence of the keyword 'kill' and 'violence' in the decription field. Label content containing these keywords as 'Bad' and all other content as 'Good'. Count how many items fall into each category.

```sql
SELECT * FROM netflix
WHERE description ILIKE '*kill%' OR description ILIKE '%violence%';


with new_table
AS
(
SELECT *,
	CASE
	WHEN description ILIKE '*kill%' OR description ILIKE '%violence%' THEN 'Bad'
		ELSE 'Good'
	END category
FROM netflix
)
SELECT 
	category,
	COUNT(*) as total_content
FROM new_table
GROUP BY 1;
```
