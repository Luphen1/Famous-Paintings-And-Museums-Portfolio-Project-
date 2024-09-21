# Famous-Paintings-And-Museums-Portfolio-Project-


#### Table of Contents



---------------------
                        

-  [Project Overview](#Project_Overview)
  

-  [Data Source](#Data_Source)
  

-  [Tools](#Tools)
  

-  [Data Cleaning/Preparation](#Data_Cleaning/Preparation)
  

-  [Exploration Data Analysis](#Exploration_Data_Analysis)
  

-  [Data Analysis](#Data_Analysis)
  

-  [Results/Findings](#Results/Findings)
  

-  [Recommendations](#Recommendations)


### Project Overview

The project focuses on famous paintings, their artists, and an accompanying museum dataset. Of the eight datasets provided, I will focus on seven to uncover trends and patterns related to paintings and museums. The findings involve questions such as which paintings were not displayed in any museum, identifying museums with invalid city information, and analyzing the most popular painting styles. Advanced SQL techniques such as joins, window functions, subqueries, and CTEs will be used to explore these questions and derive meaningful insights from the data.



### Data Source

The primary datasets utilized for the project was downloaded from the provided link [https://www.kaggle.com/datasets/mexwell/famous-paintings]( https://www.kaggle.com/datasets/mexwell/famous-paintings)


### Tool


MICROSOFT SQL SERVER was utilized for importing and inspecting the dataset, check for duplicate records, and identifying null values to standardize the dataset for data quality assurance.


### Exploratory Data Analysis


EDA involved exploring key questions such as:


1.	Fetch all the paintings that were not displayed in any museums.
   
2. Were there any museums without paintings?
   
3.  How many paintings had an asking price higher than their regular price?
   
4.  Which canvas size costs the most?
Identify the museums with invalid city information in the given dataset.


5.  Identify the museums with invalid city information in the given dataset.

6.    Which artist lived the longest? Display the artist's name, nationality, style, age, and the number of paintings they had before they died.
  
7.    Fetch the top ten most famous painting subjects.

8.    Identify the museums that are open on both Sunday and Monday. Display the museum name, state, and country.

9.    How many museums were open during weekdays? Display the museum name, country, and city.
  
10.    Which museum had the highest number of the most popular painting styles?


11.   Identify the artists whose paintings were displayed in multiple countries.


12.   Display the country and the city with the highest number of museums. Output two separate columns to mention the country and city. If there are multiple values, separate them with commas.


13.   Which country had the highest number of paintings? Display the style categories of the paintings.


14.   Which artist had the highest number of portrait paintings outside the USA? Display the artist's name, number of paintings, and nationality.

    
15.   How many museums were open during the weekend? Display the museum name, country, and city.





### Data Cleaning/Preparation



In the initial data preparation, I performed the following tasks below:
1. Data loading and inspection.
2. Handling for missing values.
3. Checking for duplicate values.
4. Data preprocessing.

### Data Analysis


```

-- Inspecting and loading of datasets.
-- We had 421 records in table artist.
SELECT * FROM [Paintings].[dbo].[artist]

```

```
-- We had 200 records in table canvas_size.
SELECT * FROM [Paintings].[dbo].[canvas_size]

```

```

-- We had 57 records in table meseum.
SELECT * FROM [Paintings].[dbo].[museum]

```

```

-- We had 351 records in table museum_hours.
SELECT * FROM [Paintings].[dbo].[museum_hours]

```

```
-- We had 6771 records in table subject.
SELECT * FROM [Paintings].[dbo].[subject]

```

```
-- We had 14776 records in table work.
SELECT * FROM [Paintings].[dbo].[work]

```

```
-- We had 110,347 records in the table product size.
SELECT * FROM [Paintings].[dbo].[product_size]

```

```
-- Checked table artist for any duplicate records.
-- Artist table has no duplicates value found.
SELECT artist_id,first_name,last_name,
COUNT(*) AS count
FROM [Paintings].[dbo].[artist]
GROUP BY artist_id,first_name,last_name
HAVING COUNT(*) > 1
ORDER BY cunt DESC

SELECT *, 
ROW_NUMBER() OVER(PARTITION BY artist_id,full_name ORDER BY artist_id,full_name) AS dup_rank
FROM [Paintings].[dbo].[artist]

```

```
-- Checked table canvas_size for any duplicate records.
-- Canvas size table has no duplicates value found.
SELECT *,
ROW_NUMBER() OVER(PARTITION BY size_id,width,height ORDER BY size_id) AS dup_rank
FROM [Paintings].[dbo].[canvas_size]

```

```
-- Checked table museum for any duplicate records.
-- Museum table had no duplicates value found.
SELECT museum_id,name,address,city
,country,
COUNT(*) AS cunt
FROM [Paintings].[dbo].[museum]
GROUP BY museum_id,name,address,city
,country
HAVING COUNT(*) > 1
ORDER BY cunt DESC

SELECT *,
ROW_NUMBER() OVER(PARTITION BY museum_id,name ORDER BY museum_id,name) AS dup_rank
FROM [Paintings].[dbo].[museum]

```

```
-- Checked table museum_hours for any duplicate records.
-- Museum hour table had 1 duplicate value,and 350 unique values.
SELECT * FROM [Paintings].[dbo].[museum_hours]
With dup_cte
as (
SELECT *,
ROW_NUMBER() OVER(PARTITION BY museum_id,[day],[open],[close] ORDER BY museum_id )AS dup_rank
FROM [Paintings].[dbo].[museum_hours])
DELETE FROM dup_cte
WHERE dup_rank > 1

```

```

-- Checked table subject for any duplicate records.
-- There were 59 duplicate records, and 6712 unique values in table subject.
WITH duplicate as
(
SELECT *,
ROW_NUMBER() OVER(PARTITION BY work_id,subject ORDER BY work_id) as dup_rank
FROM [Paintings].[dbo].[subject]) 
DELETE FROM duplicate
WHERE dup_rank > 1

```

```
-- Checked table work for any duplicate records.
-- There were 60 duplicate values,and 14716 unique values in table subject.
SELECT * FROM [Paintings].[dbo].[work]
WITH duplicate_records as
(
SELECT *,
ROW_NUMBER() OVER(PARTITION BY work_id,artist_id ORDER BY work_id) as dup_rank
FROM [Paintings].[dbo].[work])
SELECT * FROM duplicate_records
WHERE dup_rank = 1

```

```

-- Checked table product_size for any duplicate records
-- There were 687 duplicate values, and 109660 unique values in table subject.
WITH duplicate_cte as
(
SELECT *,
ROW_NUMBER() OVER(PARTITION BY work_id,size_id,sale_price,regular_price ORDER BY size_id) as dup_rnk
FROM [Paintings].[dbo].[product_size])
DELETE FROM duplicate_cte
WHERE dup_rnk > 1

```

```
 **Note
-- The total datasets provided on kaggle were eight tables, but we worked with seven datasets only to find trends and patterns.
-- The eight table was the 'Url Image' of paintings.**

-- Here were the tables,i used to find trends and patterns. 
SELECT * FROM [Paintings].[dbo].[artist]
SELECT * FROM [Paintings].[dbo].[canvas_size]
SELECT * FROM [Paintings].[dbo].[museum]	
SELECT * FROM [Paintings].[dbo].[museum_hours]
SELECT * FROM [Paintings].[dbo].[subject]
SELECT * FROM [Paintings].[dbo].[work]
SELECT * FROM [Paintings].[dbo].[product_size]

```

```
-- There were 10,223 paintings which were not displayed on any museums.
-- 1) Fetch all the paintings which were not displayed on any museums?
select * 
from [Paintings].[dbo].[work] where museum_id is null

 ```

```
-- 2) Are there museuems without any paintings?
select * from [Paintings].[dbo].[museum] m
	where not exists (select 1 from [Paintings].[dbo].[work] w
					 where w.museum_id=m.museum_id)

```

```
-- 3) How many paintings have an asking price more than their regular price? 
SELECT name
     FROM [Paintings].[dbo].[work] w
	 JOIN [Paintings].[dbo].[product_size] p
	 ON w.work_id = p.work_id
	 WHERE sale_price > regular_price 
	 GROUP BY name
	
select *
from [Paintings].[dbo].[product_size]
	where sale_price > regular_price
	
-- 
```

```
	 
-- 4) Which canva size costs the most?
SELECT TOP 1 cz.label,
		pz.sale_price
		FROM [Paintings].[dbo].[canvas_size] cz
		JOIN [Paintings].[dbo].[product_size] pz
		ON  pz.size_id = cz.size_id
		GROUP BY cz.label,pz.sale_price
		ORDER BY pz.sale_price DESC

```

```
		
-- 5) Identify the museums with invalid city information in the given dataset.
select * from [Paintings].[dbo].[museum]
	where city LIKE  '[0-9]%'

```


```

-- 6) Which artist lived more longer,Display artist,nationality,style, age and the number of piantings they had before they died.

SELECT artist,nationality,style,no_of_paintings,age
FROM 
(
SELECT a.full_name as artist,a.nationality as nationality,
		w.style as style,(death - birth) as age,
		COUNT(*) AS no_of_paintings,
		RANK() OVER(ORDER BY COUNT(*) DESC) AS rnk
		FROM  [Paintings].[dbo].[artist] a
		JOIN [Paintings].[dbo].[work] w
		ON a.artist_id = w.artist_id
		GROUP BY a.full_name,a.nationality,w.style,(death - birth)) x
		WHERE x.rnk = 1

```

```

-- 7) Fetch the top 10 most famous painting subject

SELECT * 
	FROM
(SELECT ps.subject,
COUNT(*) AS number_of_paintings,
	DENSE_RANK() OVER(ORDER BY COUNT(*) DESC) as paints_rnk
	FROM [Paintings].[dbo].[subject] ps
	JOIN [Paintings].[dbo].[work] pw
	ON ps.work_id = pw.work_id
	GROUP BY ps.subject) x
	WHERE paints_rnk <= 10
		

```

```
-- 8) Identify the museums which are open on both Sunday and Monday. Display museum name, state and country.
WITH CTE AS(
SELECT DISTINCT pm.name,pm.country,pm.state
	FROM [Paintings].[dbo].[museum] pm
	JOIN [Paintings].[dbo].[museum_hours] ph
	ON  pm.museum_id = pm.museum_id
	WHERE day = 'Sunday' AND STATE IS NOT NULL
	GROUP BY pm.name,pm.country,pm.state
	UNION 
SELECT DISTINCT pm.name,pm.country,pm.state
	FROM [Paintings].[dbo].[museum] pm
	JOIN [Paintings].[dbo].[museum_hours] ph
	ON  pm.museum_id = pm.museum_id
	WHERE day = 'Monday' AND STATE IS NOT NULL
	GROUP BY pm.name,pm.country,pm.state)
	SELECT * FROM CTE

```

```
-- 9) How many museums were opened during the weekdays? Display museum name, county.
WITH weekdays_museum AS
(
SELECT DISTINCT m.name as museum_name,m.country as country,m.city as city,
	COUNT(*) AS days_of_open
	FROM [Paintings].[dbo].[museum] m
	JOIN [Paintings].[dbo].[museum_hours] mh
	ON m.museum_id = mh.museum_id
		GROUP BY m.name,m.country,m.city
		HAVING COUNT(*) = 5)
		SELECT museum_name,country,city
		FROM weekdays_museum
		
```

```
-- 10) Which museum had the highest number of most popular painting style?
SELECT museum_name,style,no_of_paints
FROM 
(
SELECT  w.museum_id ,w.name as museum_name,w.style as style,
		COUNT(*) AS no_of_paintings,
		RANK() OVER(ORDER BY COUNT(*) DESC) AS paints_rnk
		FROM [Paintings].[dbo].[work] w
		JOIN [Paintings].[dbo].[museum] m
		ON m.museum_id = w.museum_id
		WHERE m.museum_id IS NOT NULL AND w.style IS NOT NULL
		GROUP BY w.museum_id ,w.name,w.style) x 
		JOIN [Paintings].[dbo].[museum] m
		ON m.museum_id = x.museum_id
		WHERE x. paints_rnk <= 1

```

```
-- 11) Identify the artists whose paintings were displayed in multiple countries.

WITH multiple_countries as(
SELECT a.full_name as artist,m.country as country,w.name as name_of_paints
			FROM [Paintings].[dbo].[artist] a
			JOIN [Paintings].[dbo].[work] w
			ON a.artist_id = w.artist_id
			JOIN [Paintings].[dbo].[museum] m
			ON w.museum_id = w.museum_id
			GROUP BY a.full_name,m.country,w.name)
	SELECT artist,name_of_paints,COUNT(1) as no_of_countries
	FROM multiple_countries
	GROUP by artist,name_of_paints
	HAVING COUNT(*) > 1
	ORDER BY no_of_countries DESC
			
```

```
-- 12) Display the country and the city with the highest number of museums. Output to separate columns to mention the country and city. If there are multiple value, separate them with comma.
SELECT * FROM [Paintings].[dbo].[museum]

WITH cte_country AS
(SELECT country,COUNT(*) AS no_of_museum,
		RANK() OVER(ORDER BY COUNT(*) DESC) AS country_rnk
		FROM [Paintings].[dbo].[museum]
		GROUP BY country),
	 cte_city AS
		(SELECT city,COUNT(*) AS no_of_museum,
		RANK() OVER(ORDER BY COUNT(1) DESC) AS city_rnk
		FROM [Paintings].[dbo].[museum]
		GROUP BY city)
		SELECT STRING_AGG (country.country, ' , ' ) AS country , STRING_AGG(city.city, ', ') AS city
		--SELECT  country.country,city.city
		FROM cte_country country
		CROSS JOIN cte_city city
		WHERE country.country_rnk = 1
		AND city.city_rnk = 1


```

```
-- 13) Which country had the highest number of paintings? Display style categories of the paintings.

SELECT country,style,no_of_paintings
FROM
		(SELECT m.country as country,w.style as style,COUNT(*) AS no_of_paintings,
				RANK() OVER(ORDER BY COUNT(*) DESC) AS rnk 
				FROM [Paintings].[dbo].[museum] m
				JOIN [Paintings].[dbo].[work] w
				ON m.museum_id = w.museum_id
				GROUP BY m.country,w.style) x
				WHERE x.rnk <= 5
				AND style IS NOT NULL
				

```

```
-- 14) Which artist had the highest number of portraits paintings outside USA?  Display artist name, number of paintings and the artist nationality.

SELECT artist,nationality,no_of_paintings
FROM
(
SELECT a.full_name as artist,a.nationality as nationality,
			COUNT(*) AS no_of_paintings,
			RANK() OVER(ORDER BY COUNT(*) DESC)	AS paints_rank
			FROM [Paintings].[dbo].[work] w
			JOIN [Paintings].[dbo].[artist] a
			ON a.artist_id=w.artist_id
			JOIN [Paintings].[dbo].[subject] s
			ON s.work_id=w.work_id
			JOIN [Paintings].[dbo].[museum] m
			ON	m.museum_id=w.museum_id
			WHERE s.subject = 'Portraits'
			AND m.country != 'USA'
			GROUP BY a.full_name, a.nationality) x
			WHERE paints_rank=1

```


```
-- 15) How many museums were opened during the weekend? Display museum name, country and city.
WITH weekday_museums AS
(
SELECT DISTINCT m.name as museum_name,m.country as country,
	COUNT(*) AS days_of_open
	FROM [Paintings].[dbo].[museum] m
	JOIN [Paintings].[dbo].[museum_hours] mh
	ON m.museum_id = mh.museum_id
		GROUP BY m.name,m.country
		HAVING COUNT(*) > 5 )
		SELECT museum_name,country
		FROM weekday_museums

```


### Results/Findings:


1.	10,223 paintings were not displayed in any museum.
2.	There were no museums without any paintings based on the analysis.
3.	No museums had an asking price higher than their regular price.
4.	The most expensive canvas size identified was 48" x 96" (122 cm x 244 cm), priced at $1,115.
5.	Six museums were found with invalid city information:
a.	The State Hermitage Museum in Russia with an invalid city listed as 2.
b.	Museum Folkwang in Germany with an invalid city listed as 45128.
c.	Museum of Grenoble in France with an invalid city listed as 38000.
d.	Musée des Beaux-Arts de Quimper in France with an invalid city listed as 29000.
e.	Musée du Louvre in France with an invalid city listed as 75001.
f.	Kröller-Müller Museum in the Netherlands with an invalid city listed as 6731 AW Otterlo.
6.	The artist who lived the longest was Pierre-Auguste Renoir, a French Impressionist, who created 468 paintings and died at the age of 78.
7.	The top ten most famous painting subjects were:
a.	Portraits: 1,070 paintings
b.	Nudes: 525 paintings
c.	Landscape Art: 495 paintings
d.	Rivers/Lakes: 480 paintings
e.	Abstract/Modern Art and Flowers: 457 paintings each
f.	Still-Life: 395 paintings
g.	Seascapes: 324 paintings
h.	Marine Art/Maritime: 268 paintings
i.	Horses: 265 paintings
j.	Gardens: 261 paintings
8.	Thirty-eight museums were open on both Sunday and Monday.
9.	Nine museums were open on weekdays only, listed below:
a.	Musée des Beaux-Arts de Quimper, France
b.	Museum of Fine Arts, Houston, USA
c.	Nelson-Atkins Museum of Art, USA
d.	Norton Simon Museum, USA
e.	Philadelphia Museum of Art, USA
f.	The Art Institute of Chicago, USA
g.	The Barnes Foundation, USA
h.	Toledo Museum of Art, USA
i.	Walters Art Museum, USA
10.	The most popular painting style was Impressionist landscapes (Paysage), which appeared five times.
11.	4,467 paintings were displayed in multiple countries.
12.	London, New York, Paris, and Washington had the most museums in their respective cities, while the USA had the highest number of museums overall.
13.	The USA had the highest number of paintings with 881, while the Baroque period in the Netherlands had the lowest with 174.
14.	The artists with the most portrait paintings outside the USA were Jan Willem Pieneman and Vincent Van Gogh, both Dutch, with 14 portraits each.
15.	Forty-eight museums were open during the weekend.


### Recommendations



These recommendations aim to enhance the visibility of artworks, improve museum operations, and engage a broader audience through innovative exhibition strategies.


**1.	Increase Exhibition Visibility:** Since 10,223 paintings were not displayed in museums, consider digital exhibitions or partnerships with galleries to increase visibility and accessibility of these artworks.

**2.	Data Correction:** Address the six museums with invalid city information by cross-referencing with reliable data sources or reaching out to the museums for accurate city details.

**3.	Dynamic Pricing Strategy:** Since no museums had an asking price higher than the regular price, explore dynamic pricing models, especially for high-demand exhibits or premium artworks, to optimize revenue.

**4.	Canvas Size Insights:** Leverage the data about the most expensive canvas size (48" x 96") to guide pricing strategies for similarly large or custom-sized canvases, ensuring customers see the value.

**5.	Artist Longevity and Exhibit Strategy:** Given Pierre-Auguste Renoir’s prominence, museums could develop targeted exhibits on artists with long careers, focusing on the evolution of their work over time.

**6.	Subject-Based Curated Exhibits:** The top 10 painting subjects, such as Portraits, Nudes, and Landscape Art, suggest that museums could create more subject-focused curated exhibitions to engage audiences interested in these themes.

**7.	Specialization in Popular Styles:** Since Impressionist landscapes are highly popular, museums should consider hosting dedicated Impressionism-themed exhibits or workshops to attract art enthusiasts and students.

**8.	Increase Weekday Accessibility:** With only nine museums open during weekdays, encourage museums to extend operating hours on weekdays to cater to different visitor demographics, like tourists or school groups.

**9.	Target Multi-Country Audiences:** With 4,467 paintings displayed in multiple countries, collaborate with international museums to share traveling exhibits or create virtual tours, enabling global audiences to experience these works.

**10.	Expand Weekend Operations:** Since 48 museums operate on weekends, consider expanding weekend hours or offering special weekend-only events, such as guided tours or artist talks, to boost attendance.

**11.	Museum Network Development:** Capitalize on the fact that the USA has the most museums by forming national or international museum networks that facilitate resource sharing, cross-promotions, and collaborative exhibitions.

**12.	Promote Regional Art Specialties:** Given the low number of Baroque paintings in the Netherlands, consider increasing the focus on regional or historical art styles that may be underrepresented, promoting diversity in exhibitions.

**13.	Emphasize Famous Artists:** Highlight artists like Jan Willem Pieneman and Vincent Van Gogh with specialized exhibits, possibly outside of the Netherlands, to draw in international visitors interested in Dutch art.

**14.	Online Access to Paintings:** Since many paintings aren't displayed physically, create an extensive online collection with high-resolution images, detailed information, and virtual reality tours, making the art accessible to a global audience.

**15.	Integrate Historical and Modern Art:** Combine modern abstract/modern art with classic art styles in exhibitions to offer visitors a well-rounded experience, appealing to diverse tastes and educational purposes.






