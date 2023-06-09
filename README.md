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
           case when call_category is null or call_category = 'n/a' then 1 else 0 end)/count(*)*100,1) 
           as call_percentage
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
    
 Q.21 Assume you're given tables with information on Snapchat users, including their ages and time spent sending and opening snaps.

Write a query to obtain a breakdown of the time spent sending vs. opening snaps as a percentage of total time spent on these activities grouped by age group. Round the percentage to 2 decimal places in the output.

Notes:
  - Calculate the following percentages:
        - time spent sending / (Time spent sending + Time spent opening)
        - Time spent opening / (Time spent sending + Time spent opening)
  - To avoid integer division in percentages, multiply by 100.0 and not 100.

   `Company Name - SnapChat`
   
Solution - 

    with snap as 
    (select ab.age_bucket,
            sum(case when activity_type = 'open' then time_spen else 0 end) as t_open,
            sum(case when activity_type = 'send' then time_spen else 0 end) as t_send
    from activities act
    left join age_breakdown ab on act.user_id = ab.user_id
    group by ab.age_bucket)
    
    select age_bucket, 
           round(t_sned/(t_send + t_open)*100.0,2) as send_perc,
           round(t_open/(t_open + t_send)*100.0,2) as open_perc
    from snap;    
    
 Q.22 Given a table of tweet data over a specified time period, calculate the 3-day rolling average of tweets for each user. Output the user ID, tweet date, and rolling averages rounded to 2 decimal places.

Notes:

  - A rolling average, also known as a moving average or running mean is a time-series technique that examines trends in data over a specified period of time.
  - In this case, we want to determine how the tweet count for each user changes over a 3-day period.
   `Company Name - Tweeter`
   
Solution - 

    select user_id, 
           tweet_date,
           round(avg(tweet_count) over (partition by user_id order by tweet_date perceding and current row),2)
           as rolling_avg_3d
    from tweets;        

 Q.23 Assume you're given a table with measurement values obtained from a Google sensor over multiple days with measurements taken multiple times within each day.

Write a query to calculate the sum of odd-numbered and even-numbered measurements separately for a particular day and display the results in two different columns. Refer to the Example Output below for the desired format.

Definition:

      - Within a day, measurements taken at 1st, 3rd, and 5th times are considered odd-numbered measurements, and measurements taken at 2nd, 4th, and 6th times are considered even-numbered measurements.
   `Company Name - Google`
   
Solution - 

    with odd_even as 
    (select *,
           rank() over (partition by cast(measurements_time as DATE) order by measurements_time) as number
    from measurements)
    
    select cast(measurements_time as DATE) as measurement_day,
    sum(case when number % 2 <> 0 then measurement_value else 0 end) as odd_num,
    sum(case when number % 2 = 0 then measurement_vale else 0 end) as even_num
    from odd_even
    group by cast(measurements_time as DATE)
    order by measurement_day;

 Q.24 New TikTok users sign up with their emails. They confirmed their signup by replying to the text confirmation to activate their accounts. Users may receive multiple text messages for account confirmation until they have confirmed their new account.

A senior analyst is interested to know the activation rate of specified users in the emails table. Write a query to find the activation rate. Round the percentage to 2 decimal places.

Definitions:

     - emails table contain the information of user signup details.
     - texts table contains the users' activation information.
     
Assumptions:

   - The analyst is interested in the activation rate of specific users in the emails table, which may not include all users that could potentially be found in the texts table.
   - For example, user 123 in the emails table may not be in the texts table and vice versa.
   `Company Name - TikTok`
   
Solution - 

    select round(1.0*sum(case when t.signup_action = 'Confrimed' then 1 else 0 end)/count(distinct e.user_id),2) as comfirm_rate
    from emails e
    left join texts t on e.email_id = t.email_id; 

Q.25 Assume you're given a table with information on Amazon customers and their spending on products in different categories, write a query to identify the top two highest-grossing products within each category in the year 2022. The output should include the category, product, and total spend.
   `Company Name - Amazon`
   
Solution - 

    with cte1 as
    (select category, product, sum(spend) as total_spend
    from product_spend 
    where extract(year from transaction_date) = 2022
    group by category, product),
    
    cte2 as
    (select*, row_number() over(partition by category order by total_spend desc) as rnk
    from cte1)
    
    select category, product, total_spend
    from cte2
    where rnk <=2; 

Q.26 Your team at JPMorgan Chase is soon launching a new credit card. You are asked to estimate how many cards you'll issue in the first month.

