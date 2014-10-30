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
