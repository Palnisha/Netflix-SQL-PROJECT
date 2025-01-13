  # Netflix TV SHOW MOVIES -SQL-PROJECT
![Netflix logo](https://github.com/Palnisha/Netflix-SQL-PROJECT/blob/main/Netflix.png)

# objective
This project involves a comprehensive analysis of Netflix's movies and TV shows data using SQL. The goal is to extract valuable insights and answer various business questions based on the dataset. The following README provides a detailed account of the project's objectives, business problems, solutions, findings, and conclusions.

Objectives
Analyze the distribution of content types (movies vs TV shows).
Identify the most common ratings for movies and TV shows.
List and analyze content based on release years, countries, and durations.
Explore and categorize content based on specific criteria and keywords.
Dataset
The data for this project is sourced from the Kaggle dataset:

Dataset Link: Movies Dataset
Schema
create database netflix1;
use netflix1;
select * from netflix_titles;
--  count toal content in netflix_titles--
select count(*) as total_content
from netflix_titles;
-- see the distinct type
select distinct type
from netflix_titles;
--  14business problems
-- Quesstion 1 : count the number of movies vs tv show
select type,
count(*) as tota_content
from netflix_titles
group by type;
-- Question 2 : find the most comman rating for movies nd tv shows
select * from netflix_titles;
select type,
rating
from
(select type,
        rating,
        count(*)
        Rank() over (partition by type order by count(*) desc) as ranking 
        from netflix_titles
        group by 1,2
        ) as ti
        where ranking =1;
   -- in mysql rank doesnt support
   -- Question 3. list all movies released in a specific year(e.g.,2020)
   select type,
   title,
    release_year
   from netflix_titles
   where type = 'Movie'
       and
   release_year = '2020';
-- Question 4.  Identify longest movie
select * from netflix_titles;
select * 
from netflix_titles
where type = 'Movie'
and
duration = (select max(duration) from netflix_titles);
-- Question 5. find content added in LAST  5 YEARS
SELECT  * ,
    date_format(date_added,'%m-%d-%y')as formated_date
from  netflix_titles
    where date_added = current_date - interval 5 year;
-- 	Question 6 .  find all movie/tv show by director 'Rajib Chilaka'
select * from netflix_titles;
select *
from netflix_titles
where director like '%Rajiv Chilaka%';
-- Question 7. list all tv shows with more than 5 season
select * ,
split_part(duration,',',1)
 from netflix_titles
where  type = 'TV Show'
and
duration > 5 ;
-- Question 8.count the number of content item in each genre 
 
select listed_in,
count(*)
from  netflix_titles
group by listed_in;

-- Question 9.  find each year and average number of content release by india on netflix
---- return top 5 year with highest avg content release.
select * from netflix_titles;
SELECT  
    EXTRACT(YEAR FROM STR_TO_DATE(date_added, '%d-%b-%Y')) AS year,
    COUNT(*) AS total_count,
    ROUND((COUNT(*) / (SELECT COUNT(*) FROM netflix_titles WHERE country = 'India')) * 100,2) AS avg_content
FROM netflix_titles
WHERE country = 'India'
GROUP BY year;
--- Question 10. list all movies that are documentries.
select * from netflix_titles;
select * 
from netflix_titles
where  type = 'Movie'
and
listed_in like '%Documentaries%';
-- Question 11. find all content without director
select * from netflix_titles
where director is null;
-- Question 12. find how many movies actor 'salman khan'appeared in last10 yearsSELECT * 
 select * FROM netflix_titles
WHERE TRIM(cast) = 'Salman Khan'
and
release_year is not null
AND release_year > YEAR(CURDATE()) - 10;
-- Question 13. find all type content
select count(type)
 from netflix_titles; 
-- Question 14. find kills nd violenc is bad content all rest are good content then find out how many items falls in this category
with new_table
as
(
select *,
case
when description = 'skilled'or
description  ='violence' then 'bad_content'
else 'good_content'
end category
from netflix_titles 
)
select  category,
count(*) as total_content
from new_table
group by 1;

