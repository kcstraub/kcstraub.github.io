![img](http://i.imgur.com/OQrp0Wt.png)

I created the chart above after imputting the following SQL command:

select members.party, tweets.text, tweets.created_at
from members
join social_accounts
on members.bioguide_id = social_accounts.bioguide_id
join tweets 
on social_accounts.twitter_screen_name = tweets.screen_name
where tweets.text LIKE "%ebola%"
order by members.party

I attempted to make a line chart denoting the number of tweets per political party over time, starting from the first tweet about ebola
in April 2013... but I could not get my excel sheet to behave properly and separate my month and year out of the "created_at" column. 
I will poke around with this some more, try to get the answer, and update as appropriate.
