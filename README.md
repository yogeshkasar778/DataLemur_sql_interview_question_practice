# DataLemur SQL Interview Question and Answer - 

## `Difficulty Level - Easy`

Q.1 Given a table of candidates and their skills, you're tasked with finding the candidates best suited for an open Data Science job. You want to find candidates who are proficient in Python, Tableau, and PostgreSQL. Write a query to list the candidates who possess all of the required skills for the job. Sort the output by candidate ID in ascending order.

Assumption:
  - There are no duplicates in the candidates table.
   
   `Company Name - LinkedIn`
   
Solution - 
    
    with data_skill as 
         (select candidate_id, STRING_AGG(skill,',' order by skill) as req_skill
          from candidates
          group by candidate_id)
    select candidate_id
    from data_skill
    where req_skill like '%PostgreSQL,Python,Tableau%'
    order by candidate_id asc;

Q.2 Assume you're given the table below about Facebook Page and Page likes (as in "Like a Facebook Page"). Write a query to return the IDs of the Facebook pages which do not possess any likes. The output should be sorted in ascending order. 

   `Company Name - Facebook`
   
Solution - 

    select p.page_id
    from pages as p
    left join page_likes as pl on p.page_id = pl.page_id
    where pl.page_id is not null
    order by p.page_id asc;

Q.3 Tesla is investigating production bottlenecks and they need your help to extract the relevant data. Write a query that determines which parts with the assembly steps have initiated the assembly process but remain unfinished.
Assumptions:
   - parts_assembly table contains all parts currently in production, each at varying stages of the assembly process.
   - An unfinished part is one that lacks a finish_date.
   
This question is straightforward, so let's approach it with simplicity in both thinking and solution.

Effective April 11th 2023, the problem statement and assumptions were updated to enhance clarity.

   `Company Name - Tesla`
   
Solution - 

    select part, assembly_step
    from parts_assembly
    where  finished_date is null ;    
    
Q.4 Assume you are given the table below that shows job postings for all companies on the LinkedIn platform. Write a query to get the number of companies that have posted duplicate job listings.

Clarification:
  - Duplicate job listings refer to two jobs at the same company with the same title and description.
  
   `Company Name - LinkedIN`
   
Solution - 

    with duplicate_job as
                 (select company_id, lower(title), lower(description), count(distinct job_id)
                 from job_listings
                 group by company_id, lower(title), lower(description)
                 having count(distinct job_id) > 1)
     select count(company_id) as co_w_duplicate_job
     from duplicate_job;    
    
Q.5 Write a query to identify the top 2 Power Users who sent the highest number of messages on Microsoft Teams in August 2022. Display the IDs of these 2 users along with the total number of messages they sent. Output the results in descending order based on the count of the messages.

Assumption:
  - No two users have sent the same number of messages in August 2022.

   `Company Name - Microsoft`
   
Solution - 

    select sender_id, count(sender_id) as message_count
    from message
    where sent_date between '08/01/2022' and '08/30/2022'
    group by sender_id
    having count(sender_id) > 2 
    order by message_count desc;        
    
    or
    
    SELECT sender_id, COUNT(message_id) as message_count 
    FROM messages
    WHERE EXTRACT(year FROM sent_date) = '2022' AND EXTRACT(month FROM sent_date) = '08'
    GROUP BY sender_id
    ORDER BY message_count DESC
    LIMIT 2 ;
    
 Q.6 Given the reviews table, write a query to retrieve the average star rating for each product, grouped by month. The output should display the month as a numerical value, product ID, and average star rating rounded to two decimal places. Sort the output first by month and then by product ID.

   `Company Name - Amazon`
   
Solution - 

    select extract(month from submit_date) as mth, product_id as product, round(avg(stars),2) as avg_stars
    from reviews 
    group by extract(month from submit_date), product_id
    order by mth, product asc ;   
    
 Q.7 Your team at JPMorgan Chase is soon launching a new credit card, and to gain some context, you are analyzing how many credit cards were issued each month.
 
 Write a query that outputs the name of each credit card and the difference in issued amount between the month with the most cards issued, and the least cards issued. Order the results according to the biggest difference.

   `Company Name - JPMorgan Chase`
   
