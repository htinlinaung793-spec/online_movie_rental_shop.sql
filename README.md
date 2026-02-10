--DVD Rental Database Analysis
--Author: Htin Linn Aung
--Skills: Filtering, Aggregations, Pattern Matching, Date Handling
************************************************************/

-- SECTION 1: BASIC DATA EXTRACTION
-- ----------------------------------------------------------

```sql
-- 1. Full customer contact list for email campaign
SELECT
    first_name,
    last_name,
    email
FROM customer;

-- 2. Sort customer list by last name
SELECT
    *
FROM customer
ORDER BY last_name DESC, first_name DESC;

-- 3. Different prices that have been paid from High to Low
SELECT DISTINCT
    amount
FROM payment
ORDER BY amount DESC;

-- 4. A list of all the districts customers are from
SELECT DISTINCT
    district
FROM address;

-- 5. Latest rental date
SELECT
    rental_date
FROM rental
ORDER BY rental_date DESC
LIMIT 1;

-- 6. How many films does the company have?
SELECT
    COUNT(DISTINCT film_id)
FROM film;

-- 7. How many distinct last names are there?
SELECT
    COUNT(DISTINCT last_name)
FROM customer;

-- 8. Payments made by customer_id 100
SELECT
    COUNT(*)
FROM payment
WHERE customer_id = 100;

-- 9. Find customer by first name 'ERICA'
SELECT
    first_name,
    last_name
FROM customer
WHERE first_name = 'ERICA';

-- 10. Check for missing first names
SELECT
    COUNT(*)
FROM customer
WHERE first_name IS NULL;

-- 11. Rentals not returned yet
SELECT
    COUNT(*)
FROM rental
WHERE return_date IS NULL;

-- 12. Payments less than or equal to $2
SELECT
    payment_id,
    amount
FROM payment
WHERE amount <= 2;

-- 13. Specific customers with amounts < $2 or > $10
SELECT
    *
FROM payment
WHERE customer_id IN (322, 346, 354)
  AND (amount < 2 OR amount > 10)
ORDER BY customer_id ASC, amount DESC;

-- 14. Targeted January 2020 Audit
SELECT
    *
FROM payment
WHERE customer_id IN (12, 25, 67, 93, 124, 234)
  AND amount IN (4.99, 7.99, 9.99)
  AND payment_date BETWEEN '2020-01-01' AND '2020-02-01';

-- 15. Count movies with "Documentary" in description
SELECT
    COUNT(*)
FROM film
WHERE description ILIKE '%Documentary%';

-- 16. Search for 'Saga' with specific Title patterns
SELECT
    COUNT(*) AS no_of_movies
FROM film
WHERE description ILIKE '%Saga%'
  AND (title ILIKE 'A%' OR title ILIKE '%R');

-- 1. Customers with 'ER' in name and 'A' as the 2nd letter
SELECT
    *
FROM customer
WHERE first_name ILIKE '%ER%'
  AND first_name ILIKE '_A%'
ORDER BY last_name DESC;

-- 2. Count of payments (Amount 0 or 3.99-7.99) on 2020-05-01
SELECT
    COUNT(*) AS no_of_payments
FROM payment
WHERE (amount = 0 OR amount BETWEEN 3.99 AND 7.99)
  AND payment_date BETWEEN '2020-05-01' AND '2020-05-02';

-- 3. Basic Stats of replacement cost in film table
SELECT
    MIN(replacement_cost) AS min_replacement,
    MAX(replacement_cost) AS max_replacement,
    ROUND(AVG(replacement_cost), 2) AS avg_replacement,
    SUM(replacement_cost) AS total_replacement_value
FROM film;

-- 4. Staff performance: Total amount and count of payments
SELECT
    staff_id,
    SUM(amount) AS total_amount,
    COUNT(amount) AS total_payments
FROM payment
WHERE amount > 0
GROUP BY staff_id
ORDER BY staff_id;

-- 5. Highest daily sales and volume per employee
SELECT
    staff_id,
    DATE(payment_date) AS sales_date,
    SUM(amount) AS daily_sum,
    COUNT(*) AS daily_count
FROM payment
WHERE amount > 0
GROUP BY staff_id, DATE(payment_date)
ORDER BY SUM(amount) DESC, DATE(payment_date);

-- 6. Average payment per customer/day with >1 payment (Late April 2020)
SELECT
    customer_id,
    DATE(payment_date) AS payment_date,
    ROUND(AVG(amount), 2) AS avg_amount,
    COUNT(*) AS payment_count
FROM payment
WHERE DATE(payment_date) IN ('2020-04-28', '2020-04-29', '2020-04-30')
GROUP BY customer_id, DATE(payment_date)
HAVING COUNT(*) > 1
ORDER BY avg_amount DESC;

-- 7. Email character analysis
SELECT
    UPPER(email) AS email_upper,
    LOWER(email) AS email_lower,
    LENGTH(email) AS email_length
FROM customer
WHERE LENGTH(email) > 30;

-- 8. Find long names (over 10 chars) and output in lower case
SELECT
    LOWER(first_name) AS first_name,
    LOWER(last_name) AS last_name,
    LOWER(email) AS email
FROM customer
WHERE LENGTH(first_name) > 10 
   OR LENGTH(last_name) > 10;

-- 9. Extract the dot '.' from email address
SELECT
    RIGHT(LEFT(RIGHT(email, 5), 2), 1) AS extracted_dot
FROM customer;
```

