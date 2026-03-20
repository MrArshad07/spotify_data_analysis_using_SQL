# Spotify Advanced SQL Project 
Project Category: Advanced
[Click Here to get Dataset](https://www.kaggle.com/datasets/sanjanchaudhari/spotify-dataset)

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
Before diving into SQL, it’s important to understand the dataset thoroughly. The dataset contains attributes such as:
- `Artist`: The performer of the track.
- `Track`: The name of the song.
- `Album`: The album to which the track belongs.
- `Album_type`: The type of album (e.g., single or album).
- Various metrics such as `danceability`, `energy`, `loudness`, `tempo`, and more.

### 4. Querying the Data
After the data is inserted, various SQL queries can be written to explore and analyze the data. Queries are categorized into **easy**, **medium**, and **advanced** levels to help progressively develop SQL proficiency.

#### Easy Queries
- Simple data retrieval, filtering, and basic aggregations.
  
#### Medium Queries
- More complex queries involving grouping, aggregation functions, and joins.
  
#### Advanced Queries
- Nested subqueries, window functions, CTEs, and performance optimization.

### 5. Query Optimization
In advanced stages, the focus shifts to improving query performance. Some optimization strategies include:
- **Indexing**: Adding indexes on frequently queried columns.
- **Query Execution Plan**: Using `EXPLAIN ANALYZE` to review and refine query performance.
  
---

## 15 Practice Questions

### Easy Level
1. Retrieve the names of all tracks that have more than 1 billion streams.
```sql
select track from spotify
  where
Stream > 1000000000;
```
2. List all albums along with their respective artists.
```sql
SELECT DISTINCT album, artist
FROM spotify;
```
3. Get the total number of comments for tracks where `licensed = TRUE`.
```sql
select sum(Comments) as total_comments
from spotify 
where Licensed='TRUE';
``` 
4. Find all tracks that belong to the album type `single`.
```sql
select track
from spotify
where Album='single';
```
5. Count the total number of tracks by each artist.
```sql
SELECT Artist, COUNT(Track) AS Total_tracks
FROM spotify
GROUP BY Artist
ORDER BY Total_tracks DESC;
```
### Medium Level
1. Calculate the average danceability of tracks in each album.
```sql
SELECT Album, AVG(Danceability) AS avg_danceability
FROM spotify
GROUP BY Album
ORDER BY avg_danceability DESC;
```
2. Find the top 5 tracks with the highest energy values.
```sql
SELECT track, MAX(energy)
FROM spotify
GROUP BY track
ORDER BY energy DESC
LIMIT 5;
```
3. List all tracks along with their views and likes where `official_video = TRUE
```sql
select track , Views , Likes
from spotify
where official_video='True';
```
4. For each album, calculate the total views of all associated tracks.
```sql
select distinct Album,track,
        sum(Views)as Total_viwes
from spotify
group by Album,track
order by Total_viwes DESC;
```
5. Retrieve the track names that have been streamed on Spotify more than YouTube.
```sql
SELECT 
    track,
    SUM(CASE WHEN most_playedon = 'Spotify' THEN stream ELSE 0 END) AS spotify_stream,
    SUM(CASE WHEN most_playedon = 'Youtube' THEN stream ELSE 0 END) AS youtube_stream,
    
    CASE 
        WHEN SUM(CASE WHEN most_playedon = 'Spotify' THEN stream ELSE 0 END) >
             SUM(CASE WHEN most_playedon = 'Youtube' THEN stream ELSE 0 END)
        THEN 'Spotify'
        
        WHEN SUM(CASE WHEN most_playedon = 'Spotify' THEN stream ELSE 0 END) <
             SUM(CASE WHEN most_playedon = 'Youtube' THEN stream ELSE 0 END)
        THEN 'Youtube'
        
        ELSE 'Equal'
    END AS most_played_platform

FROM spotify
GROUP BY track;
```
### Advanced Level
1. Find the top 3 most-viewed tracks for each artist using window functions.
```sql
SELECT *
FROM (
    SELECT 
        artist,
        track,
        SUM(views) AS total_views,
        DENSE_RANK() OVER (
            PARTITION BY artist 
            ORDER BY SUM(views) DESC
        ) AS rnk
    FROM spotify
    GROUP BY artist, track
) AS t
WHERE rnk <= 3
ORDER BY artist, rnk;
```
2. Write a query to find tracks where the liveness score is above the average.`
```sql
SELECT track, liveness
FROM spotify
WHERE liveness > (
    SELECT AVG(liveness)
    FROM spotify
);
```
3. **Use a `WITH` clause to calculate the difference between the highest and lowest energy values for tracks in each album.**
```sql
WITH cte
AS
(SELECT 
	album,
	MAX(energy) as highest_energy,
	MIN(energy) as lowest_energery
FROM spotify
GROUP BY 1
)
SELECT 
	album,
	highest_energy - lowest_energery as energy_diff
FROM cte
ORDER BY 2 DESC
```
   


