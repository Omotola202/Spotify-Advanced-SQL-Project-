# Spotify-Advanced-SQL-Project-
Project Category: Advanced
SQL Flavour: PostgreSQL
[Click Here to get Dataset](https://www.kaggle.com/datasets/sanjanchaudhari/spotify-dataset)

![Spotify Logo](https://github.com/Omotola202/Spotify-Advanced-SQL-Project-/blob/main/spotify_logo.jpg)

## Overview
This project involves analyzing a Spotify dataset with various attributes about tracks, albums, and artists using **SQL**. It covers an end-to-end process of normalizing a denormalized dataset, performing SQL queries of varying complexity (easy, medium, and advanced), and optimizing query performance. The primary goals of the project are to practice advanced SQL skills and generate valuable insights from the dataset.

```sql
-- create table
DROP TABLE IF EXISTS spotify;
CREATE TABLE spotify (
    artist VARCHAR(255),
    track VARCHAR(255),
    album VARCHAR(255),
    album_type VARCHAR(50),
    danceability FLOAT,
    energy FLOAT,
    loudness FLOAT,
    speechiness FLOAT,
    acousticness FLOAT,
    instrumentalness FLOAT,
    liveness FLOAT,
    valence FLOAT,
    tempo FLOAT,
    duration_min FLOAT,
    title VARCHAR(255),
    channel VARCHAR(255),
    views FLOAT,
    likes BIGINT,
    comments BIGINT,
    licensed BOOLEAN,
    official_video BOOLEAN,
    stream BIGINT,
    energy_liveness FLOAT,
    most_played_on VARCHAR(50)
);
```
## Project Steps

### 1. Data Exploration
```sql

SELECT *
FROM spotify
LIMIT 100 

SELECT COUNT(*) --Count of rows in dataset
FROM spotify

SELECT COUNT (DISTINCT album) -- Checks for unique album
FROM spotify

SELECT COUNT (DISTINCT artist)  -- Checks for unique artist
FROM spotify

SELECT DISTINCT album_type  -- Checks for unique album_types
FROM spotify

SELECT MAX(duration_min)  --Checks for maximum duration of song tracks
FROM spotify

SELECT Min(duration_min)  --Checks for minimum duration of song tracks
FROM spotify

-- Deleting tracks with 0 duration

SELECT most_played_on, COUNT(most_played_on) AS num
FROM spotify
GROUP BY 1

SELECT *
FROM spotify
WHERE duration_min = 0

DELETE FROM spotify
WHERE duration_min = 0


SELECT COUNT (DISTINCT channel) -- Checks for unique channel
FROM spotify

SELECT channel, COUNT(channel) AS num
FROM spotify
GROUP BY 1
ORDER BY num DESC
```
