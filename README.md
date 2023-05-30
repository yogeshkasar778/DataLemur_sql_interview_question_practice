# DataLemur SQL Interview Question and Answer - 

## `Difficulty Level - Easy`

Q.1 Given a table of candidates and their skills, you're tasked with finding the candidates best suited for an open Data Science job. You want to find candidates who are proficient in Python, Tableau, and PostgreSQL. Write a query to list the candidates who possess all of the required skills for the job. Sort the output by candidate ID in ascending order.

Assumption:
  - There are no duplicates in the candidates table.
   
   `Company Name - Apple`
   
 ###  Solution - 
    
    select distinct count(*) as event_count, event_name
    from playbook_events
    where device = 'macbook pro'
    group by event_name
    order by event_count;  
