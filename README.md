--DVD Rental Database Analysis
--Author: Htin Linn Aung
--Skills: Filtering, Aggregations, Pattern Matching, Date Handling
************************************************************/

```sql
-- SECTION 1: BASIC DATA EXTRACTION
-- ----------------------------------------------------------

-- 1. Full customer contact list for email campaign
-- Purpose: Generate a mailing list for marketing outreach.
SELECT
    first_name,
    last_name,
    CONCAT(first_name, ' ', last_name) AS full_name,
    email
FROM customer
ORDER BY last_name ASC;


-- 2. Sort customer list by last name
-- Purpose: Create an alphabetical reference list for customer support.
SELECT
    customer_id,
    first_name,
    last_name,
    email
FROM customer
ORDER BY 
    last_name DESC, 
    first_name DESC;


-- 3. Different prices that have been paid from High to Low
-- Purpose: Identify the various price points currently offered to customers.
SELECT DISTINCT
    amount AS payment_amount
FROM payment
ORDER BY payment_amount DESC;


-- 4. A list of all the districts customers are from
-- Purpose: Identify which regions or districts the business currently serves.
SELECT DISTINCT
    district
FROM address
ORDER BY district ASC;


-- 5. Latest rental date
-- Purpose: Determine the most recent activity in the store to check system latency.
SELECT
    rental_date AS latest_rental_timestamp
FROM rental
ORDER BY rental_date DESC
LIMIT 1;


-- 6. How many films does the company have?
-- Purpose: Audit the total number of unique titles available in the catalog.
SELECT
    COUNT(DISTINCT film_id) AS total_film_count
FROM film;


-- 7. How many distinct last names are there?
-- Purpose: Analyze demographic diversity within the customer base.
SELECT
    COUNT(DISTINCT last_name) AS unique_last_name_count
FROM customer;

-- SECTION 2: Filtering and conditional logic
-- ----------------------------------------------------------

-- 8. Transaction count for Customer 100
-- Purpose: Analyze the engagement level/loyalty of a specific user.
SELECT
    COUNT(*) AS total_payments_count
FROM payment
WHERE customer_id = 100;


-- 9. Search for specific customer
-- Purpose: Quickly retrieve contact details for a specific individual.
SELECT
    first_name,
    last_name,
    email
FROM customer
WHERE first_name = 'ERICA';


-- 10. Data Integrity Check: Missing first names
-- Purpose: Quality control check to identify incomplete user profiles.
SELECT
    COUNT(*) AS missing_name_count
FROM customer
WHERE first_name IS NULL;


-- 11. Operational Audit: Unreturned rentals
-- Purpose: Track outstanding inventory that has not yet been returned.
SELECT
    COUNT(*) AS active_rentals_count
FROM rental
WHERE return_date IS NULL;


-- 12. Low-value transaction filtering (<= $2)
-- Purpose: Identify budget-tier transactions for promotional analysis.
SELECT
    payment_id,
    customer_id,
    amount
FROM payment
WHERE amount <= 2
ORDER BY amount DESC;


-- 13. Outlier payment values for specific customers
-- Purpose: Audit specific high-value or low-value anomalies for targeted accounts.
SELECT
    payment_id,
    customer_id,
    amount,
    payment_date
FROM payment
WHERE customer_id IN (322, 346, 354)
    AND (amount < 2 OR amount > 10)
ORDER BY 
    customer_id ASC, 
    amount DESC;


-- 14. Targeted January 2020 Financial Audit
-- Purpose: Reconcile specific transactions for a monthly financial review.
SELECT
    payment_id,
    customer_id,
    amount,
    payment_date
FROM payment
WHERE customer_id IN (12, 25, 67, 93, 124, 234)
    AND amount IN (4.99, 7.99, 9.99)
    AND payment_date >= '2020-01-01' 
    AND payment_date < '2020-02-01';


-- 15. Content Analysis: "Documentary" keyword search
-- Purpose: Quantify the volume of niche content (Documentaries) in the catalog.
SELECT
    COUNT(*) AS documentary_description_count
FROM film
WHERE description ILIKE '%Documentary%';


-- 16. Title and Description Pattern Matching
-- Purpose: Filter films based on complex naming conventions for promotional collections.
SELECT
    COUNT(*) AS saga_pattern_movie_count
FROM film
WHERE description ILIKE '%Saga%'
    AND (title ILIKE 'A%' OR title ILIKE '%R');


-- 17. Customer Name Pattern Matching
-- Purpose: Advanced search for customer records with partial data.
SELECT
    first_name,
    last_name,
    email
FROM customer
WHERE first_name ILIKE '%ER%'
    AND first_name ILIKE '_A%'
ORDER BY last_name DESC;


-- 18. Daily transaction volume for specific price tiers
-- Purpose: Monitor revenue performance for specific price points on a given date.
SELECT
    COUNT(*) AS specific_tier_payment_count
FROM payment
WHERE (amount = 0 OR amount BETWEEN 3.99 AND 7.99)
    AND payment_date >= '2020-05-01' 
    AND payment_date < '2020-05-02';

-- SECTION 3: Data Aggregation and String Manipulation
-- ----------------------------------------------------------

-- 19. Inventory Financial Metrics
-- Purpose: Calculate the total financial exposure and average replacement 
--          cost for the film catalog.
SELECT
    MIN(replacement_cost) AS min_replacement_cost,
    MAX(replacement_cost) AS max_replacement_cost,
    ROUND(AVG(replacement_cost), 2) AS avg_replacement_cost,
    SUM(replacement_cost) AS total_inventory_replacement_value
FROM film;


-- 20. Staff Performance Overview
-- Purpose: Evaluate employee productivity based on total revenue and 
--          transaction volume (excluding refunds/zero-amount transactions).
SELECT
    staff_id,
    SUM(amount) AS total_revenue,
    COUNT(amount) AS total_transaction_count
FROM payment
WHERE amount > 0
GROUP BY staff_id
ORDER BY staff_id ASC;


-- 21. Peak Sales Performance by Employee
-- Purpose: Identify the highest revenue-generating days for each staff member.
SELECT
    staff_id,
    DATE(payment_date) AS sales_date,
    SUM(amount) AS daily_revenue_total,
    COUNT(*) AS daily_transaction_count
FROM payment
WHERE amount > 0
GROUP BY staff_id, DATE(payment_date)
ORDER BY daily_revenue_total DESC, sales_date ASC;


-- 22. High-Frequency Customer Analysis (April 2020)
-- Purpose: Identify loyal customers who made multiple purchases in a single 
--          day during the late April promotional period.
SELECT
    customer_id,
    DATE(payment_date) AS payment_date,
    ROUND(AVG(amount), 2) AS avg_transaction_amount,
    COUNT(*) AS daily_payment_count
FROM payment
WHERE DATE(payment_date) BETWEEN '2020-04-28' AND '2020-04-30'
GROUP BY customer_id, DATE(payment_date)
HAVING COUNT(*) > 1
ORDER BY avg_transaction_amount DESC;


-- 23. Email Format Analysis
-- Purpose: Identify long email addresses that may require UI layout 
--          adjustments or specific data cleaning.
SELECT
    UPPER(email) AS email_uppercase,
    LOWER(email) AS email_lowercase,
    LENGTH(email) AS email_char_length
FROM customer
WHERE LENGTH(email) > 30;


-- 24. Long-Name Customer Identification
-- Purpose: Extract customer records with names exceeding 10 characters 
--          for specialized formatting or data validation.
SELECT
    LOWER(first_name) AS clean_first_name,
    LOWER(last_name) AS clean_last_name,
    LOWER(email) AS clean_email
FROM customer
WHERE LENGTH(first_name) > 10 
   OR LENGTH(last_name) > 10;


-- 25. Email Domain Delimiter Verification
-- Purpose: Verify the position of the '.' in the domain suffix using string positioning.
SELECT
    email,
    SUBSTRING(email FROM '\.') AS extracted_dot,
    POSITION('.' IN email) AS dot_position
FROM customer;
```

