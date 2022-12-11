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
