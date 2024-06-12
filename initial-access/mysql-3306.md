# MYSQL - 3306

```bash
sudo nmap 10.129.14.128 -sV -sC -p3306 --script mysql*
```

![[mysql.png]]

#### Accessing MySQL Database

To interact with a MySQL database from the command line, follow these steps:

**Login to MySQL Server**

```bash
mysql -u root -pP4SSw0rd -h 10.129.14.128
```

**List All Databases**

```sql
show databases;
select version();
```

**Select a Database**

```sql
use <database>;
```

**List All Tables in the Database**

```sql
show tables;
```

**List All Columns in a Table**

```sql
show columns from <table>; 
```

**Select All Records from a Table**

```sql
select * from <table>; 
```

**Select Records with Specific Criteria**

```sql
select * from <table> where <column> = "<string>";
```

**Verify current user**

```
select system_user();
```