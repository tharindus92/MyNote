# installation



```text
yum install https://download.postgresql.org/pub/repos/yum/reporpms/EL-7-x86_64/pgdg-redhat-repo-latest.noarch.rpm

# Install PostgreSQL:
yum install postgresql11
```

{% embed url="https://dbtut.com/index.php/2019/10/16/how-to-create-postgresql-repository-to-install-postgres-without-internet/" %}

pg\_dumpall -U postgres -p 5433 -h 10.10.100.28 -f s.sql

```text
export PGPASSWORD="zone@123"
pg_dumpall -U postgres -p 5433 -h 10.10.100.28 -f s.sql
```



