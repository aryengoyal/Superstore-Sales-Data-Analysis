# Superstore-Sales-Data-Analysis

In this Superstore Sales Data Analysis project, an exploratory data analysis was performed on the Superstore Sales Data available on Kaggle. The main aim of the project is to uncover insights into the store's sales and profits trends and patterns from 2014 to 2017. After cleaning and preprocessing the data, the profits trends were visualized by states, region, category, sub-category and timeline. Additionally, top-performing products and the top customers were identified. After that, the data is displayed in 3 Power BI interactive dashboards. A region focus, a category focus and a customer focused. The analysis below provided valuable insights into the Superstore's sales and profits performance and identified areas for improvement.

My analysis was carried with Excel, SQL and finally Power BI. Excel will serve as first repository for our data, SQL will give meaning to our data and Power BI will give a clear face to our data. The following sales performance analysis will follow the 6 steps of Data Analysis which are: Goals, Prepare, Process, Analyse, Visualize and Act.

## **Step 1: Goals**

In this step, we will define the business problem given to us which was interpreted as “What are the best products, regions, categories and customer segments for the Superstore to target or avoid in order to increase profitability?”

Business objectives:

How can we optimize our profits?

What are the emerging trends that we can we identify?

What are the insights we can generate to give recommendations?

## **Step 2: Prepare**

In this phase, we will identify and assess the features of our Superstore Dataset:

The data is publicly available through Kaggle under https://www.kaggle.com/datasets/vivek468/superstore-dataset-final.

It comes with 9995 rows with 9994 being pure data and the other one row being the column headers. It contains data recorded between the 3rd of January 2014 (the first order date) to the 30th of December 2017. (We will use the order dates range to represent our 4 years of business)

It contains the data of 793 customers.

The data contains the 21 columns namely; Row ID, Order ID, Order Date, Ship Date, Ship Mode, Customer ID , Customer Name, Segment, Postal Code, City, State, Country, Region, Product ID, Category, Sub-Category, Product Name, Sales, Quantity, Discount and Profit

The only limitations of our dataset that I could mention is that the most recent data point is very old. So, our data is not current. However, our data is quite reliable, original, comprehensive and is cited.

Moving on to the data processing, we will use Excel for pre-processing

## **Step 3: Process**

We will process and clean our data with the help of Excel as the file is already a CSV file so a look through of our data with Excel can be ideal to:

Observe our data

Check for missing data with the help of conditional formating

Remove duplicate rows

Correctly format columns for easy SQL analysis

While exploring our dataset, we can perform and notice the following;

Our data looks correct and consistent. Everything looks well-structured for further analysis it just needs a little editing.

With the use of conditional formating, which is a technique to highlight certain values of interest, we set our new formatting rule to be ‘Format only cells that contain’. Then we added on it to be ‘‘Format only cells with’: Blanks’. Our color of choice was yellow. Then we zoomed out to have a birds eye view of our dataset. We can confirm that our dataset contains no missing values.

With the command ‘remove duplicates’, there was no instance where the data was duplicated with all the exact parameters for a customer in all columns. So all the rows had some variety to it hence returning no duplicate data for our data set.

Finally, made sure Order Date and Ship Date were well formatted by formating it to dates (“YYYY-MM-DD” – Format). The discount column was not formated to currency as in this context, the discount is more of a percentage value. So discount will later be formatted from number to percentage.

Then to implement Normalization , we break our original table into two table namely , “Orders” and “Customers”.

The “Orders” table contains the 14 columns namely; RowID, OrderID, OrderDate,  ShipDate, ShipMode, CustomerID, ProductID, Category, SubCategory, ProductName, Sales, Quantity, Discount, Profit.

The “Customers” table contains the 8 columns namely; CustomerID, CustomerName, Segment, Country, City, State, PostalCode, Region

Now our dataset is ideal for analysis to discover relationships, trends and patterns that will give us a competitive edge and completely solve our business objectives.

