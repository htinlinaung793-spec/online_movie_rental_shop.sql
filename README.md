DVD Rental Database Analysis
Author: Htin Linn Aung
Skills: Filtering, Aggregations, Pattern Matching, Date Handling
************************************************************/

-- SECTION 1: BASIC DATA EXTRACTION
-- ----------------------------------------------------------

-- 1. Full customer contact list for email campaign
-- Purpose: Extracting active emails for marketing reach-out
SELECT 
    first_name, 
    last_name, 
    email 
FROM customer;

-- 2. Sort customer list by last name
-- Purpose: Organizational view for manual audit
SELECT 
    * FROM customer 
ORDER BY 
    last_name DESC, 
    first_name DESC;
