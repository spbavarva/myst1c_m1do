> [!NOTE]
> Vulnerability that consists of an attacker interfering with the SQL
> queries that an application makes to a database.

![[Pasted image 20240429185611.png]]

`--` just comment rest part
![[Pasted image 20240429185555.png]]

it impacts CIA
C: view info
I: alter data
A: delete data

![[Pasted image 20240429185740.png]]

# Types
## In-band

Retrieved data is presented directly in the application web page

**EASY**

### error-based

Give error which will tell imp things like, version of database, particular line of code etc

![[Pasted image 20240429185941.png]]


### Union-based

leverage UNION to combine results

![[Pasted image 20240429190030.png]]

The `UNION` keyword enables you to execute one or more additional `SELECT` queries and append the results to the original query. For example:

`SELECT a, b FROM table1 UNION SELECT c, d FROM table2`

## Blind

no actual data transfer is happening with server

attacker observe the effects which every payload/request they generate and get on conclusion

as we need to do lot of things here, which is not direct so it takes longer time

### Boolean-based

uses Boolean conditions to return a different result depending on whether the
query returns a TRUE or FALSE result

![[Pasted image 20240430000544.png]]

![[Pasted image 20240430000558.png]]


### Time-based

relies on time with database

![[Pasted image 20240430004308.png]]

## out of bound

anything random
use different payload


# find SQLi

## Black box

![[Pasted image 20240430004559.png]]


## White box

![[Pasted image 20240430004619.png]]


# Exploit them

## Error based

Submit SQL-specific characters such as ' or ", and look for errors or
other anomalies

Different characters can give you different errors

## Union based

### two rules for combining the result sets of two queries by using Union:
1. The number and the order of the columns must be the same in all queries
2. The data types must be compatible

#### Exploitation
• Figure out the number of columns that the query is making
• Figure the data types of the columns (mainly interested in string data)
• Use the UNION operator to output information from the database

### Get columns

#### ORDER BY way

```
select title, cost from product where id =1 order by 1
```

Incrementally inject a series of ORDER BY clauses until you get an error or observe a
different behavior in the application

![[Pasted image 20240430005142.png]]

#### NULL VALUES way

```
select title, cost from product where id =1 UNION SELECT NULL--
```

Change value till we get **NO ERROR**

![[Pasted image 20240430005221.png]]

## Boolean based

• Submit a Boolean condition that evaluates to False and not the response
• Submit a Boolean condition that evaluates to True and note the response
• Write a program that uses conditional statements to ask the database a series of True / False questions and monitor response

## Time based

• Submit a payload that pauses the application for a specified period of time
• Write a program that uses conditional statements to ask the database a series of TRUE / FALSE questions and monitor response time

