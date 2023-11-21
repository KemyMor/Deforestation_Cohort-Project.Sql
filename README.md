## Deforestation_Cohort-Project.Sql
*Skills used: Joins, CTE's, Temp Tables,String Functions, Aggregate Functions, Creating Views, Converting Data Types.*

# Question 1: What are the total number of countries involved in deforestation? 
--- SELECT
    COUNT(DISTINCT Country_name) AS TotalNumberOfCountries
    FROM Forest_Area;
----
![TotalNumberOfCountries](https://github.com/KemyMor/Deforestation_Cohort-Project.Sql/blob/84cf8ec125476eb60b8bb647e58882f864799598/TotalNumberOfCountries.jpg)

# Question 2: Show the income groups of countries having total area ranging from 75,000 to 150,000 square meter?
---SELECT 
        Land_Area.country_name,
        Land_Area.total_area_sq_mi,
        Regions.income_group
  FROM 
      Land_Area
  JOIN 
      Regions ON Land_Area.country_code = Regions.country_code
  WHERE 
      Land_Area.total_area_sq_mi BETWEEN 75000 AND 150000
  ORDER BY 
      Land_Area.country_name;
-------
![Income_Group_Total_Area](https://github.com/KemyMor/Deforestation_Cohort-Project.Sql/blob/37b96d8524ca92debe6b04762a35f0d4a1add2b8/Income_Group_Total_Area.jpg)

# Question 3: Calculate average area in square miles for countries in the 'upper middle income region'. 
----SELECT 
    Regions.region,
    AVG(Land_Area.total_area_sq_mi) AS AVGTotalArea,
    Regions.income_group
FROM 
    Land_Area
JOIN 
    Regions ON Land_Area.country_code = Regions.country_code
GROUP BY 
    Regions.region, Regions.income_group
HAVING 
    Regions.income_group =  'upper middle income'
ORDER BY 
    AVGTotalArea DESC;  
-----
![Upper_Middle_Income_Region](https://github.com/KemyMor/Deforestation_Cohort-Project.Sql/blob/37b96d8524ca92debe6b04762a35f0d4a1add2b8/Upper_Middle_Income_Region.jpg)

*Comparing the above result with the rest of the income categories, it was observed the follwoing;
The income group of a particular region is strongly dependent on the total area coverage. 
The higher the total average land area the higher the income group. 
see below query table*
![High_Upper_Middle_Income_Regions](https://github.com/KemyMor/Deforestation_Cohort-Project.Sql/blob/37b96d8524ca92debe6b04762a35f0d4a1add2b8/High_Upper_Middle_Income_Regions.jpg)

![Upper_Middle_Income_Region](https://github.com/KemyMor/Deforestation_Cohort-Project.Sql/blob/37b96d8524ca92debe6b04762a35f0d4a1add2b8/Upper_Middle_Income_Region.jpg)

![Upper_Lower_Middle_Income_Region](https://github.com/KemyMor/Deforestation_Cohort-Project.Sql/blob/37b96d8524ca92debe6b04762a35f0d4a1add2b8/Upper_Lower_Middle_Income_Region.jpg)

![Upper_Low_Income_Region](https://github.com/KemyMor/Deforestation_Cohort-Project.Sql/blob/37b96d8524ca92debe6b04762a35f0d4a1add2b8/Upper_Low_Income_Region.jpg)

# Question 4: Determine the total forest area in square km for countries in the 'high income' group. 
   ----Compare result with the rest of the income categories.
---- SELECT 
    DISTINCT Regions.country_name,
	Regions.income_group,
    SUM(Forest_Area.forest_area_sqkm) AS TotalArea_Coverage
FROM 
    Regions
JOIN 
    Forest_Area ON Forest_Area.country_code = Regions.country_code
GROUP BY 
    Regions.country_name, Regions.income_group, Forest_Area.forest_area_sqkm
HAVING
    Regions.income_group = 'High income'
ORDER BY 
    TotalArea_Coverage DESC;  
----
![High_Income_Countries](https://github.com/KemyMor/Deforestation_Cohort-Project.Sql/blob/37b96d8524ca92debe6b04762a35f0d4a1add2b8/High_Income_Countries.jpg)

*Compare result with the rest of the income categories.*
![High_Low_Income_Countries](https://github.com/KemyMor/Deforestation_Cohort-Project.Sql/blob/37b96d8524ca92debe6b04762a35f0d4a1add2b8/High_Low_Income_Countries.jpg)

# Question 5: Show countries from each region(continent) having the highest total forest areas. 
---SELECT 
    Regions.region,
    Regions.country_name,
    MAX(Forest_Area.forest_area_sqkm) AS TotalForestArea
FROM 
    Regions
JOIN 
    Forest_Area ON Forest_Area.country_code = Regions.country_code
WHERE 
    Regions.region IS NOT NULL
GROUP BY 
    Regions.region, Regions.country_name
ORDER BY 
    TotalForestArea DESC;
----
![Highest_Forest_Area_Countries](
    
    
-- Using CTE to perform Calculation on Partition By in previous query
SELECT * FROM Forest_Area
SELECT * FROM Land_Area
SELECT * FROM Regions

WITH ForvsReg (country_name, region, income_group, forest_area_sqkm, total_area_sq_mi, TotalCoverage)
AS
(
    SELECT 
        Fore.country_name, 
        Reg.region, 
        Reg.income_group,  
        Fore.forest_area_sqkm, 
        Lan.total_area_sq_mi,
        SUM(Fore.forest_area_sqkm) OVER (PARTITION BY Fore.country_name ORDER BY Fore.country_name) as TotalCoverage
    FROM 
        Forest_Area Fore
    JOIN 
        Regions Reg ON Fore.country_name = Reg.country_name
    JOIN 
        Land_Area Lan ON Lan.country_code = Reg.country_code
    WHERE 
        Fore.country_name IS NOT NULL
)
SELECT 
    *, 
    (TotalCoverage / total_area_sq_mi) * 100 as CoveragePercentage
FROM 
    ForvsReg;

	-- Creating View to store data for later visualizations
	Select * from PercentTotalLandCoverage;
Create View PercentTotalLandCoverage as
SELECT 
        Fore.country_name, 
        Reg.region, 
        Reg.income_group,  
        Fore.forest_area_sqkm, 
        Lan.total_area_sq_mi,
        SUM(Fore.forest_area_sqkm) OVER (PARTITION BY Fore.country_name ORDER BY Fore.country_name) as TotalCoverage
    FROM 
        Forest_Area Fore
    JOIN 
        Regions Reg ON Fore.country_name = Reg.country_name
    JOIN 
        Land_Area Lan ON Lan.country_code = Reg.country_code
    WHERE 
        Fore.country_name IS NOT NULL

		SELECT * FROM PercentTotalLandCoverage
		WHERE COUNT(country_name) = 'Nigeria' AND income_group = 'Lower middle income';    
