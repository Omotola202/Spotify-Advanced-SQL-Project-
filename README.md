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

SELECT channel, COUNT(channel) AS num  --checks number of channels
FROM spotify
GROUP BY 1
ORDER BY num DESC
```

### 2. Querying the Data

### 1a. Insert a new record for a track with custom values into the dataset 
```sql
INSERT INTO spotify (Artist, Track, Album,	Album_type,
Danceability, Energy, Loudness, Speechiness, Acousticness, 
Instrumentalness, Liveness, Valence, Tempo, Duration_min, Title, Channel, Views, 
Likes, Comments, Licensed, official_video, Stream,	Energy_Liveness,	most_played_on
)

VALUES ('Gorillaz',	'New Gold (feat. Tame Impala and Bootie Brown)', 'New Gold (feat. Tame Impala and Bootie Brown)',	'single',	0.695,	0.923,	
-3.93,	0.0522,	0.0425,	0.0469,	0.116,	0.551,	108.014, 3.585833333, 'Gorillaz - New Gold ft. Tame Impala & Bootie Brown (Official Visualiser)',	
'Gorillaz',	8435055,	282142,	7399,	'TRUE',	'TRUE',	63063467, 7.956896552,	'Spotify'
)
```

### 1b. Identifying duplicates
```sql
SELECT track, artist, COUNT(*) AS occurence
FROM spotify
GROUP BY 1,2
HAVING  COUNT(*) > 1
ORDER BY 2
```

### 1c. Removing duplicates
```sql
WITH Row_numb AS
(SELECT *, ROW_NUMBER() OVER(PARTITION BY Track, artist ORDER BY track DESC) AS row_num
FROM spotify
ORDER BY row_num DESC)

DELETE FROM Row_numb 
WHERE row_num > 1
```


### 1d. Identifying outliers in views
```sql
SELECT AVG(views) + 3 * STDDEV(views) 
FROM spotify
 

WITH Stats AS (
    SELECT 
        AVG(views) AS mean_views,
        STDDEV(views) AS stddev_views
    FROM spotify
)
SELECT *
FROM spotify, Stats
WHERE views > (mean_views + 3 * stddev_views)
   OR views < (mean_views - 3 * stddev_views);
```


### 1e. Retrieve the names of all tracks that have more than 1 billion streams.
```sql
SELECT track
FROM spotify
WHERE stream > 1000000000
```


### 2. List all albums along with their respective artists.
```sql
SELECT DISTINCT artist, album
FROM spotify
ORDER BY 1

