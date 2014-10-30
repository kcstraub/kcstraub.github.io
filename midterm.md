### PADJO Midterm "Malarkey"

*Question 1*

Query:
select leso.county, leso.state, leso.`Item Name`, leso.quantity, county_boundaries.geometry, county_boundaries.value, county_boundaries.GEO_ID, county_boundaries.GEO_ID2
from leso 
INNER JOIN county_ansi
ON leso.county = county_ansi.county
and leso.state = county_ansi.state
INNER JOIN county_boundaries
ON county_ansi.FIPS = county_boundaries.fips
WHERE leso.`Item Name`LIKE "%MINE RESISTANT%"
group by county_boundaries.`County Name`

Link to map:
https://www.google.com/fusiontables/data?docid=1PXNxY6FYBG-KR0ws1pIjNhwKISZcvTDg0ERug9-n#map:id=3

*Question 2*

Query 1:
select leso.`Item Name`, leso.`Acquisition Cost`, leso.county, leso.state, leso.quantity, county_boundaries.geometry, county_boundaries.value, county_boundaries.GEO_ID, county_boundaries.GEO_ID2
from leso 
INNER JOIN county_ansi
ON leso.county = county_ansi.county
and leso.state = county_ansi.state
INNER JOIN county_boundaries
ON county_ansi.FIPS = county_boundaries.fips
group by leso.`Item Name`
order by leso.`Acquisition Cost` DESC

Most expensive item: "AIRCRAFT, ROTARY WING"

Query 2:
select leso.`Item Name`, leso.`Acquisition Cost`, leso.county, leso.state, leso.quantity, county_boundaries.geometry, county_boundaries.value, county_boundaries.GEO_ID, county_boundaries.GEO_ID2
from leso 
INNER JOIN county_ansi
ON leso.county = county_ansi.county
and leso.state = county_ansi.state
INNER JOIN county_boundaries
ON county_ansi.FIPS = county_boundaries.fips
WHERE leso.`Item Name` = "AIRCRAFT, ROTARY WING"
GROUP BY leso.county

List of counties with at least one "AIRCRAFT, ROTARY WING":
Brevard, FL
Chilton, AL
Desoto, MS
Oklahoma, OK
Pinal, AZ
Saint Louis, MO
Santa Barbara, CA

*Question 3*

Query: 
select leso.`Item Name`, census_quickfacts.fips, leso.county, leso.state, 
COUNT (leso.`Item Name`)
from leso 
INNER JOIN county_ansi
ON leso.county = county_ansi.county
and leso.state = county_ansi.state
INNER JOIN census_quickfacts
ON county_ansi.FIPS = census_quickfacts.fips
INNER JOIN psc
ON psc.`PSC CODE` = leso.psc_code
WHERE leso.`Item Name` = "RIFLE,7.62 MILLIMETER" 
OR leso.`Item Name`= "SHOTGUN,12 GAGE"
OR leso.`Item Name`= "RIFLE,5.56 MILLIMETER"
OR leso.`Item Name` = "REVOLVER,CALIBER .38 SPECIAL"
OR leso.`Item Name` = "PISTOL,CALIBER .45,AUTOMATIC"
group by census_quickfacts.fips
order by COUNT (leso.`Item Name`) DESC
LIMIT 10

List of top 10 counties: 
Los Angeles, CA;
Leon, FL;
Franklin, OH;
Franklin, KY;
Cook, IL;
Ramsey, MN;
Sacremento, CA;
Prince Georges, MD;
Sangamon, IL;
Fulton, GA

*Question 4*

Query: 
select leso.`Item Name`,  census_quickfacts.fips, leso.county, COUNT (leso.`Item Name`) / census_quickfacts.PST045213 * 1000 as per_100k,
COUNT (leso.`Item Name`) as tot_gun
from leso 
INNER JOIN county_ansi
ON leso.county = county_ansi.county
and leso.state = county_ansi.state
INNER JOIN census_quickfacts
ON county_ansi.FIPS = census_quickfacts.fips
INNER JOIN psc
ON psc.`PSC CODE` = leso.psc_code
WHERE leso.`Item Name` = "RIFLE,7.62 MILLIMETER" 
OR leso.`Item Name`= "SHOTGUN,12 GAGE"
OR leso.`Item Name`= "RIFLE,5.56 MILLIMETER"
OR leso.`Item Name` = "REVOLVER,CALIBER .38 SPECIAL"
OR leso.`Item Name` = "PISTOL,CALIBER .45,AUTOMATIC"
group by census_quickfacts.fips
order by per_100k DESC
LIMIT 10

*Question 5*