Solution - 

    select card_name, max(issued_amount) - min(issued_amount) as difference
    from monthly_cards_issued  
    group by card_name
    order by difference desc;  
    
 Q.8 Assume you're given the table on user viewership categorised by device type where the three types are laptop, tablet, and phone. 
 
Write a query that calculates the total viewership for laptops and mobile devices where mobile is defined as the sum of tablet and phone viewership. Output the total viewership for laptops as laptop_reviews and the total viewership for mobile devices as mobile_views.

   `Company Name - New York Times`
   
Solution - 

    with view as 
    (select 
         case when device_type = 'laptop' then 1
         else 0
         end as laptop,
         case when device_type = 'tablet','phone' then 1
         else 0
         end as mobile
    from viewership)
    
    select sum(laptop) as laptop_view, sum(mobile) as mobile_view
    from view;      
    
Q.9 Assume you're given the tables containing completed trade orders and user details in a Robinhood trading system.
 
Write a query to retrieve the top three cities that have the highest number of completed trade orders listed in descending order. Output the city name and the corresponding number of completed trade orders.

   `Company Name - Robinhood`
   
Solution - 

    select u.city, count(*) as total_orders
    from trades t
    left join users u on t.user_id = u.user_id
    where t.status = 'Completed'
    group by u.city
    order by total_orders
    limit 3;    
    
Q.10 Assume you have an events table on Facebook app analytics. Write a query to calculate the click-through rate (CTR) for the app in 2022 and round the results to 2 decimal places.

Definition and note:

  - Percentage of click-through rate (CTR) = 100.0 * Number of clicks / Number of impressions
  - To avoid integer division, multiply the CTR by 100.0, not 100.

   `Company Name - Facebook`
   
Solution - 

    with evn_ctr as 
    (select app_id,
           sum(case when event_type = 'impression' then 1
           else 0
           end) as number_impression,
           sum(case when event_type = 'click' then 1
           else 0
           end) as number_click
    from events  
    where extract(year from timestamp) = '2022'
    group by app_id)
    
    select app_id, round(100.0 * number_click/number_impression,2) as ctr
    from evn_ctr;    
    
  Q.11 You're trying to find the mean number of items per order on Alibaba, rounded to 1 decimal place using tables which includes information on the count of items in each order (item_count table) and the corresponding number of orders for each item count (order_occurrences table).

   `Company Name - Alibaba`
   
Solution - 

    with orders as 
    (select sum(item_count * order_occurrences) as total_items,
            sum(order_occurrences) as total_orders
    from items_per_orders)
    
    select round(1.0 * total_items / total_orders,1) as mean
    from orders;       
    
Q.12 CVS Health is trying to better understand its pharmacy sales, and how well different products are selling. Each drug can only be produced by one manufacturer.

Write a query to find the top 3 most profitable drugs sold, and how much profit they made. Assume that there are no ties in the profits. Display the result from the highest to the lowest total profit.

Definition:

  - cogs stands for Cost of Goods Sold which is the direct cost associated with producing the drug.
  - Total Profit = Total Sales - Cost of Goods Sold

   `Company Name - CSV Health`
   
Solution - 

    select drug, (total_sales - cogs) as total_profit
    from pharmacy_sales 
    order by total_profit desc
    limit 3;          

Q.13 CVS Health is analyzing its pharmacy sales data, and how well different products are selling in the market. Each drug is exclusively manufactured by a single manufacturer.

Write a query to identify the manufacturers associated with the drugs that resulted in losses for CVS Health and calculate the total amount of losses incurred.

Output the manufacturer's name, the number of drugs associated with losses, and the total losses in absolute value. Display the results sorted in descending order with the highest losses displayed at the top.

   `Company Name - CSV Health`
   
Solution - 

    select manufacturer, count(distinct product_id) as drug_count, sum(cogs - total_sales) as total_loss
    from pharmacy_sales 
    where cogs > total_sales
    group by manufacturer
    order by total_loss desc;     

Q.14 CVS Health is trying to better understand its pharmacy sales, and how well different products are selling.

Write a query to find the total drug sales for each manufacturer. Round your answer to the closest million, and report your results in descending order of total sales.

