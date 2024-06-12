> [!NOTE]
> Always use `#` or `//` if `--` won't work as these are other commenting operator

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

In frontend
```
SELECT * FROM users WHERE user_name='leon'
```

In backend
```
<?php
$uname = $_POST['uname'];
$passwd =$_POST['password'];

$sql_query = "SELECT * FROM users WHERE user_name= '$uname' AND password='$passwd'";
$result = mysqli_query($con, $sql_query);
?>
```

**MySQL**

```
SELECT user, authentication_string FROM mysql.user WHERE user = 'offsec';
```

![[Pasted image 20240507120634.png]]


# MSSQL

Windows have built-in command line tool named SQLCMD

**Connect to mssql via impacket**
```
impacket-mssqlclient Administrator:Lab123@192.168.50.18 -windows-auth
```

![[Pasted image 20240507120831.png]]

![[Pasted image 20240507120900.png]]

try different types of syntax as it varies by databases

**Show tables**

```
SELECT name FROM sys.databases;
```

![[Pasted image 20240507121224.png]]

**Get info of particular table**
```
SELECT * FROM offsec.information_schema.tables;
```

![[Pasted image 20240507121303.png]]

Our query returned the _users_ table as the only one available in the database, so let's inspect it by selecting all of its records. We'll need to specify the _dbo_ table schema between the database and the table names

```
select * from offsec.dbo.users;
```

![[Pasted image 20240507121357.png]]