I was interested in finding out how many air conditioners were provided nationally, by month, by year, through the 1033 program since 2010. After mining that data, I noticed that a traditional bar-style histogram would have been confusing, so I used a line chart instead. 

![img](http://i.imgur.com/AQG7cjN.png)

*Questions 6*

Query:
select county_ansi.FIPS, county_ansi.county, census_quickfacts.fips, census_quickfacts.PST045213 as pop, county_boundaries.geometry
from county_ansi
INNER JOIN census_quickfacts
ON county_ansi.FIPS = census_quickfacts.fips 
INNER JOIN county_boundaries
ON county_boundaries.fips = county_ansi.FIPS
LEFT JOIN leso
ON leso.county = county_ansi.county
and leso.state = county_ansi.state
WHERE leso.Quantity IS NULL  
order by pop DESC

Link to map:
https://www.google.com/fusiontables/data?docid=1gkiEmgpSv_y2X1FYlQetXZLXC4Bd5sc_tDqG6-E2#map:id=3

*Question 7*

Query: 
select county_ansi.FIPS, leso.county, leso.state, leso.`Item Name`, leso.Quantity, leso.`Acquisition Cost`, leso.Quantity * leso.`Acquisition Cost` as tot_cost, 
SUM (leso.Quantity * leso.`Acquisition Cost`)
from leso
INNER JOIN county_ansi
ON leso.county = county_ansi.county
and leso.state = county_ansi.state
GROUP by county_ansi.FIPS
order by SUM (leso.Quantity * leso.`Acquisition Cost`) DESC


![img](http://i.imgur.com/a48vTzX.png)

*Question 8*

My queries aimed to investigate the curious problem of unnamed items in the 1033 database. I encountered many unnamed items under the “Item Name” column in the leso table and immediately started wondering what this missing information might mean. 
I couldn’t find much in the way of a specific explanation as to why certain items would be unnamed in the database. One article from U.S. News, explained a phenomenon I’m fully aware of: “Variables are missing, fields are completely unstandardized and the errors in spelling make accurate analysis impossible,” so I figured I would chaulk this issue up to simple misinformed, misrepresented, or simply lazy data collection. 

This pursuit is newsworthy, because I want to narrow down where a critism of misinformation, misrepresentation or laziness might be focused. So, I decided to mine for information revealing where – that is, to which counties --  most of these unnamed items are going. So, I used the following query to find the top ten counties with the highest number of unnamed items listed under their location in the database. 

select leso.`Item Name`,  psc.`PSC CODE`, census_quickfacts.fips, leso.county, leso.state, COUNT (leso.`Item Name`) as tot_unnamed,census_quickfacts.PST045213 as pop
from leso 
INNER JOIN county_ansi
ON leso.county = county_ansi.county
and leso.state = county_ansi.state
INNER JOIN census_quickfacts
ON county_ansi.FIPS = census_quickfacts.fips
INNER JOIN psc
ON psc.`PSC CODE` = leso.psc_code
WHERE leso.`Item Name` = ""
group by census_quickfacts.fips
order by tot_unnamed DESC
limit 10

That query lead me to the following counties:
BALDWIN, GA
CALHOUN, AL
COLBERT, AL
HARRIS, TX
JEFFERSON, AL
KENT, DE
MANASSAS CITY, VA
SAINT JOSEPH, IN
VENTURA, CA
WILLIAMSON, TX

After learning this information, then news folks or those curious can follow up with those particular counties to get the scoop on what caused the unnamed items to appear, and perhaps that could help discover what those items might be. Which got me thinking…. If I don’t know what the item actually is, can I still find out, at least, the category of the unnamed item? I ran the following query to discover the top ten categories of items, using a PSC code, which had the most unnamed items under their label. 

select leso.`Item Name`,  psc.`PSC CODE`, census_quickfacts.fips, leso.county, leso.state, COUNT (leso.`Item Name`) as tot_unnamed,census_quickfacts.PST045213 as pop
from leso 
INNER JOIN county_ansi
ON leso.county = county_ansi.county
and leso.state = county_ansi.state
INNER JOIN census_quickfacts
ON county_ansi.FIPS = census_quickfacts.fips
INNER JOIN psc
ON psc.`PSC CODE` = leso.psc_code
WHERE leso.`Item Name` = ""
group by psc.`PSC CODE`
order by tot_unnamed DESC
limit 10

![img](http://i.imgur.com/uNgQD5e.png)

Again, I hope that this information would clarify where some of the problem with these data may be. It is a newsworthy pursuit by creating some more specific pressure on those particular country police departments to get to the bottom of a fishy situation. 