SELECT artist, COUNT(album)
FROM spotify
GROUP BY 1
ORDER BY 2 DESC
```


### 3. Get the total number of comments for tracks where `licensed = TRUE`.
```sql
SELECT SUM(comments) AS total_comments
FROM spotify
WHERE licensed = TRUE
```


### 4. Find all tracks that belong to the album type `single`.
```sql
SELECT track
FROM spotify
WHERE album_type = 'single'
```


### 5. Count the total number of tracks by each artist.
```sql
SELECT artist, COUNT(track) AS tracks
FROM spotify
GROUP BY 1
ORDER BY 2 DESC
```


### 6. Calculate the average danceability of tracks in each album.
```sql
SELECT album, AVG(danceability) AS avg_danceability
FROM spotify
GROUP BY 1
ORDER BY 2 DESC
```


### 7. Find the top 5 tracks with the highest energy values.
```sql
SELECT track, MAX(energy)
FROM spotify
GROUP BY 1
ORDER BY 2 DESC
LIMIT 5
```


### 8.List all tracks along with their views and likes where `official_video = TRUE`
```sql
SELECT track, SUM(views), SUM(likes)
FROM spotify
WHERE official_video = TRUE
GROUP BY 1
ORDER BY 2 DESC
```


### 9. For each album, calculate the total views of all associated tracks.

SELECT album, track, SUM(views)
FROM spotify
GROUP BY 1,2
ORDER BY 3 DESC



### 10. Retrieve the track names that have been streamed on Spotify more than YouTube
WITH T1 AS 
(SELECT track,  
	COALESCE(SUM(CASE WHEN most_played_on = 'Youtube' THEN stream END), 0) AS stream_on_youtube,
	COALESCE(SUM(CASE WHEN most_played_on = 'Spotify' THEN stream END), 0) AS stream_on_spotify
FROM spotify
GROUP BY 1)

SELECT *
FROM T1
WHERE stream_on_spotify > stream_on_youtube
	AND stream_on_youtube != 0



### 11. Find the top 3 most-viewed tracks for each artist using window functions.
WITH T2 AS 
(SELECT artist, track, views, DENSE_RANK() OVER(PARTITION BY artist ORDER BY views DESC) AS view_rank
FROM spotify)

SELECT *
FROM T2
WHERE view_rank <= 3



### 12. Write a query to find tracks where the liveness score is above the average.

SELECT track
FROM spotify
WHERE liveness > (SELECT AVG(liveness)
				  FROM spotify)


### 13.Use a `WITH` clause to calculate the difference between the highest and lowest energy values for tracks in each album.

WITH T3 AS
(SELECT  album, Min(energy) AS min_energy, MAX(energy) AS max_energy
FROM spotify
GROUP BY 1)

SELECT album, max_energy -  min_energy
FROM T3
ORDER BY 2 DESC



### 14. Find tracks where the energy-to-liveness ratio is greater than 1.2.

SELECT track, (energy/liveness) AS energy_to_liveness_ratio 
FROM spotify
WHERE (energy/liveness) > 1.2



### 15. Calculate the cumulative sum of likes for tracks ordered by the number of views, using window functions.

SELECT Artist, Track, Views, Likes, 
       SUM(Likes) OVER (ORDER BY Views ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) AS CumulativeLikes
FROM spotify;

### 16. Retrieve all tracks from the album 'Plastic Beach' with more than 1 million views.

SELECT track
FROM spotify
WHERE album = 'Plastic Beach' AND views > 1000000

### 17. Find the total number of streams and the average number of likes for all tracks by the artist 'Gorillaz'.

SELECT track, SUM(stream) AS total_stream, AVG(likes) AS avg_likes
FROM spotify
WHERE artist = 'Gorillaz'
GROUP BY 1
ORDER BY 2, 3 DESC

### 18. Extract the first word of each track title and count how many times each word appears in the dataset.

SELECT track, LEFT(track, 1)
FROM spotify

### 19. For each album, rank the tracks based on their number of streams, showing the album name, track title, and rank.

SELECT album, track, stream, DENSE_RANK() OVER(PARTITION BY album ORDER BY stream DESC) AS stream_rank
FROM spotify

### 20. Update all tracks where 'Licensed' is 'TRUE' but 'official_video' is 'FALSE' to set 'official_video' as 'TRUE'.

UPDATE spotify
SET official_video = TRUE
WHERE licensed = TRUE AND official_video = FALSE

### 21. Find the artist with the highest average loudness across all their tracks.

SELECT artist, AVG(loudness) AS avg_loudness
FROM spotify
GROUP BY artist
ORDER BY avg_loudness DESC
LIMIT 1;

--Using window fxn

WITH T4 AS 
(SELECT artist, track, loudness, AVG(loudness) OVER(PARTITION BY artist) AS avg_loudness
FROM spotify)
 
SELECT artist, MAX(avg_loudness)
FROM T4
GROUP BY 1
ORDER BY 2 DESC
LIMIT 1

### 22. Identify and list tracks that have null or missing values in the 'EnergyLiveness' column.

SELECT track
FROM spotify
WHERE energy IS NULL AND liveness IS NULL

 ### 23. Retrieve all tracks with a danceability score greater than 0.7 and a loudness below -5.0.
 
SELECT track
FROM spotify
WHERE danceability > 0.7 AND loudness < -5.0

### 24. Find the total number of views and the average number of likes for each album.

SELECT album, SUM(views) AS total_views, AVG(likes) AS avg_likes
FROM spotify
GROUP BY 1

### 25  What is the maximum and minimum energy value among all tracks by the artist 'Gorillaz'?

SELECT MAX(energy), MIN(energy) 
FROM spotify 
WHERE artist = 'Gorillaz'

### 26. Find the tracks whose title contains the word 'Official'.

SELECT track 
FROM spotify 
WHERE track ilike '%Official%'

### 27 Extract the first 10 characters of each track’s title and display them along with the artist.
SELECT LEFT(track, 10) AS ten_xter, artist 
FROM spotify

### 28. Replace all occurrences of the word 'Video' in the 'Title' column with 'Clip'.

SELECT title, REPLACE(title, 'Video', 'Clip') AS replaced_words 
FROM spotify

### 29.Find the top 5 tracks (in terms of streams) for each artist.
WITH T1 AS 
(SELECT artist, track, stream, DENSE_RANK() OVER(PARTITION BY artist ORDER BY stream DESC) AS streaming_rank 
	FROM spotify ) 

SELECT artist, track, streaming_rank FROM T1 WHERE streaming_rank <= 5

### 30. Retrieve the average loudness for tracks that have over 10 million views and are classified as official videos.
SELECT track, AVG(loudness) AS avg_loudness 
FROM spotify WHERE views > 10000000 AND official_video = TRUE 
GROUP BY 1 
ORDER BY 2 DESC


### 31. Rank all tracks by their number of streams within each album.
SELECT album, track, stream, DENSE_RANK() OVER(PARTITION BY album ORDER BY stream DESC) AS streaming_rank 
FROM spotify

### 32. For each artist, calculate the cumulative total streams across their tracks, ordered by track title.
SELECT artist, track, stream, SUM(stream) OVER(PARTITION BY artist )
FROM spotify
ORDER BY track

### 33. Display the top 3 most viewed tracks for each album, including album name and track title.
SELECT album, track, views, DENSE_RANK() OVER(PARTITION BY album ORDER BY views DESC) AS view_rank
FROM spotify

### 34. Delete all records where the 'Stream' count is less than 1000.

DELETE FROM spotify
WHERE stream < 1000

### 35. Find the album with the highest total number of streams.
```sql
SELECT album, MAX(stream) AS highest_stream
FROM spotify
GROUP By 1
ORDER BY 2 DESC
LIMIT 1
```
### 36. For each artist, find the track that has the most likes.
```sql
WITH likes_rank AS
(SELECT artist, track, likes, DENSE_RANK() OVER(PARTITION BY artist ORDER BY LIKES DESC) AS rank_likes
FROM spotify)

