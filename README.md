## SQL Advent Calendar Challenge
This was A 24-day SQL Advent Calendar Challenge assessing SQL capabilities through questions graded into three difficulty levels: easy, medium, and hard. 

### Tool Used 
MySQL

### SQL Skills Demonstrated
- Aggregate functions
- Window functions
- CTEs
- Joins
- Subqueries
- Case statements
- Date manipulation

### Day 1
A ski resort company wants to know which customers rented ski equipment for more than one type of activity (e.g., skiing and snowboarding). List the customer names and the number of distinct activities they rented equipment for.

Question level of difficulty: Medium

```sql
SELECT customer_name, 
      COUNT(DISTINCT activity) AS activity_count
FROM rentals
GROUP BY customer_name
HAVING COUNT(DISTINCT activity) > 1;
```

### Day 2
Santa wants to know which gifts weigh more than 1 kg. Can you list them?

Question level of difficulty: Easy
```sql
SELECT gift_name
FROM gifts
WHERE weight_kg > 1;
```

### Day 3
You’re trying to identify the most calorie-packed candies to avoid during your holiday binge. Write a query to rank candies based on their calorie count within each category. Include the candy_name, candy_category, calories, and rank (rank_in_category) within the category.

Question level of difficulty: Hard
```sql
SELECT candy_name,
       candy_category,
       calories,
       RANK() OVER(PARTITION BY candy_category 
       ORDER BY calories DESC) AS rank
FROM candy_nutrition;
```

### Day 4
You’re planning your next ski vacation and want to find the best regions with heavy snowfall. Given the tables resorts and snowfall, find the average snowfall for each region and sort the regions in descending order of average snowfall. Return the columns region and average_snowfall.

Question level of difficulty: Medium
```sql
SELECT sr.region, 
       AVG(sf.snowfall_inches) AS average_snowfall
FROM ski_resorts AS sr 
INNER JOIN snowfall sf
ON sr.resort_id = sf.resort_id
GROUP BY sr.region
ORDER BY average_snowfall DESC;
```

### Day 5
This year, we’re celebrating Christmas in the Southern Hemisphere! Which beaches are expected to have temperatures above 30°C on Christmas Day?

Question level of difficulty: Easy
```sql
SELECT beach_name
FROM beach_temperature_predictions
WHERE expected_temperature_c > 30
  AND date = '2024-12-25';

```

### Day 6
Scientists are tracking polar bears across the Arctic to monitor their migration patterns and caloric intake. Write a query to find the top 3 polar bears that have traveled the longest total distance in December 2024. Include their bear_id, bear_name, and total_distance_traveled in the results.

Question level of difficulty: Hard
```sql
SELECT pb.bear_id,
      pb.bear_name,
      SUM(tn.distance_km) AS total_distance_travelled
FROM polar_bears pb 
INNER JOIN tracking tn 
ON pb.bear_id = tn.bear_id
WHERE tn.date BETWEEN '2024-12-01' AND '2024-12-31'
GROUP BY pb.bear_id, pb.bear_name
ORDER BY total_distance_travelled DESC
LIMIT 3;
```

### Day 7
The owner of a winter market wants to know which vendors have generated the highest revenue overall. For each vendor, calculate the total revenue for all their items and return a list of the top 2 vendors by total revenue. Include the vendor_name and total_revenue in your results.

Question level of difficulty: Medium
```sql
SELECT v.vendor_name,
       SUM(s.quantity_sold*s.price_per_unit) AS total_revenue
FROM vendors AS v
INNER JOIN sales AS s 
ON v.vendor_id = s.vendor_id
GROUP BY v.vendor_name
ORDER BY total_revenue DESC
LIMIT 2;
```

### Day 8
You are managing inventory in Santa’s workshop. Which gifts are meant for “good” recipients? List the gift name and its weight.

Question level of difficulty: Easy
```sql
SELECT gift_name, weight_kg
FROM gifts
WHERE recipient_type LIKE 'good%';
```

### Day 9
A community is hosting a series of festive feasts, and they want to ensure a balanced menu. Write a query to identify the top 3 most calorie-dense dishes (calories per gram) served for each event. Include the dish_name, event_name, and the calculated calorie density in your results.