Because this data is being directly fed into a dashboard which is being seen by business stakeholders, format your result like this: "$36 million".

   `Company Name - CSV Health`
   
Solution - 

    select manufacturer,concat('$', round(sum(total_sales)/pow(10,6)), ' million') as sale
    from pharmacy_sales 
    group by manufacturer
    order by sum(total_sales) desc;   
    
Q.15 Given a table of Facebook posts, for each user who posted at least twice in 2021, write a query to find the number of days between each user’s first post of the year and last post of the year in the year 2021. Output the user and number of the days between each user's first and last post.

   `Company Name - Facebook`
   
Solution - 

    select user_id, max(cast(post_date as date)) - min(cast(post_date as date)) as sale
    from posts  
    where extract(year from post_date) = 2021
    group by user_id
    having count(post_id) >= 2 ;       
    
Q.16 Assume you're given a table Twitter tweet data, write a query to obtain a histogram of tweets posted per user in 2022. Output the tweet count per user as the bucket and the number of Twitter users who fall into that bucket.

In other words, group the users by the number of tweets they posted in 2022 and count the number of users in each group.

   `Company Name - Tweeter`
   
Solution - 

    with tweet as 
    (select user_id, count(tweet_id) as number
    from tweets  
    where extract(year from tweet_date) = 2022
    group by user_id)
    
    select number as tweet_bucket, count(user_id) as user_num
    from tweet
    group by number;     
    
Q.17 UnitedHealth has a program called Advocate4Me, which allows members to call an advocate and receive support for their health care needs – whether that's behavioural, clinical, well-being, health care financing, benefits, claims or pharmacy help.

Write a query to find how many UHG members made 3 or more calls. case_id column uniquely identifies each call made.

   `Company Name - UnitedHealth`
   
Solution - 

    with cte as 
    (select policy_holder_id
    from callers   
    group by policy_holder_id
    having count(case_id) >= 3)
    
    select count(policy_holder_id) as user_num
    from cte; 
   
 Q.18 UnitedHealth Group has a program called Advocate4Me, which allows members to call an advocate and receive support for their health care needs – whether that's behavioural, clinical, well-being, health care financing, benefits, claims or pharmacy help.

Calls to the Advocate4Me call centre are categorised, but sometimes they can't fit neatly into a category. These uncategorised calls are labelled “n/a”, or are just empty (when a support agent enters nothing into the category field).

Write a query to find the percentage of calls that cannot be categorised. Round your answer to 1 decimal place.
   `Company Name - UnitedHealth`
   
Solution - 

    select round(1.0*sum(
           case when call_category is null or call_category = 'n/a' then 1 else 0 end)/count(*)*100,1) as call_percentage
    from callers;    

 Q.19 Assume there are three Spotify tables containing information about the artists, songs, and music charts. Write a query to find the top 5 artists whose songs appear most frequently in the Top 10 of the global_song_rank table.

Display the top 5 artist names in ascending order, along with their song appearance ranking. Note that if two artists have the same number of song appearances, they should have the same ranking, and the rank numbers should be continuous (i.e. 1, 2, 2, 3, 4, 5).

For instance, if Ed Sheeran appears in the Top 10 five times and Bad Bunning four times, Ed Sheeran should be ranked 1st, and Bad Bunny should be ranked 2nd.
   `Company Name - Spotify`
   
Solution - 

    with cte1 as 
    (select a.artist_name, count(*) as num_of_times
    from global_song_rank rnk
    left join songs s on s.song_id = rnk.song_id
    left join artists a on a.artist_id = s.artist_id
    where rnk.rank <=10
    group by a.artist_name),
    
    cte2 as 
    (select *, dense_rank() over(order by num_of_times desc) as artist_rank
    from cte1)
    
    select artist_name, artist_rank
    from cte2
    where artist_rank <=5
    order by artist_rank,artist_name;    

 Q.20 Assume you are given the table below on Uber transactions made by users. Write a query to obtain the third transaction of every user. Output the user id, spend and transaction date.
   `Company Name - Uber`
   
Solution - 

    with cte as 
    (select *, row_number() over(partition by user_id order by transactions_date) as tran_num
    from transactions )
    
    select user_id, speed, transactions_date 
    from cte
    where tran_num = 3;
    
    
    
    
