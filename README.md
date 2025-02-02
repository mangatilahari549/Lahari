S30 ETL Assignment:

Creating a python script:

1.Connect to the SQLite3 database provided

##Database Connection
import sqlite3
connection = sqlite3.connect("Company_xyz.db") #database path

2.Extract the total quantities of each item bought per customer aged 18-35
   For each customer, get the sum of each item 
   Items with no purchase(total quantity=0) should be omitted from the final list
   list-No decimal points allowed (The company doesn’t sell half of an item ;) )
   Challenge: Provide 2 solutions, one using purely SQL, the other using Pandas

Using SQL:

select 
cust.customer_id,
i.item_id,
i.item_name,
sum(coalesce(o.quantity, 0)) as total_quantity
from Customer cust
join Sales s on cust.customer_id = s.customer_id
join Orders o on s.sales_id = o.sales_id
join Items i on o.item_id = i.item_id
where c.age between 18 and 35
group by c.customer_id, i.item_id, i.item_name
having sum(coalesce(o.quantity, 0)) > 0
order by c.customer_id, i.item_id;

Using Pandas:
# loading data 

import pandas as pd
from sqlalchemy import create_engine

engine = create_engine("sqlite3://database_name.db")

df_customer = pd.read_sql('select * from customer', engine)
df_sales = pd.read_sql('select * from sales', engine)
df_orders = pd.read_sql('select * from orders', engine)
df_items = pd.read_sql('select * from items', engine)


final_df = df_customers.merge(df_sales,on = 'customer_id')\
                       .merge(df_orders,on = 'sales_id')\
                       .merge(df_items, on ='item_id')

filter_df = final_df.(final_df(col("age")).between(18, 35))

result_df = filter_df.groupby(['customer_id', 'item_id', 'item_name'])['quantity']\
    .sum()\
    .dropna()

result_df = result_df(result_df(col('quantity')) > 0)


3.Store the query to a CSV file, delimiter should be the semicolon character (';')

result_df = result_df.to_csv("file_name",sep = ';',index = False)



