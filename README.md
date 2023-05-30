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

Q.3 Assume you're given the table below about Facebook Page and Page likes (as in "Like a Facebook Page"). Write a query to return the IDs of the Facebook pages which do not possess any likes. The output should be sorted in ascending order. 

   `Company Name - Facebook`
   
Solution - 

    select p.page_id
    from pages as p
    left join page_likes as pl on p.page_id = pl.page_id
    where pl.page_id is not null
    order by p.page_id asc;    
    
    
    
    
    
