# John's Pizzeria SQL Project: Enhancing Business Operations through Data Analysis
This SQL Project focuses on optimizing business operations for Pizzeria, a fictional pizza restaurant, through data analysis and database management. Leveraging SQL and relational database systems, our goal is to construct a tailored database solution to meet the restaurant's specific needs.

## Project Objective
Develop a customized, bespoke relational database solution tailored to the specific needs of John's new Pizzeria establishment. This database will efficiently capture and store all pertinent business data, enabling John to effectively monitor and analyze business performance through the creation of intuitive dashboards.

## Client's Brief
Main areas of focus
- Orders
- Stock Control
- Staff

## Menu provided by the client
<img width="612" alt="Screenshot 2024-04-08 at 12 28 25 AM" src="https://github.com/sameersk2k/Buffalo-Pizzeria-SQL-Project-/assets/115322069/59a1e251-146e-4759-9651-497c2c6e7b86">

## Designing normalized relational database

### Orders data requirements
- Item name
- Item price
- Quantity
- Customer name
- Delivery address

Now we are required to build database tables that store data of above features in a normalized manner which will look like this:
<img width="698" alt="Screenshot 2024-04-08 at 12 37 04 AM" src="https://github.com/sameersk2k/Buffalo-Pizzeria-SQL-Project-/assets/115322069/cea2f31c-920d-452f-bcfe-4829cc0e35ff">

### Stock control requirements
- Wants to be able to know when it's time to order new stock
- To do this we're going to need more information about:
    - what ingredients go into each pizza
    - their quantity based on the size of the pizza
    - the existing stock level
- We'll assume the lead time for delivery by suppliers is the same for all ingredients

Now that we have an idea what additional data we might need, here is how our updated database design looks like.
<img width="698" alt="Screenshot 2024-04-08 at 12 51 21 AM" src="https://github.com/sameersk2k/Buffalo-Pizzeria-SQL-Project-/assets/115322069/4e6492f2-edee-4670-b1d0-e3e16a5d7180">

### Staff data requirements
- Want to know which staff members are working when.
- Based on the staff salary information, how much each pizza costs(ingredients+chef+delivery)
Now that we have an idea what additional data we might need, here is how our updated database design looks like.
<img width="698" alt="Screenshot 2024-04-08 at 12 57 52 AM" src="https://github.com/sameersk2k/Buffalo-Pizzeria-SQL-Project-/assets/115322069/122afed1-fd0f-4d94-a3bc-450c43c11d2a">

