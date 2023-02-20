# MySQL-Project
Sakila Database project

USE sakila;

# 1 - list of actors avaiable on the database table and last updated date

SELECT last_name, first_name, last_update
	FROM actor;
    
# 2 Task
# Is there any change in the actor's first name or last name?
# How many actors have the same first names and last names?  Make a list of all these actors
# How many actors have unique names and how many are there in the database table?

SELECT UPPER(CONCAT(first_name,'',last_name))
	AS 'Actor Name'
    FROM actor;
    
SELECT first_name, COUNT(first_name) from actor
	GROUP BY first_name;
SELECT last_name, COUNT(last_name) FROM actor
	GROUP BY last_name;


/*Make a list of all actors with the same first name*/


select first_name as 'First Name',
Count(*) 
from sakila.actor
group by first_name 
Having Count(*) > 1 
;

select last_name as 'Last Name',
Count(*) 
from sakila.actor
group by last_name 
Having Count(*) > 1 
;

select first_name as 'First Name', last_name as 'Last Name',
Count(*) 
from sakila.actor
group by first_name, last_name
Having Count(*) > 1 
;        

/*There are 2 actors named Susan Davis*/



/*Find the actors with unique names (first and last?)*/  

Select COUNT(DISTINCT first_name) 
from sakila.actor
;

Select COUNT(DISTINCT last_name) 
from sakila.actor

SELECT distinct first_name 
FROM actor
GROUP BY first_name;

SELECT DISTINCT last_name 
FROM actor;

SELECT COUNT(*)
(DISTINCT last_name)
FROM actor; 


# TASK 3
# To avoid confusion about actors' names, the management needs a list of actors who share a name with 
# someone else and a list of those with unique names

# Share names
SELECT DISTINCT a1.first_name, a1.last_name 
FROM actor a1, actor a2 
WHERE a1.last_name = a2.last_name AND a1.actor_id !=  a2.actor_id
ORDER BY a1.last_name;

# unique names
SELECT MAX(a.first_name), a.last_name,
       COUNT(DISTINCT a.first_name) as num_first_names
FROM actor AS a
GROUP BY a.last_name
HAVING COUNT(DISTINCT a.first_name) = 1;


# TASK 4
# The management needs to categorize the actors playing identity roles, such as Action, Romance, Horror
# and Mystery. For this, the board members want to have a detailed overview of films based on the actors' preference




# TASK 5
# Management wants to analyze the trend for movies based on their categories and determine which movie 
# category is the largest.  To achieve this, the board wants to see the various rating categories with 
# descriptions.  It needs to determine which are the suitable for kids, which movies are restricted for 
# all under age 16 unless accompanied by a parent, and which movies are restricted for all audiences 
# under age 18.

create view ratings_view
as select title, description, rating, name
from film, film_category, category
where film.film_id=film_category.film_id
AND film_category.category_id=category.category_id;

select * from film where rating='R'; 
select * from film where rating <> 'R'; 
select * from film where rating != 'R'; 
select rating, count(rating) from film group by rating; 

/* in operator is used when we work with text data */ 
select * from film where rating in('G','R','PG'); 
/*Summary stats on the table using groupby function */ 
select rating, avg(rental_rate) as r, 
max(rental_rate),min(rental_rate) from film group by 
rating order by r desc; 


SELECT * 
FROM ratings_view
WHERE rating IN ('PG')('G');

# TASK 6
# Board wants to understand the replacement cost; that is, the amount of money required to replace an existing
# asset with an equally valued or similar asset at the current market price.
# 		* Figure out the movie titles where the replacemnt cost is up to $9
#		* Find the movie titles where the replacement costs is between $15 and $20
# 		* Find the movies with the highest replacement cost but the lowerst rental cost

select count(*) from film where replacement_cost 
between 0.00 and 9.00; 
select count(*) from film where replacement_cost 
between 9.99 and 14; 
select count(*) from film where replacement_cost>9.99 
and replacement_cost<14;
select count(*) from film where replacement_cost not 
between 9.99 and 14; 
select * from film order by replacement_cost desc; 
select title, replacement_cost from film order by 
replacement_cost desc; 


# TASK 7
# The management needs to see the list of all the films along with the number of actors listed for each film.

SELECT f.title AS 'Film Title', COUNT(fa.actor_id) AS 
`Number of Actors` 
FROM film_actor fa 
INNER JOIN film f 
ON fa.film_id= f.film_id 
GROUP BY f.title; 

# Task 8
# Music of Queen and Kris Kristofferson has been an unexpected resurgence.  As an unintended consequence, films 
# starting with the letters 'K' and 'Q' have also soared in popularity.  Display the titles of the movies starting 
# with the letter 'K' and 'Q'

SELECT title
FROM film 
WHERE (title like 'K%' OR title like 'Q%')
AND language_id in (
	SELECT language_id 
	FROM language 
	WHERE name = 'English'
)
order by title;


# TASK 9 
# Because the film 'AGENT TRUMAN' has enjoyed great success, display all the actors who appeared in this film

SELECT first_name, last_name, title
FROM actor, film
WHERE title = 'AGENT TRUMAN';

# TASK 10
Select  film.title, category.name
from film
join film_category
on film.film_id=film_category.film_id
join category
on film_category.category_id=category.category_id
where name like '%family%';



# TASK 11
SELECT f.title, COUNT(rental_id) AS 'Times Rented' 
FROM rental r 
JOIN inventory i 
ON (r.inventory_id = i.inventory_id) 
JOIN film f 
ON (i.film_id = f.film_id) 
GROUP BY f.title 
ORDER BY `Times Rented` DESC; 



# TASK 12
SELECT name AS category_name , AVG(rental_rate) AS 
Avg_rental_price, AVG(replacement_cost) AS 
Avg_replacement_price, (AVG(replacement_cost) - 
AVG(rental_rate)) AS replace_sub_rental 
FROM film_category 
INNER JOIN film USING (film_id) 
INNER JOIN category USING (category_id) 
GROUP BY category_id 
HAVING replace_sub_rental > 15 ; 


# TASK 13

SELECT name, COUNT(film_id) as Number_of_films
FROM film_category INNER JOIN category USING
(category_id)
GROUP BY category_id
HAVING Number_of_films > 65 AND Number_of_films < 70
ORDER BY Number_of_films DESC;





    