## **Step 4: Analyse**

For the analysis part, we will string out the most important components of our data to answer our business objectives using SQLlite.

1. ` `**What are total sales and total profits of each year and what is the Year on growth In sales?**

The years were grouped by order date, so we can observe data for the year 2014, 2015, 2016 and 2017.

SQL query :

```Select strftime("%Y",orderdate) as Year ,sum(sales) as Total\_Sales , sum(profit) as Total\_Profit```

```FROM Orders```

```GROUP by strftime("%Y",orderdate)```

```order by strftime("%Y",orderdate) ;```

Output:

![](Aspose.Words.99692d1c-ffff-459b-8be1-684177130179.001.png)

The data above shows how the profits over the years have steadily increased with each year being more profitable than the other despite having a fall in sales in 2015.

**To get the year on growth** 

SQL query:

```DROP TABLE if EXISTS yeartable1;```

```CREATE TEMPORARY TABLE yeartable1 as ```

```SELECT strftime('%Y',Orders.OrderDate) as year, sum(sales) as total\_sales```

```from Orders```

```group by strftime('%Y',Orders.OrderDate)```

```order by orderdate```

LIMIT 4;

```SELECT b.year,b.total\_sales ,concat(round(((a.total\_sales-b.total\_sales)/a.total\_sales)\*100,2)," %") as Year\_On\_Growth```

```from yeartable1 as a ```

```right join yeartable1 as b```

```on a.year-b.year = 1;```

Output:

![](Aspose.Words.99692d1c-ffff-459b-8be1-684177130179.002.png)

The data above shows how the sales over the years have increased despite having a fall in 2015 and most significant growth in 2016 of 23.76% as compared to previous year.

2.` `** What are the total profits and total sales per quarter?**

This is done to see the periods where our company has been the most impactful. So that in the future, we can tailor our operations where we see fit like maximizing our resources like advertisement, customer service and our overall presence during those times of the year. 

SQL query:

```Select strftime('%Y',orderdate) as Year,```

```Case```

```when strftime('%m',orderdate) in ('01','02','03') THEN 'Q1'```

```when strftime('%m',orderdate) in ('04','05','06') THEN 'Q2'```

```when strftime('%m',orderdate) in ('07','08','09') THEN 'Q3'```

```ELSE 'Q4'```

```end as Ouarter,```

```sum(sales) as Total\_Sales , sum(profit) as Total\_Profit```

```from Orders```

```group by Year,Ouarter```

```Order by Year,Ouarter;```

Output:

![](Aspose.Words.99692d1c-ffff-459b-8be1-684177130179.003.png) 

![](Aspose.Words.99692d1c-ffff-459b-8be1-684177130179.004.png)

The data above shows that the period of October, November and December are our best selling months and our months where we bring in the most profit. Just by seeing this table, we can develop operation strategies pretty nicely.

Let’s get into the regions.

3. ` `**What region generates the highest sales and profits ?**

SQL query:

```Select c.region,sum(o.sales) as Total\_Sales, sum(o.profit) as Total\_Profit```

```from Customers as c```

```join orders as o```

```On c.CustomerID = o.CustomerID```

```GROUP by c.region```

```Order by Total\_Profit DESC;```

```SELECT \* from(```

```Select c.region,c.State,row\_number() over (partition by c.Region order by sum(o.Profit) desc) as Staterank,sum(o.sales) as Total\_Sales, sum(o.profit) as Total\_Profit```

```from Customers as c```

```join orders as o```

```On c.CustomerID = o.CustomerID```

```GROUP by c.region,c.State```

```Order by c.region,Total\_Profit DESC)```

```where Staterank<4;```

Output:

![](Aspose.Words.99692d1c-ffff-459b-8be1-684177130179.005.png)

![](Aspose.Words.99692d1c-ffff-459b-8be1-684177130179.006.png)