### SQL code to create tables
``` sql
CREATE TABLE `customers` (
    `cust_id` INT NOT NULL,
    `cust_firstname` VARCHAR(50) NOT NULL,
    `cust_lastname` VARCHAR(50) NOT NULL,
    PRIMARY KEY (`cust_id`)
);
```
``` sql
CREATE TABLE `address` (
    `add_id` INT NOT NULL,
    `delivery_address1` VARCHAR(200) NOT NULL,
    `delivery_address2` VARCHAR(200),
    `delivery_city` VARCHAR(50) NOT NULL,
    `delivery_zipcode` VARCHAR(20) NOT NULL,
    PRIMARY KEY (`add_id`)
);
```
``` sql
CREATE TABLE `item` (
    `item_id` VARCHAR(10) NOT NULL,
    `sku` VARCHAR(20) NOT NULL,
    `item_name` VARCHAR(100) NOT NULL,
    `item_cat` VARCHAR(100) NOT NULL,
    `item_size` VARCHAR(10) NOT NULL,
    `item_price` DECIMAL(10,2) NOT NULL,
    PRIMARY KEY (`item_id`)
);
```
``` sql
CREATE TABLE `ingredient` (
    `ing_id` VARCHAR(10) NOT NULL,
    `ing_name` VARCHAR(200) NOT NULL,
    `ing_weight` INT NOT NULL,
    `ing_meas` VARCHAR(20) NOT NULL,
    `ing_price` DECIMAL(5,2) NOT NULL,
    PRIMARY KEY (`ing_id`)
);
```
``` sql
CREATE TABLE `recipe` (
    `row_id` INT NOT NULL,
    `recipe_id` VARCHAR(20) NOT NULL,
    `ing_id` VARCHAR(10) NOT NULL,
    `quantity` INT NOT NULL,
    PRIMARY KEY (`row_id`),
    FOREIGN KEY (`ing_id`) REFERENCES `ingredient` (`ing_id`),
    FOREIGN KEY (`recipe_id`) REFERENCES `item` (`sku`)
);
```
``` sql
CREATE TABLE `inventory` (
    `inv_id` INT NOT NULL,
    `item_id` VARCHAR(10) NOT NULL,
    `quantity` INT NOT NULL,
    PRIMARY KEY (`inv_id`),
    FOREIGN KEY (`item_id`) REFERENCES `item` (`item_id`)
);
```
``` sql
CREATE TABLE `orders` (
    `row_id` INT NOT NULL,
    `order_id` VARCHAR(10) NOT NULL,
    `created_at` DATETIME NOT NULL,
    `item_id` VARCHAR(10) NOT NULL,
    `quantity` INT NOT NULL,
    `cust_id` INT NOT NULL,
    `delivery` BOOLEAN NOT NULL,
    `add_id` INT NOT NULL,
    PRIMARY KEY (`row_id`),
    FOREIGN KEY (`cust_id`) REFERENCES `customers` (`cust_id`),
    FOREIGN KEY (`add_id`) REFERENCES `address` (`add_id`),
    FOREIGN KEY (`item_id`) REFERENCES `item` (`item_id`)
);
```
``` sql
CREATE TABLE `rota` (
    `row_id` INT NOT NULL,
    `rota_id` VARCHAR(20) NOT NULL,
    `date` DATETIME NOT NULL,
    `shift_id` VARCHAR(20) NOT NULL,
    `staff_id` VARCHAR(20) NOT NULL,
    PRIMARY KEY (`row_id`),
    FOREIGN KEY (`date`) REFERENCES `orders` (`created_at`),
    FOREIGN KEY (`staff_id`) REFERENCES `staff` (`staff_id`)
);
```
``` sql
CREATE TABLE `staff` (
    `staff_id` VARCHAR(20) NOT NULL,
    `first_name` VARCHAR(50) NOT NULL,
    `last_name` VARCHAR(50) NOT NULL,
    `position` VARCHAR(100) NOT NULL,
    `hourly_rate` DECIMAL(5,2) NOT NULL,
    PRIMARY KEY (`staff_id`)
);
```
``` sql
CREATE TABLE `shift` (
    `shift_id` VARCHAR(20) NOT NULL,
    `day_of_week` VARCHAR(10) NOT NULL,
    `start_time` TIME NOT NULL,
    `end_time` TIME NOT NULL,
    PRIMARY KEY (`shift_id`),
    FOREIGN KEY (`shift_id`) REFERENCES `rota` (`shift_id`)
);
```
Now that we have created our tables we can populated our tables by using csv files that are created using any random data generators.

## Dashboards

### Dashboard 1 - Order Activity

For this we will need the dashboard with following data
- Total orders
- Total sales
- Total items
- Average order value
- Sales by category
- Top selling items
- Orders by hour
- Sales by hour
- Orders by address
- Orders by delivery/pick up

SQL query for this data:

``` sql
SELECT
	o.order_id,
	i.item_price,
	o.quantity,
	i.item_cat,
	i.item_name,
	o.created_at,
	a.delivery_address1,
	a.delivery_address2,
	a.delivery_city,
	a.delivery_zipcode,
	o.delivery 
FROM
	orders o
	LEFT JOIN item i ON o.item_id = i.item_id
	LEFT JOIN address a ON o.add_id = a.add_id
```
### Dashboard 2 - Inventory Management

This will be a lot more complicated than the orders. Mainly because we need to calculate how much inventory we're using and then identify inventory that needs reordering.
We also want to calculate how much each pizza costs to make based on the cost of the ingredients so we can keep an eye on pricing and P/L.
Here is what we need:
- Total quantity by ingredient
- Total cost of ingredients
- Calculated cost of pizza
- Percentage stock remaining by ingredient
- List of ingredients to re-order based on remaining inventory

For the first 3 we can execute the following sql query to get the data we need:

