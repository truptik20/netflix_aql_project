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

### 1. Count the number of Movies vs TV shows

### 1. Count the number of Movies vs TV shows

### 1. Count the number of Movies vs TV shows

### 1. Count the number of Movies vs TV shows

### 1. Count the number of Movies vs TV shows

### 1. Count the number of Movies vs TV shows

### 1. Count the number of Movies vs TV shows

### 1. Count the number of Movies vs TV shows