The above results shows that West & East regions are the most profitable, on the other hand it also gives us the region with lowest profitability, we can use these as data point at times when we want to increase profits. We can further deep dive into the South region, use the user journey of this user segment and try to find the possible bottlenecks, conduct user research and come up with a strategy to increase profits there, which will increase our overall profit.

Further we drill down region to see Top – 3 performing states in each region.

4. ` `**What are the Category-wise total profits and total sales?**

SQL query:

```Select category, sum(sales) as Total\_Sales, sum(profit) as Total\_Profit```

```From Orders```

```GROUP by category```
```order by Total\_Profit desc;```

```Select category,subcategory, sum(sales) as Total\_Sales, sum(profit) as Total\_Profit```

```From Orders```

```GROUP by category, subcategory```

```order by Total\_Profit desc```

```Limit 5;```

```select strftime("%Y",orderdate) as Year,category,concat(round(avg(discount),2)," %") as Average\_Discount from orders```

```group by year,category```

```order by year;```

```select strftime("%Y",orderdate) as Year,category,subcategory,concat(round(avg(discount),2)," %") as Average\_Discount from orders```

```group by year,category,subcategory```

```order by year;```

Output:

![](Aspose.Words.99692d1c-ffff-459b-8be1-684177130179.007.png) 

![](Aspose.Words.99692d1c-ffff-459b-8be1-684177130179.008.png)

From the above results, we can see that although Furniture category has more sales than Office Supplies category, it has less total\_profits as compared to the latter. We can further drill down in Furniture category and see which of its metrics is not optimal.

Next, we see the top-5 categories in terms of total\_profit. We can also interpret that Phons have the highest margins.

5. ` `**The relationship between discount and sales and the total discount per category**

SQL query:

```select strftime("%Y",orderdate) as Year,category,subcategory,concat(round(avg(discount),2)," %") as Average\_Discount , sum(sales) as Total\_Sales from orders```

```group by year,category,subcategory```

```order by year;```

Output:

![](Aspose.Words.99692d1c-ffff-459b-8be1-684177130179.009.png)

From the above result, we can get a more granular view of relation b/w Discount & Sales in terms of Year, Category and Sub-category. We can use this identify the category and sub-category in 2015 which was the most significant contributor to decline in sales in 2015. 

We interpreted that, category Office Supplies saw the highest decline of 14k dollars (approx.) from 2014 to 2015.  Under Office Supplies, we saw that “Fasteners” showed decline in sales by 115K dollars(approx.) when the discount was reduced by 2 % (approx.).

So we can interpret that if we want to increase profits from the Fasteners sub-category, reducing discount is not the way as it has a major impact on sales.

6. ` `**How are our top-10 customers in terms of profitability?**

SQL query:

```Select c.customerid,c.customername,c.State,COUNT(distinct(o.OrderID)) as Total\_Orders,sum(o.sales) as Total\_Sales,sum(o.profit) Total\_Profit```

```From orders o```

```join Customers c```

```ON o.CustomerID=c.CustomerID```

```group by o.CustomerID```

```order by Total\_Profit DESC```

```limit 10;```

Output:

![](Aspose.Words.99692d1c-ffff-459b-8be1-684177130179.010.png)

From the above result, we can see our most profitable customers and take multiple insights with us like: There is not relationship b/w number of orders and total\_sale, New York’s customers like us the most.

We can also make a rewarding referral program for our top customers as they are most likely to promote of products and also, we want to retain these customers as getting new customers is always more expensive than retaining existing ones.

7. ` `**Lets see which states have highest number of our customers**

SQL query:

```Select State,COUNT(DISTINCT(Customers.customerid)) as Total\_Customers,sum(Orders.sales) as Total\_Sales,sum(Orders.Profit) as Total\_Profit```

```from Customers```

```join orders ```

```on Customers.CustomerID=orders.CustomerID```

```Group by state```

```order by Total\_Profit DESC;```

Output:

![](Aspose.Words.99692d1c-ffff-459b-8be1-684177130179.011.png)

