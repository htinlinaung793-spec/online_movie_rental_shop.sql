# SQL-statements
--The following SQL queries were developed to answer specific business questions:

-- 1.Basic Functions

--Full customer contact list for email campaign
SELECT
first_name,
last_name,
email
FROM customer

--Sort customer list by last name.
SELECT
*
FROM customer
ORDER BY last_name DESC, first_name DESC

--Different prices that have been paid from High to Low.
SELECT DISTINCT
amount
FROM payment
ORDER BY amount DESC

--A list of all the districts customers are from.
SELECT DISTINCT
district
FROM address

--Latest rental date.
SELECT
rental_date
FROM rental
ORDER BY rental_date DESC
LIMIT 1

--How many films does the company have?
SELECT
COUNT (DISTINCT film_id)
FROM film

--How many distinct last names of the customers are there?
SELECT
COUNT (DISTINCT last_name)
FROM customer

--How many payment were made by the customer with customer_id = 100?
SELECT
COUNT (*)
FROM payment
WHERE customer_id = 100

--What is the last name of our customer with first name 'ERICA'?
SELECT
first_name,
last_name,
FROM customer
WHERE first_name = 'ERICA'

--Count of customers with fist name 'Unknown'.
SELECT
COUNT (*)
FROM customer
WHERE first_name IS null

--How many rentals have not been returned yet?
SELECT
COUNT (*)
FROM rental
WHERE renturn_date IS null

--A list of all the payment_ids with an amount less than or equal to $2.
SELECT
payment_id,
amount
FROM payment
WHERE amount <= 2

--A list of all the payment of the customer 322, 346 and 354 where the amount is either less than $2 or greater than $10
SELECT
*
FROM payment
WHERE customer_id IN (322,346,354)
AND (amount < 2 OR amount > 10)
ORDER BY customer_id ASC, amount DESC

--6 complaints of customers with customer_id:  12,25,67,93,124,234 with amounts 4.99, 7.99 and 9.99 in January 2020.
SELECT
*
FROM payment
WHERE customer_id IN (12,25,67,93,124,234)
AND amount IN (4.99,7.99,9.99)
AND payment_date BETWEEN '2020-01-01' AND '2020-02-01'

--How many movies are there that contain the "Documentary" in the description?
SELECT
COUNT (*)
FROM film
WHERE description ILIKE '%Documentary%'

--Movies that contain 'Saga' in the description and where the title starts either with 'A' or ends with 'R'? (Use the alias 'no_of_movies'.)
SELECT
COUNT (*) AS no_of_movies
FROM film
WHERE description ILIKE '%Saga%'
AND (title ILIKE 'A%' 
OR title ILIKE '%R')

--A list of all customers where the first name contains 'ER' and has an 'A' as the second letter. Order the results by the last name descendingly.
SELECT
*
FROM customer
WHERE first_name ILIKE '%ER%'
AND first_name ILIKE '_A%'
ORDER BY last_name DESC

--Count of payments where the amount is either 0 or is between 3.99 and 7.99 and in the same time has happened on 2020-05-01.
SELECT
COUNT (*) AS no_of_payments
FROM payment
WHERE (amount = 0
OR amount BETWEEN 3.99 AND 7.99)
AND payment_date BETWEEN '2020-05-01' AND '2020-05-02'

--Basic Stats of replacement cost in film table
SELECT
MIN(replacement_cost),
MAX(replacement_cost),
ROUND(AVG(replacement_cost),2) AS avg,
SUM(replacement_cost)
FROM film

--Which of the two employees (staff_id) is responsible for more payments?
--Which of the two is responsible for a higher overall payment amount?
SELECT
staff_id,
SUM(amount),
COUNT(amount)
FROM payment
WHERE amount > 0
GROUP BY staff_id
ORDER BY staff_id

--Which employee had the highest sales amount in a single day?
--Which employee had the most sales in a single day (not counting payments with amount = 0?
SELECT
staff_id,
date(payment_date),
SUM(amount),
COUNT(*)
FROM payment
WHERE amount > 0
GROUP BY staff_id, date(payment_date)
ORDER BY SUM(amount) DESC, date(payment_date)

--Find out what is the average payment amount grouped by customer and day in 2020, April 28, 29 and 30.
--Consider only the days/customers with more than 1 payment (per customer and day). Order by the average amount in a descending order.
SELECT
customer_id,
date(payment_date),
ROUND(AVG(amount),2) AS avg,
COUNT(*)
FROM payment
WHERE date(payment_date) IN ('2020-04-28','2020-04-29','2020-04-30')
GROUP BY customer_id, date(payment_date)
HAVING COUNT(*) > 1
ORDER BY ROUND(AVG(amount),2) DESC

SELECT
UPPER(email) AS email_upper,
LOWER(email) AS email_lower,
LENGTH(email) 
FROM customer
WHERE LENGTH(email) > 30

--In the email system there was a problem with names where either the first name or the last name is more than 10 characters long.
--Find these customers and output the list of these first and last names in all lower case.
SELECT
LOWER(first_name) AS first_name,
LOWER(last_name) AS last_name,
LOWER(email) AS email
FROM customer
WHERE LENGTH(first_name) > 10 OR 
LENGTH(last_name) > 10

--extract just the dot '.' from the email address
SELECT
RIGHT(LEFT(RIGHT(email,5),2),1)
FROM customer



























































