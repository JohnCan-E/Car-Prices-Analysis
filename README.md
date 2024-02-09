# Car-Prices-Analysis
A Small Project - subqueries, join  and CTE demonstrated


select * 
from car_prices_analysis..processes2

-- all records where the selling price is greater than $100 000
SELECT *
FROM car_prices_analysis..processes2
WHERE selling_price > 100000
order by selling_price asc

--  missing or null values in the 'name' column

DELETE FROM car_prices_analysis..processes2
WHERE name IS NULL OR name = ''


--where the selling price  > the average selling price , for cars with a fuel type of 'Petrol'

SELECT *
FROM car_prices_analysis..processes2
WHERE selling_price > 
    ( SELECT AVG(selling_price)
    FROM car_prices_analysis..processes2
    WHERE fuel = 'Petrol'
);

--how many of the top 5 owners are based within each car brand
SELECT name, owner, COUNT(owner) AS Num_Top_Owners
FROM (
    SELECT name, owner, ROW_NUMBER() OVER (PARTITION BY name ORDER BY selling_price DESC) AS RowNum
    FROM car_prices_analysis..processes2
) AS ranked
WHERE RowNum <= 5
GROUP BY name, owner
order by Num_Top_Owners ASC


--top 10 car owners within the top car brands identified
SELECT owner, COUNT(*) AS Num_Cars
FROM car_prices_analysis..processes2 AS C
JOIN (
    SELECT name
    FROM car_prices_analysis..processes2
    GROUP BY name
    ORDER BY AVG(selling_price) DESC
    LIMIT 5
) AS Top_Brands 
ON C.name = Top_Brands.name
GROUP BY owner
ORDER BY Num_Cars DESC
LIMIT 5;

--count of top 5 owners based within each car brand using a Common Table Expression (CTE)
WITH Top_Owners AS (
    SELECT name, owner, ROW_NUMBER() OVER (PARTITION BY name ORDER BY selling_price DESC) AS RowNum
    FROM car_prices_analysis..processes2
)
SELECT name, owner, COUNT(owner) AS Num_Top_Owners
FROM Top_Owners
WHERE RowNum <= 5
GROUP BY name, owner;


