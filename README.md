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
    
    
    
    
    
    
    
