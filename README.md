# Exploratory data analysis of coffee shop sales data 

eda <- read.csv("coffee shop sales.csv")
View(eda)
install.packages("ggplot2", dependencies = TRUE, repos = "http://cran.rstudio.com/")
library(ggplot2)

# Let's visualize some categorical variable columns using barplots

ggplot(eda, aes(x = product_category)) +
  geom_bar(fill = "coral", colour = "black") +
  theme_minimal() +
  labs(title = "Barplot of product category", x = "Product Category", y = "Count")

![Image](https://github.com/user-attachments/assets/c62bd3d2-4cdc-4c13-b161-272cc4365bb5)

# NOTE: Coffee and Tea are the most popular categories by quantity, followed by Bakery and 
# Drinking Chocolate

# Let's look at the stores with the most count of sales

table(eda$store_location)

# NOTE: We can see that the restaurants with the most count of sales is Hell's Kitchen followed
# by Astoria and then Lower Manhattan

# Let's insert a new column to calculate Sales (Unit Price * Quantity)
# We do this using the mutate function.

install.packages("dplyr")
library(dplyr)

mutate(eda, Sales_Amount = transaction_qty * unit_price) %>% 
  View()

eda_modified <- mutate(eda, 
                       Sales_Amount = transaction_qty * unit_price)

# Let's create a Time series plot to identify trends, patterns and seasonality
install.packages("tidyverse")
library(tidyverse)
library(lubridate)

# Convert the date column to an actual date column to make it useful for analysis
eda_modified$transaction_date <- dmy(eda_modified$transaction_date)

# With the transaction date column changed to an actual date column, next extract "month" and "day" from the date using the "cut" function
eda_modified$month <- as.Date(cut(eda_modified$transaction_date, breaks = 'month'))
eda_modified$day <- as.Date(cut(eda_modified$transaction_date, breaks = 'day'))

# Next, we plot the graph of Sales by Month
ggplot(eda_modified, aes(x= month, y= Sales_Amount)) +
  geom_col(fill= "darkblue")
![Image](https://github.com/user-attachments/assets/a1bc1e7a-7c63-4a1d-80b0-6f93795e7974)

# This can also be viewed in table form using the r package "dplyr"
 # SUM OF SALES BY MONTH
  
  eda_modified %>% 
    select(Sales_Amount, month) %>% 
    group_by(month) %>% 
    summarise(total_sale = sum(Sales_Amount))  %>%
    view()

# NOTE: Plot shows sales are the highest in May and June, this may be because those are closer to the Summer months

# Taken a step further, we can look at sales by the day
# To count the sales on each day
eda_modified %>% 
  select(product_id, day) %>% 
  count(day) %>% 
  view()

# To find out what day of the week the most sales is made, we first create a new column for the days of the week again from the Lubridate package which is already loaded
eda_modified$daywk <- wday(eda_modified$day, label = TRUE, abbr = TRUE)

# Next we plot the graph for this new Day of the Week variable
eda_modified %>% 
  select(day, daywk) %>% 
  group_by(daywk) %>% 
  summarise(no_of_Sale = n()) %>% 
  view() %>% 
  ggplot(aes(x = daywk, y = no_of_Sale)) +
  geom_bar(stat = 'identity', fill = "lightblue")

![Image](https://github.com/user-attachments/assets/e8ed7948-14e2-4a94-9e1d-d53abf7fd949)

# NOTE: The highest sales are made on Thursdays and Fridays and followed by Mondays 

# NEXT: EXPLORE PRODUCTS IN COFFEE SHOP
#
# Since we did a previous analysis and found the Category with the most sales to be "Coffee", I break down Coffee to see the products under this category and their respective sales

table(CoffeeBrkDwn$product_type)
![Image](https://github.com/user-attachments/assets/7d06c965-c73c-44c3-9f0f-9a45e15382a1)
# NOTE: We see from the breakdown of the product category "Coffee" that the most cups of coffee sold was in 
# the Gourmet Brewed Coffee category followed by Barista Espresso, the least being "Premium
# Brewed Coffee"

# Next, let's check the Sum of sales for the Coffee Category by plotting a graph using ggplot

ggplot(CoffeeBrkDwn, aes(x = product_type, y = Sales_Amount)) +
  geom_col(fill = "purple")
![Image](https://github.com/user-attachments/assets/b9a7b3cd-1b5a-4b00-af03-9996d3175759)
# NOTE: Highest product sales is Barista Espresso.
# although by count the Gourmet Brewed Coffee is more

# Show the breakdown of product category according to product type(Count)
ggplot(eda_modified) +
  geom_bar(aes(x = product_category, fill = product_type)) +
  xlab("Product Category") + ylab("Count")
![Image](https://github.com/user-attachments/assets/9f12afa1-d5f0-48c4-8050-f0c8cdb135d7)

# Find out the Sales Amount by Stores, and then the percentage contribution of each store
# to Total Sales

eda_modified %>% 
  select(store_location, Sales_Amount) %>% 
  group_by(store_location) %>% 
  summarise(total_sales = sum(Sales_Amount)) %>%
  ggplot(aes(x = store_location, y = total_sales)) +
  geom_bar(stat = 'identity',color = "red", fill = "lightblue")

![Image](https://github.com/user-attachments/assets/c73fb92e-0612-4639-8905-4e37809d7d41)
# To find the % contribution of each store location to total sales

eda_modified %>% 
  group_by(store_location) %>% 
  summarise(percentage= n()/nrow(eda_modified) * 100)