Question level of difficulty: Hard
```sql
WITH calorie_density AS
(
    SELECT dish_name,
       event_name, 
       calories/weight_g AS calorie_density
FROM events e 
INNER JOIN menu m 
ON e.event_id = m.event_id
),
ranked_dishes AS
(
SELECT dish_name, event_name, calorie_density,
    ROW_NUMBER() OVER(PARTITION BY event_name 
    ORDER BY calorie_density DESC) AS ranking
FROM calorie_density
)
SELECT dish_name, event_name, calorie_density
FROM ranked_dishes
WHERE ranking <= 3;
```

### Day 10
You are tracking your friends’ New Year’s resolution progress. Write a query to calculate the following for each friend: number of resolutions they made, number of resolutions they completed, and success percentage (% of resolutions completed) and a success category based on the success percentage:
- Green: If success percentage is greater than 75%.
- Yellow: If success percentage is between 50% and 75% (inclusive).
- Red: If success percentage is less than 50%.

Question level of difficulty: Medium
```sql
WITH resolutions_count AS
(
SELECT friend_name, 
       COUNT(*) AS number_of_resolutions,
       SUM(CASE 
        WHEN is_completed = 1 THEN 1 ELSE 0 END) 
         AS resolutions_completed
FROM resolutions 
GROUP BY friend_name
),
percent_count AS
(
SELECT friend_name, 
       number_of_resolutions,
       resolutions_completed,
       (resolutions_completed/number_of_resolutions)*100 
       AS percent_of_resolutions_completed
FROM resolutions_count
)
SELECT friend_name,
        number_of_resolutions,
        resolutions_completed,
        percent_of_resolutions_completed,
        CASE WHEN percent_of_resolutions_completed > 75 THEN 'Green'
             WHEN percent_of_resolutions_completed BETWEEN 50 AND 74 THEN 'Yellow'
             ELSE 'Red' END 
             AS success_category
FROM percent_count;
```

### Day 11
You are preparing holiday gifts for your family. Who in the family_members table are celebrating their birthdays in December 2024? List their name and birthday.

Question level of difficulty: Easy
```sql
SELECT name, birthday
FROM family_members
WHERE birthday BETWEEN '2024-12-01' AND '2024-12-31';
```

### Day 12
A collector wants to identify the top 3 snow globes with the highest number of figurines. Write a query to rank them and include their globe_name, number of figurines, and material.

Question level of difficulty: Hard
```sql
SELECT globe_name,
       number_of_figurines,
       material
FROM
(SELECT sg.globe_name,
COUNT(fg.figurine_id) AS number_of_figurines,
sg.material,
RANK() OVER(ORDER BY COUNT(fg.figurine_id) DESC) AS ranking
FROM snow_globes AS sg 
LEFT JOIN figurines AS fg 
ON sg.globe_id = fg.globe_id
GROUP BY sg.globe_name) AS ranked_globes
WHERE ranking <= 3;
```

### Day 13
We need to make sure Santa’s sleigh is properly balanced. Find the total weight of gifts for each recipient.

Question level of difficulty: Medium
```sql
SELECT recipient, SUM(weight_kg) AS total_weight
FROM gifts
GROUP BY recipient;
```

### Day 14
Which ski resorts had snowfall greater than 50 inches?

Question level of difficulty: Easy
```sql
SELECT resort_name
FROM snowfall
WHERE snowfall_inches > 50;
```
### Day 15
A family reunion is being planned, and the organizer wants to identify the three family members with the most children. Write a query to calculate the total number of children for each parent and rank them. Include the parent’s name and their total number of children in the result.

Question level of difficulty: Hard
```sql
WITH children_count AS(
SELECT fm.name AS parent_name, 
COUNT(pcr.child_id) AS total_children
FROM family_members fm
LEFT JOIN parent_child_relationships pcr 
ON fm.member_id = pcr.parent_id 
WHERE fm.age > 18
GROUP BY fm.name)
SELECT parent_name, total_children
FROM children_count
ORDER BY total_children DESC
LIMIT 3;
```
### Day 16
As the owner of a candy store, you want to understand which of your products are selling best. Write a query to calculate the total revenue generated from each candy category

