# YD Foods - Dashboard

## Problem Statement


The food store faces challenges in tracking and analyzing key business metrics such as sales performance, customer behavior, inventory levels, and profitability. The existing manual processes for generating reports are time-consuming, prone to errors, and lack real-time insights. This Power BI dashboard aims to provide an integrated, interactive solution for visualizing and monitoring these critical metrics, enabling better decision-making, improving operational efficiency, and enhancing profitability. The dashboard will centralize data from sales, inventory, and customer systems, allowing store managers and decision-makers to easily track trends, identify opportunities for growth, and optimize store operations.


## Steps followed 

### Step 1 : Connecting and shaping the data:
1) Connect to the Customers csv file

Make sure that headers have been promoted
Confirm that data types are accurate (Note: "customer_id" should be whole numbers, and both "customer_acct_num" and "customer_postal_code" should be text)
Add a new column named "full_name" to merge the the "first_name" and "last_name" columns, separated by a space
Create a new column named "birth_year" to extract the year from the "birthdate" column, and format as text
Create a conditional column named "has_children" which equals "N" if "total_children" = 0, otherwise "Y"



2) Connect to the Products csv file

Make sure that headers have been promoted
Confirm that data types are accurate (Note: "product_id" should be whole numbers, "product_sku" should be text), "product_retail_price" and "product_cost" should be decimal numbers)
Add a calculated column named "discount_price", equal to 90% of the original retail price
Format as a fixed decimal number, and then use the rounding tool to round to 2 digits
Replace "null" values with zeros in both the "recyclable" and "low-fat" columns


3) Connect to the Stores csv file

Make sure that headers have been promoted
Confirm that data types are accurate (Note: "store_id" and "region_id" should be whole numbers)
Add a calculated column named "full_address", by merging "store_city", "store_state", and "store_country", separated by a comma and space custom separator
Add a calculated column named "area_code", by extracting the characters before the dash ("-") in the "store_phone" field 


4) Connect to the Regions csv file

Make sure that headers have been promoted
Confirm that data types are accurate (Note: "region_id" should be whole numbers)


5) Connect to the Calendar csv file

Make sure that headers have been promoted
Use the date tools in the query editor to add the following columns:
Start of Week (starting Sunday)
Name of Day
Start of Month
Name of Month
Quarter of Year
Year


6) Connect to the Returns csv file

Name the table Returns_Date and make sure that headers have been promoted
Confirm that data types are accurate (all ID columns and quantity should be whole numbers)


7) Add a new folder in my documents named "YDFoods_Transactions", containing both the Transactions_1997 and Transactions_1998 csv files

Connect to the folder path, and choose "Edit" (vs. Combine and Edit)
Click the "Content" column header (double arrow icon) to combine the files, then remove the "Source.Name" column
Name the table "Transaction_Data", and confirm that headers have been promoted
Confirm that data types are accurate (all ID columns and quantity should be whole numbers)

8) With the exception of the two data tables, disable "Include in Report Refresh", then Close & Apply

Confirm that all 7 tables are now accessible within both the RELATIONSHIPS view and the DATA view

![snip_tables](https://github.com/user-attachments/assets/2916917c-342f-43c9-84a3-8bcfc83be095)


### Step 2 : Creating the Data Model

1) In the MODEL view, arrange tables with the lookup tables above the data tables

Assign Primary Keys for each table (Excluding the Data Tables)

Connect Transaction_Data to Customers, Products, and Stores using the assigned primary/foreign keys 

Connect Transaction_Data to Calendar using both date fields, with an inactive "stock_date" relationship

Connect Return_Data to Products, Calendar, and Stores using valid primary/foreign keys

Connect Stores to Regions as a "snowflake" schema



2) Confirm the following:

All relationships follow one-to-many cardinality, with primary keys (1) on the lookup side and foreign keys (*) on the data side

Filters are all one-way (no two-way filters)

Filter context flows "downstream" from lookup tables to data tables

Data tables are connected via shared lookup tables (not directly to each other)



3) Hide all foreign keys in both data tables from Report View, as well as "region_id" from the Stores table



4) In the DATA view:

Update "product_retail_price", "product_cost", and "discount_price" to Currency ($ English) format

In the Customers table, categorize "customer_city" as City, "customer_postal_code" as Postal Code, and "customer_country" as Country

In the Stores table, categorize "store_city" as City, "store_state" as State or Province, "store_country" as Country, and "full_address" as Address 

![Model_Schema](https://github.com/user-attachments/assets/9bcfdf42-ef01-40e4-bf47-8c87a3cce7a3)


### Step 3 : Adding DAX calculated columns and measures.

1) Calculated columns:

 - In the Calendar table, add a column named "Weekend"
Equals "Y" for Saturdays or Sundays (otherwise "N")

 - In the Calendar table, add a column named "End of Month"
Returns the last date of the current month for each row

