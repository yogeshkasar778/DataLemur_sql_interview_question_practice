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
    
    
