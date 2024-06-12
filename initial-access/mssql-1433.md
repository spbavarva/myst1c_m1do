# MSSQL - 1433

Microsoft SQL (MSSQL) is Microsoft's SQL-based relational database management system.

{% code overflow="wrap" %}
```bash
sudo nmap --script ms-sql-info,ms-sql-empty-password,ms-sql-xp-cmdshell,ms-sql-config,ms-sql-ntlm-info,ms-sql-tables,ms-sql-hasdbaccess,ms-sql-dac,ms-sql-dump-hashes --script-args mssql.instance-port=1433,mssql.username=sa,mssql.password=,mssql.instance-name=MSSQLSERVER -sV -p 1433 10.129.201.248
```
{% endcode %}

```bash
python3 mssqlclient.py Administrator@10.129.201.248 -windows-auth
```

we can run sql in their gui app

need to figure out how to run sql in MSSQL cmd