```sql
SELECT
    s1.item_name AS item_name,
    s1.ing_name AS ing_name,
    s1.ing_id AS ing_id,
    s1.ing_weight AS ing_weight,
    s1.ing_price AS ing_price,
    s1.order_quantity AS order_quantity,
    s1.recipe_quantity AS recipe_quantity,
    (s1.order_quantity * s1.recipe_quantity) AS ordered_weight,
    (s1.ing_price / s1.ing_weight) AS unit_cost,
    ((s1.order_quantity * s1.recipe_quantity) * (s1.ing_price / s1.ing_weight)) AS ingredient_cost 
FROM
    (
    SELECT
        o.item_id AS item_id,
        i.sku AS sku,
        i.item_name AS item_name,
        r.ing_id AS ing_id,
        ing.ing_name AS ing_name,
        ing.ing_weight AS ing_weight,
        ing.ing_price AS ing_price,
        sum(o.quantity) AS order_quantity,
        r.quantity AS recipe_quantity 
    FROM
        ((orders o
        LEFT JOIN item i ON (o.item_id = i.item_id))
        LEFT JOIN recipe r ON (i.sku = r.recipe_id))
        LEFT JOIN ingredient ing ON (ing.ing_id = r.ing_id) 
    GROUP BY
        o.item_id,
        i.sku,
        i.item_name,
        r.ing_id,
        r.quantity,
        ing.ing_weight,
        ing.ing_price
    ) s1
```
To get the data for the last 2, we can use the data we got from the above sql query. To achieve this we can save the output as a view and execute below query.

``` sql
select
s2.ing_name,
s2.ordered_weight,
ing.ing_weight,
inv.quantity,
ing.ing_weight * inv.quantity AS total_inv_weight 
FROM
	( SELECT ing_id, ing_name, sum( ordered_weight ) AS ordered_weight FROM stock1 GROUP BY ing_name, ing_id ) s2
	LEFT JOIN inventory inv ON inv.item_id = s2.ing_id
	LEFT JOIN ingredient ing ON ing.ing_id = s2.ing_id
```

### Dashboard 3 - Staff

By far the simplest part of the requirements, we want to be able to monitor who was working on any given day or shift and what our overall staff costs are.
- Total staff cost
- Total hours worked
- Hours worked by staff member
- Cost per staff member

```sql
SELECT
	r.date,
	s.first_name,
	s.last_name,
	s.hourly_rate,
	sh.start_time,
	sh.end_time,
	((
			HOUR (
			timediff( sh.end_time, sh.start_time ))* 60 
			)+(
			MINUTE (
			timediff( sh.end_time, sh.start_time ))))/ 60 AS hours_in_shift,
	((
			HOUR (
			timediff( sh.end_time, sh.start_time ))* 60 
			)+(
			MINUTE (
			timediff( sh.end_time, sh.start_time ))))/ 60 * s.hourly_rate AS staff_cost 
FROM
	rota r
	LEFT JOIN staff s ON r.staff_id = s.staff_id
	LEFT JOIN shift sh ON r.shift_id = sh.shift_id
```
Now, we are done with extracting the required data. It's time for some data visualization through dashboards.

For this project, we are using Google Data Studio for the data visualization.

## Orders - Dashboard 1
<img width="958" alt="Screenshot 2024-04-08 at 3 34 14 AM" src="https://github.com/sameersk2k/Buffalo-Pizzeria-SQL-Project-/assets/115322069/b5fe328a-57c6-4199-91d1-fcbd97a8ab38">


## Inventory - Dashboard 2
<img width="966" alt="Screenshot 2024-04-08 at 3 35 36 AM" src="https://github.com/sameersk2k/Buffalo-Pizzeria-SQL-Project-/assets/115322069/71fffc5e-9cd3-4cb2-ac02-4bac9b5518c9">


# Staff - Dashboard 3
<img width="969" alt="Screenshot 2024-04-08 at 3 36 52 AM" src="https://github.com/sameersk2k/Buffalo-Pizzeria-SQL-Project-/assets/115322069/44209ca5-70ed-4c95-828a-f5ebe1bb83d4">

## Conclusion

In conclusion, this SQL project has successfully addressed the specific needs of John's Pizzeria, providing tailored solutions to optimize business operations through data analysis and database management. By constructing a customized relational database, we've facilitated efficient data storage and retrieval, enabling John to monitor and analyze key aspects of his pizzeria's performance.

Through intuitive dashboards created using Google Data Studio, we've visualized crucial metrics across various facets of the business, including order activity, inventory management, and staff operations. These dashboards provide John with valuable insights into sales trends, stock levels, staff costs, and more, empowering him to make informed decisions to drive business growth and profitability.

By leveraging SQL and relational database systems, we've laid a solid foundation for ongoing data-driven decision-making and continuous improvement within John's Pizzeria. With the ability to track and analyze critical business metrics in real-time, John is well-equipped to adapt to changing market dynamics, optimize resource allocation, and deliver exceptional experiences to his customers.

In essence, this SQL project not only meets but exceeds the client's expectations, showcasing the power of data-driven insights in enhancing business operations and driving success in the competitive restaurant industry.