SELECT *
FROM likes_rank
WHERE rank_likes = 1
```

### 37. Count how many tracks have a 'Liveness' value greater than 0.5, grouped by whether they are licensed or not.
```sql
SELECT Licensed, COUNT(*) AS track_count
FROM spotify
WHERE Liveness > 0.5
GROUP BY Licensed;
```
### 38. Calculate the average loudness for official videos vs. non-official videos.
```sql
SELECT official_video, AVG(loudness) AS avg_loudness
FROM spotify
GROUP BY 1
```
### 39. Identify all records where the 'EnergyLiveness' value is null, and update them to the average value of the column.
```sql
UPDATE spotify
SET Energy_Liveness = (SELECT AVG(Energy_Liveness)
                       FROM spotify
                       WHERE Energy_Liveness IS NOT NULL)
WHERE Energy_Liveness IS NULL;
```

### 40. Count how many tracks have missing or null values in any of the numeric columns.
```sql
SELECT COUNT(*) AS tracks_with_nulls
FROM spotify
WHERE Stream IS NULL
   OR Likes IS NULL
   OR Views IS NULL
   OR Liveness IS NULL
   OR Energy_Liveness IS NULL;
```


### 41. Retrieve tracks that have more than 5000 comments and are streamed more on 'Youtube' than 'Spotify'.
```sql
WITH T1 AS 
(SELECT track, comments,
	COALESCE(SUM(CASE WHEN most_played_on = 'Youtube' THEN stream END), 0) AS stream_on_youtube,
	COALESCE(SUM(CASE WHEN most_played_on = 'Spotify' THEN stream END), 0) AS stream_on_spotify
FROM spotify
GROUP BY 1,2)

SELECT *
FROM T1
WHERE stream_on_spotify > stream_on_youtube
	AND stream_on_youtube != 0 
	AND comments > 5000
```

### 42. Group the tracks by 'most_played_on' platform and show the platform with the highest average number of likes.
```sql
SELECT track, most_played_on, AVG(likes)
FROM spotify
GROUP BY 1,2
ORDER BY 3 DESC
```

### 43. Create an index on the 'Artist' column to optimize queries filtering by artist.
```sql
CREATE INDEX idx_artist 
ON spotify (artist);
```
