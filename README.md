# imdb_top_1000

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
This returned 2706 lines (actors). I could manually count to get the breakpoint to create a query that will return only the top 5 however here is a query that will return only the numbers so I can look at the 5th line to find the breakpoint of # of films for the top 5
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

This returns 548 lines. We will use the same disctinct count to return our threshold for the top 5.

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