![](Aspose.Words.99692d1c-ffff-459b-8be1-684177130179.012.png)

From the above results, we can see that the buying capacity per customers is very different in different states like in Washington, we have only 38 customers, making a total profit of 24405 dollars, on the order hand in North Carolina, 29 customers make total losses of 4349 dollars.

So, we may want to look into North Caroline as there is some big issue there in terms of our operations.

Now, we have all the required data for our Power BI visualization.

## **Step 5: Visualization**

Onto sharing our observations, below you will find a picture of the interactive dashboard that represents the main KPIs and information on the collected Superstore data which was realized and demonstrated with Power BI.

![](Aspose.Words.99692d1c-ffff-459b-8be1-684177130179.013.png)

Dashboard 1

![](Aspose.Words.99692d1c-ffff-459b-8be1-684177130179.014.png)

Dashboard 2

![](Aspose.Words.99692d1c-ffff-459b-8be1-684177130179.015.png)

Dashboard 3

This is an in-depth analysis of the Superstore dataset.

## **Step 6: Act**

With everything that was covered, here are our conclusions and future recommendations for the success of our Superstore:

- Our profits got progressively better. Our sales too even with a short halt in 2015. We should keep the pace up on that aspect.
- Our most profitable quarter all year round was Q4. To maximize even more profits, we must make sure to have enough stock and push our marketing and customer service to make the most out of the October — December festive period.
- The most performing regions are the West then the East, South and Central regions in that order. The Central region brings in at least $100,000 more in sales than the South region but still makes less profits than it. There is work to be done in the Central region if we really want to keep that market. However, I believe it is better to take some of the resources in our Central region to instead our West region stores as we are more profitable there and could really establish ourselves as kingpin in that region.
- California, New York and Washington are our most profitable markets and most present ones especially in terms of sales as states. We have to focus more on them. Our least profitable markets are Texas, Ohio, and Pennsylvania. Which I believe that we should decrease our presence there or even put a halt at our store locations there as sales in Texas and Pennsylvania are in the $100,000s but are unable to convert to profits.
- Out of the 3 categories, Technology and Office Supplies are the best in terms of profits. Plus, they seem like a good investment because of their profit margins. Furnitures are still making profits but do not convert well in overall. With low profits and low profit margins, we should start to see what more we can bring to the furniture department. The sales are there but they do not translate smoothly.
- Still under categories but regionally, Office supplies in the West brings the most profit so we must increase the cap of those materials over there. Same thing with the East and office supplies and both the East and West with Technology. However, furniture in the Central region is the only category that doesn’t convert to profits so it would be better to take some of these resources to the West region which is the biggest gainer in terms of Furniture.
- State-wise, Technology and Office supplies brings us the most profit in the state of New York and California. We have to increase the availability of these goods in these states for better profits. However, Office supplies in Texas, Technology in Ohio and Furniture in Texas and Illinois are our biggest losses so we have to drastically reduce these types of products in those areas.
- For subcategories regionally, Copiers in the West and East with Accessories and Binders in the West are products that we always have to have in stock and promote for more profit. While Tables in the East, South and Central with furnishings in the Central region are the top products where we lose money so we should direct our attention elsewhere.
- In what concerns subcategories by state, Machines, Phones and Binders perform very well in New York. Followed by Accessories and Binders in California and Michigan respectively so there is a need to accentuate our business there with those products. For our biggest losses, Binders in Texas and Illinois with machines in Ohio are not profitable at all. We have to decrease stock in those places.
- Out of the 3 segments, the consumer segment brings in the most profit followed by Corporate and then Home office. We must give more importance to the consumer segment even if all the 3 are profitable.
- Finally, for our clientele, we have 793 customers total, and we have the most customers in California, New York and Texas. The case of North Carolina is pretty ironic since it is also the state that losses us the most money. So, we must take a critical decision about North Carolina first as we absolutely can’t break through now. California and New York are pretty obvious, we have to be outstanding and be the best of what there is to offer in our respective niche.