Before you can answer this question, you want to first get some perspective on how well new credit card launches typically do in their first month.

Write a query that outputs the name of the credit card, and how many cards were issued in its launch month. The launch month is the earliest record in the monthly_cards_issued table for a given card. Order the results starting from the biggest issued amount.
   `Company Name - JPMorgan`
   
Solution - 

    with cte1 as
    (select *, rank() over(partition by card_name order by issue_year, issue_month) as rnk
    from monthly_cards_issued )

    select card_name, issued_amount
    from cte1
    where rnk =1
    order by issued_amount desc; 

Q.27 A Microsoft Azure Supercloud customer is a company which buys at least 1 product from each product category.
Write a query to report the company ID which is a Supercloud customer.
   `Company Name - Microsoft`
   
Solution - 

    with cte1 as
    (select c.*, p.product_category
    from customer_contracts c
    left join products p on c.product_id = p.product_id)

    select customer_id
    from cte1
    group by customer_id
    having count(distinct product_category) = (select count(distinct product_category) from products); 

Q.28 A phone call is considered an international call when the person calling is in a different country than the person receiving the call.

What percentage of phone calls are international? Round the result to 1 decimal.

Assumption:

  - The caller_id in phone_info table refers to both the caller and receiver.
  
   `Company Name - Verizon`
   
Solution - 

    with cte1 as
    (select pc.*, p1.country_id as call_country,
    p2.country_id as rec_country
    from phone_calls  pc
    left join phone_info  p1 on pc.caller_id = p1.caller_id
    left join phone_info  p2, on pc.receiver_id = p2.caller_id)

    select round(1.0*sum(case when call_country <> rec_country then 1 else 0 end)/count(*)*100,1)
    from cte1;
    
Q.29 Assume you are given the table on Walmart user transactions. Based on a user's most recent transaction date, write a query to obtain the users and the number of products bought.

Output the user's most recent transaction date, user ID and the number of products sorted by the transaction date in chronological order.
  
   `Company Name - Walmaart`
   
Solution - 

    with cte1 as
    (select pc.*, rank() over(partition by user_id 
    order by transaction_date desc) as rnk
    from phone_calls)

    select transaction_date, user_id, count(distinct product_id) as purchase_count
    from cte1
    where rnk = 1 
    group by transaction_date, user_id
    order by transaction_date;    

Q.30 Given a table containing the item count for each order and the frequency of orders with that item count, write a query to determine the mode of the number of items purchased per order on Alibaba. If there are several item counts with the same frequency, you should sort them in ascending order.

   `Company Name - Alibaba`
   
Solution - 

    with cte1 as
    (select pc.*, rank() over(order by order_occurrences desc) as rnk
    from items_per_order )

    select items_count as mode
    from cte1
    where rnk = 1 
    order by mode;    
    
## `Difficulty Level - Hard`

Q.31 Assume you're given a table containing information on Facebook user actions. Write a query to obtain number of monthly active users (MAUs) in July 2022, including the month in numerical format "1, 2, 3".

Hint:

   - An active user is defined as a user who has performed actions such as 'sign-in', 'like', or 'comment' in both the current month and the previous month.

   `Company Name - Facebook`
   
Solution - 

    with cte1 as
    (select distinct user_id
    from items_per_order 
    where extract(month from event_date) = 7
    and extract(year from event_date) = 2022
    intersect
    select distinct user_id
    from items_per_order 
    where extract(month from event_date) = 6
    and extract(year from event_date) = 2022)

    select '7' as month, count(user_id) as monthly_count
    from cte1;   

Q.32 Sometimes, payment transactions are repeated by accident; it could be due to user error, API failure or a retry error that causes a credit card to be charged twice.

Using the transactions table, identify any payments made at the same merchant with the same credit card for the same amount within 10 minutes of each other. Count such repeated payments.

Assumptions:

      - The first transaction of such payments should not be counted as a repeated payment. This means, if there are two transactions performed by a merchant with the same credit card and for the same amount within 10 minutes, there will only be 1 repeated payment.
      
   `Company Name - Stripe`
   
Solution - 

     with cte1 as
     (SELECT *, lag(transaction_timestamp) OVER(PARTITION BY 
     merchant_id, credit_card_id, amount order by transaction_timestamp) 
     as prev_trasaction_time
     FROM transactions)

    select  count(transaction_id) as payment_count
    from cte1
    where extract(epoch from transaction_timestamp - 
    prev_trasaction_time)/60 <=10;   