![Callendar_columns](https://github.com/user-attachments/assets/c6b174ec-9681-47ce-8c79-98fdcfb52d85)

 - In the Customers table, add a column named "Current Age"
Calculates current customer ages using the "birthdate" column and the TODAY() function

 - In the Customers table, add a column named "Priority"
Equals "High" for customers who own homes and have Golden membership cards (otherwise "Standard")   

 - In the Customers table, add a column named "Short_Country"
Returns the first three characters of the customer country, and converts to all uppercase 

 - In the Customers table, add a column named "House Number"
Extracts all characters/numbers before the first space in the "customer_address" column 

![Customer_columns](https://github.com/user-attachments/assets/d241a1ad-8737-4dca-92fe-8f27a47db155)

 - In the Products table, add a column named "Price_Tier"
Equals "High" if the retail price is >$3, "Mid" if the retail price is >$1, and "Low" otherwise

 - In the Stores table, add a column named "Years_Since_Remodel"
Calculates the number of years between the current date (TODAY()) and the last remodel date

![Product_and_stores_columns](https://github.com/user-attachments/assets/21e2fd2a-f8ab-4763-ad23-12374dd2bd3e)


2) Define new Measures table and start creating new DAX Measures in it.

 - Create new measures named "Quantity Sold" and "Quantity Returned" to calculate the sum of quantity from each data table

 - Create new measures named "Total Transactions" and "Total Returns" to calculate the count of rows from each data table

 - Create a new measure named "Return Rate" to calculate the ratio of quantity returned to quantity sold (format as %)

 - Create a new measure named "Weekend Transactions" to calculate transactions on weekends

 - Create a new measure named "% Weekend Transactions" to calculate weekend transactions as a percentage of total transactions (format as %)

 ![Measures_part1](https://github.com/user-attachments/assets/d9c6759b-6169-4dcc-9254-a324e917162e)

 - Create new measures named "All Transactions" and "All Returns" to calculate grand total transactions and returns (regardless of filter context)

 - Create a new measure to calculate "Total Revenue" based on transaction quantity and product retail price, and format as $ 

 - Create a new measure to calculate "Total Cost" based on transaction quantity and product cost, and format as $ 

 - Create a new measure named "Total Profit" to calculate total revenue minus total cost, and format as $

 ![Measures_part2](https://github.com/user-attachments/assets/fb225d34-2445-40c1-81b2-7f83ded9678b)

 - Create a new measure to calculate "Profit Margin" by dividing total profit by total revenue calculate total revenue (format as %)

 - Create a new measure named "Unique Products" to calculate the number of unique product names in the Products table

 - Create a new measure named "YTD Revenue" to calculate year-to-date total revenue, and format as $

 - Create a new measure named "60-Day Revenue" to calculate a running revenue total over a 60-day period, and format as $

 - Create new measures named  "Last Month Transactions", "Last Month Revenue", "Last Month Profit", and "Last Month Returns"

 - Create a new measure named "Revenue Target" based on a 5% lift over the previous month revenue, and format as $

![Measures_part3](https://github.com/user-attachments/assets/8f87336b-2563-4a7e-b398-a9ec494d2268)


 ### Step 4 - Visualizing the report in dashboard.

To start with I want to mention that for this Dashboard I chose the classic "Black and Gold" color scheme with my logo in golden and canvas background in black.
All my visuals where apropriate have these two colors and I have customized tooltips for all visuals in those colors as well.
In addition I have created a custom tooltip sheet used in the "Transactions by Country visual" to provide additional information without overwhelming the users wtih too many visuals.

![Tooltips](https://github.com/user-attachments/assets/8a3b87b8-ea09-4073-b8b0-6a1d10d079f2)

1) Insert a Matrix visual to show Total Transactions, Total Profit, Profit Margin, and Return Rate by Product_Brand (on rows)

Add conditional formatting to show data bars on the Total Transactions column, and color scales on Profit Margin (White to Green) and Return Rate (White to Red)
Add a visual level Top N filter to only show the top 30 product brands, then sort descending by Total Transactions

![Matrix](https://github.com/user-attachments/assets/defa0991-6bc1-4b31-8031-25de58ecc1f8)

2) Add a KPI Card to show Total Transactions, with Start of Month as the trend axis and Last Month Transactions as the target goal

Update the title to "Current Month Transactions"
Create two more copies: one for Total Profit (vs. Last month Profit) and one for Total Returns (vs. Last Month Returns)
Update titles, and change the Returns chart to color coding to "Low is Good"

![KPI_cards](https://github.com/user-attachments/assets/85383a03-d5ad-4d63-a2ec-a501b3f6981a)

3) Add a Map visual to show Total Transactions by store city

Add a slicer for store country 
Under the "selection controls" menu in the formatting pane, activate the "Show Select All" option
Change the orientation in the "General" formatting menu to horizontal and resize to create a vertical stack (rather than a list)

![Map](https://github.com/user-attachments/assets/b1780dbe-91f2-49c3-8c72-e8a64f98e751)

4) Next to the map, add a Column chart visual to break down Total Transactions by store country

Pull in store_state and store_city beneath store_country in the "Group" field to enable drill-up and drill-down functionality

![transactions_column_chart](https://github.com/user-attachments/assets/1869c27c-96cd-45fa-8d8c-88e482db7a4d)

5) Beneath the map, add a Column Chart to show Total Revenue by week

Add a report level filter to only show data for 1998
Update the title to "Weekly Revenue Trending"

![Weekly_Revenue](https://github.com/user-attachments/assets/c487f8e7-e439-4379-a66a-68d4a5fae264)

6) In the lower right - Gauge Chart to show Total Revenue against Revenue Target as "target value"

Add a visual level Top N filter to show the latest Start of Month
Remove data labels, and update the title to "Revenue vs. Target"

![revenue_target](https://github.com/user-attachments/assets/37ece98e-c105-4173-b83c-3e121f71ab60)

7) Select the Matrix and activate the  Edit interactions option to prevent the Column charts from filtering


 
 # Report Snapshot (Power BI DESKTOP)

 
![Dashboard](https://github.com/user-attachments/assets/0bdaed41-f995-478b-bd88-3ce9652bc9fb)