Question level of difficulty: Medium
```sql
SELECT category, 
       SUM(quantity_sold*price_per_unit) AS total_revenue
FROM candy_sales
GROUP BY category;
```
### Day 17
The Grinch is planning out his pranks for this holiday season. Which pranks have a difficulty level of “Advanced” or “Expert”? List the prank name and location (both in descending order).

Question level of difficulty: Easy
```sql
SELECT prank_name, location
FROM grinch_pranks
WHERE difficulty IN('Advanced', 'Expert')
ORDER BY location DESC, prank_name DESC;
```
### Day 18
A travel agency is promoting activities for a “Summer Christmas” party. They want to identify the top 2 activities based on the average rating. Write a query to rank the activities by average rating.

Question level of difficulty: Hard
```sql
SELECT activity_name
FROM(
    SELECT ac.activity_id, ac.activity_name, 
      AVG(ar.rating) AS avg_rating
FROM activities ac 
LEFT JOIN  activity_ratings ar 
ON ac.activity_id = ar.activity_id
GROUP BY ac.activity_id, ac.activity_name) AS ranking
ORDER BY avg_rating DESC
LIMIT 2;
```
### Day 19
Scientists are studying the diets of polar bears. Write a query to find the maximum amount of food (in kilograms) consumed by each polar bear in a single meal December 2024. Include the bear_name and biggest_meal_kg, and sort the results in descending order of largest meal consumed.

Question level of difficulty: Medium
```sql
SELECT pb.bear_name, 
      MAX(ml.food_weight_kg) AS biggest_meal_kg
FROM polar_bears AS pb 
INNER JOIN meal_log AS ml 
ON pb.bear_id = ml.bear_id
WHERE date BETWEEN '2024-12-01' AND '2024-12-31'
GROUP BY pb.bear_name
ORDER BY biggest_meal_kg DESC;
```
### Day 20
We are looking for cheap gifts at the market. Which vendors are selling items priced below $10? List the unique (i.e. remove duplicates) vendor names.

Question level of difficulty: Medium
```sql
SELECT DISTINCT v.vendor_name
FROM vendors v 
INNER JOIN item_prices p 
ON v.vendor_id = p.vendor_id
WHERE p.price_usd < 10;
```
### Day 21
Santa needs to optimize his sleigh for Christmas deliveries. Write a query to calculate the total weight of gifts for each recipient type (good or naughty) and determine what percentage of the total weight is allocated to each type. Include the recipient_type, total_weight, and weight_percentage in the result.

Question level of difficulty: Hard
```sql
WITH overall_weightx AS
(
SELECT SUM(weight_kg) AS overall_weight
FROM gifts
)
SELECT recipient_type, 
       SUM(weight_kg) AS total_weight,
       1.0 * SUM(weight_kg)/overall_weight AS weight_percentage  
FROM gifts g
CROSS JOIN overall_weightx w 
GROUP BY recipient_type;
```
### Day 22
We are hosting a gift party and need to ensure every guest receives a gift. Using the guests and guest_gifts tables, write a query to identify the guest(s) who have not been assigned a gift (i.e. they are not listed in the guest_gifts table).

Question level of difficulty: Medium
```sql
SELECT gs.guest_name
FROM guests gs 
WHERE gs.guest_id NOT IN(
    SELECT gg.guest_id
    FROM guest_gifts gg 
);
```
### Day 23
The Grinch tracked his weight every day in December to analyze how it changed daily. Write a query to return the weight change (in pounds) for each day, calculated as the difference from the previous day’s weight.

Question level of difficulty: Medium
```sql
SELECT day_of_month, 
weight - LAG(weight) OVER(ORDER BY log_id) AS weight_change
FROM grinch_weight_log
ORDER BY log_id;
```
### Day 24
Santa is tracking how many presents he delivers each night leading up to Christmas. He wants a running total to see how many gifts have been delivered so far on any given night. Using the deliveries table, calculate the cumulative sum of gifts delivered, ordered by the delivery date.

Question level of difficulty: Hard
```sql
SELECT
    delivery_date,
    gifts_delivered,
    SUM(gifts_delivered) OVER (ORDER BY delivery_date) AS cumulative_gifts_delivered
FROM
    deliveries
ORDER BY
    delivery_date;
```

## Reference
[SQL Advent Calendar](https://www.sqlcalendar.com/welcome)
