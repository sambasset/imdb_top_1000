# imdb_top_1000

This dataset is from Kaggle [LINK](https://www.kaggle.com/datasets/harshitshankhdhar/imdb-dataset-of-top-1000-movies-and-tv-shows)

## **PRIMARY GOAL**
Discover different information from the database through SQL queries (BigQuery)
1) Who are the top 5 Actors that appear on the list?
  - What movies did the top actor star in?
2) Who are the top 5 directors that appear on the list?
  - What movies did the top director direct?
3) What are the top Genre's?
4) What are the top 10 highest grossing movies?
5) Who are the top 5 highest grossing actors?

## **1) Who are the top 5 Actors that appear on the list?**

Actors are in 4 different columns (Star1, Star2, Star3, Star4). In order to get a proper count for the actors I will have to union the columns.

```
SELECT s.stars, COUNT(s.stars) as totalnumber
   FROM (
    SELECT Star1 as stars
      FROM `clean-result-365422.movie_data.imdb_top_1000`
        UNION ALL
    SELECT Star2
      FROM `clean-result-365422.movie_data.imdb_top_1000`
        UNION ALL
    SELECT Star3
      FROM `clean-result-365422.movie_data.imdb_top_1000`
        UNION ALL
    SELECT Star4
      FROM `clean-result-365422.movie_data.imdb_top_1000`
    ) AS s
      GROUP BY s.stars
        ORDER BY totalnumber DESC
```
This returned 2706 lines (actors). I could manually count to get the breakpoint to create a query that will return only the top 5, however, here is a query that will return only the numbers so I can look at the 5th line to find the breakpoint of # of films for the top 5
```
SELECT DISTINCT totalnumber
  FROM
  (SELECT s.stars, COUNT(s.stars) as totalnumber
    FROM (
      SELECT Star1 as stars
        FROM `clean-result-365422.movie_data.imdb_top_1000`
          UNION ALL
      SELECT Star2
        FROM `clean-result-365422.movie_data.imdb_top_1000`
          UNION ALL
      SELECT Star3
        FROM `clean-result-365422.movie_data.imdb_top_1000`
          UNION ALL
      SELECT Star4
        FROM `clean-result-365422.movie_data.imdb_top_1000`
      ) AS s
        GROUP BY s.stars
          ORDER BY totalnumber DESC
            LIMIT 150)
 ```
 This returned a breakpoint of 11 movies.
 
 ![alt text](https://github.com/sambasset/imdb_top_1000/blob/main/imdb_project_1.png?raw=true)
 
Next I create a query that will return all the names of actors whos totalnumber is >= 11

```
SELECT *
  FROM
    (
    SELECT s.stars, COUNT(s.stars) as totalnumber
      FROM (
        SELECT Star1 as stars
          FROM `clean-result-365422.movie_data.imdb_top_1000`
            UNION ALL
        SELECT Star2
          FROM `clean-result-365422.movie_data.imdb_top_1000`
            UNION ALL
        SELECT Star3
          FROM `clean-result-365422.movie_data.imdb_top_1000`
            UNION ALL
        SELECT Star4
          FROM `clean-result-365422.movie_data.imdb_top_1000`
        ) AS s
          GROUP BY s.stars
            ORDER BY totalnumber DESC
    ) WHERE totalnumber >= 11
```
This returns the following ...

 ![alt text](https://github.com/sambasset/imdb_top_1000/blob/main/imdb_project_2.png?raw=true)
 
Then we will see what movies Robert De Niro has starred in from newest to oldest.

```
SELECT Series_Title, Released_Year
  FROM `clean-result-365422.movie_data.imdb_top_1000`
    WHERE Star1 = 'Robert De Niro' 
      OR Star2 = 'Robert De Niro'
      OR Star3 = 'Robert De Niro'
      OR Star4 = 'Robert De Niro'
        ORDER BY Released_Year DESC
```

This returns the following ...

 ![alt text](https://github.com/sambasset/imdb_top_1000/blob/main/imdb_project_3.png?raw=true)
 
## **1) Who are the top 5 Directors that appear on the list?** 

Works very similar to the actors but without the need to union the columns.

```
SELECT Director, COUNT(Director) AS totalnumber
  FROM `clean-result-365422.movie_data.imdb_top_1000`
    GROUP BY Director
      ORDER BY totalnumber DESC
```

This returns 548 lines. We will use the same distinct count to return our threshold for the top 5.

```
SELECT DISTINCT totalnumber
 FROM (
   SELECT Director, COUNT(Director) AS totalnumber
    FROM `clean-result-365422.movie_data.imdb_top_1000`
      GROUP BY Director
 ) ORDER BY totalnumber DESC
 ```
 
 ![alt text](https://github.com/sambasset/imdb_top_1000/blob/main/imdb_project_4.png?raw=true)
 
 With our threshold being 9 I write the query to return everything >= 9
 
 ```
 SELECT *  
  FROM (
    SELECT Director, COUNT(Director) AS totalnumber
      FROM `clean-result-365422.movie_data.imdb_top_1000`
        GROUP BY Director
          ORDER BY totalnumber DESC
        ) WHERE totalnumber >= 9
```

This returns the following ...

 ![alt text](https://github.com/sambasset/imdb_top_1000/blob/main/imdb_project_5.png?raw=true)
 
Now I will see what movies Alfred Hitchcock directed in order from oldest to newest using the following query.

```
SELECT Series_Title, Released_Year
  FROM `clean-result-365422.movie_data.imdb_top_1000`
    WHERE Director = 'Alfred Hitchcock'
      ORDER BY Released_Year DESC
```

This returns the following ...

![alt text](https://github.com/sambasset/imdb_top_1000/blob/main/imdb_project_6.png?raw=true)

## **3) What are the top Genres**

This was more difficult because many movies had multiple Genre's (ie, Western, Drama, Fantasy) that when SPLIT were not put into a different row so I couldn't then use a COUNT to get an accurate representation of how many movies were that Genre type. 

My workaround for this was creating a long and repeptitive query that pulled when the word was used using REGEXP_EXTRACT and then putting a count on that.

```
SELECT 
  COUNT(genre_cnt) as Biography,
  COUNT (genre_cnt2) as Drama,
  COUNT (genre_cnt3) as Comedy,
  COUNT (genre_cnt4) as Crime,
  COUNT (genre_cnt5) as Adventure,
  COUNT (genre_cnt6) as Horror,
  COUNT (genre_cnt7) as Animation,
  COUNT (genre_cnt8) as Fantasy,
  COUNT (genre_cnt9) as Western,
  COUNT (genre_cnt10) as Thriller,
  COUNT (genre_cnt11) as Mystery,
  FROM
  (
    SELECT 
      REGEXP_EXTRACT(Genre,"Biography") As genre_cnt,
      REGEXP_EXTRACT(Genre,"Drama") As genre_cnt2,
      REGEXP_EXTRACT(Genre,"Comedy") As genre_cnt3,
      REGEXP_EXTRACT(Genre,"Crime") As genre_cnt4,
      REGEXP_EXTRACT(Genre,"Adventure") As genre_cnt5,
      REGEXP_EXTRACT(Genre,"Horror") As genre_cnt6,
      REGEXP_EXTRACT(Genre,"Animation") As genre_cnt7,
      REGEXP_EXTRACT(Genre,"Fantasy") As genre_cnt8,
      REGEXP_EXTRACT(Genre,"Western") As genre_cnt9,
      REGEXP_EXTRACT(Genre,"Thriller") As genre_cnt10,
      REGEXP_EXTRACT(Genre,"Mystery") As genre_cnt11
  FROM `clean-result-365422.movie_data.imdb_top_1000`
  )
  ```
  
  This returned the following ...
  
  ![alt text](https://github.com/sambasset/imdb_top_1000/blob/main/imdb_project_7.png?raw=true)
  
  Not the cleanest or most efficient way to get the results I wanted but I got the result. Will update later if I can come across a simpler, more efficient solution.

## **4) What are the top 10 highest grossing movies?**

Fairly simple query top return the top 10 highest grossing movies. Added a $ sign through a concat.

```
SELECT 
  Series_Title, CONCAT("$",Gross) AS dollar
    FROM `clean-result-365422.movie_data.imdb_top_1000`
        ORDER BY Gross DESC
          LIMIT 10
```

This returned the following ...

![imdb_project_8](https://user-images.githubusercontent.com/119076539/207102016-06ee8386-d597-4a03-a551-bd4a975b77ee.png)

## **5) Who are the top 5 highest grossing actors?**

For this I already had the query for unioning the stars so it was then just creating a SUM for the Gross then ordering it from highest to lowest and limiting the return to 10.

```
SELECT s.stars, SUM(Gross) as ttl_gross
  FROM
    (SELECT Star1 as stars
        FROM `clean-result-365422.movie_data.imdb_top_1000`
          UNION ALL
      SELECT Star2
        FROM `clean-result-365422.movie_data.imdb_top_1000`
          UNION ALL
      SELECT Star3
        FROM `clean-result-365422.movie_data.imdb_top_1000`
          UNION ALL
      SELECT Star4
        FROM `clean-result-365422.movie_data.imdb_top_1000`
      ) AS s, `clean-result-365422.movie_data.imdb_top_1000`
      GROUP BY s.stars
        ORDER BY SUM(Gross) DESC
        LIMIT 10
```

This returned the following ...

![imdb_project_9](https://user-images.githubusercontent.com/119076539/207106862-dc72bc11-2a09-4e79-a48f-e3f0c56249c8.png)

That concludes my current discoveries in the imdb top 1000 dataset. Hopefully as I continue my journey in Data Analytics I can return to this to create better querys and dive deeper into the information with potential insights.

Thank you for reading!

Sam
