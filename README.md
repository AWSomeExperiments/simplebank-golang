# go-lang-masterclass
https://www.udemy.com/course/backend-master-class-golang-postgresql-kubernetes/

## Docker commands
### To run psql console in terminal
`docker exec -it postgres12 psql -U root -d simple_bank`


## Useful links

- [Postgres Lock Monitoring - To look for blocked queries and what's blocking them](https://wiki.postgresql.org/wiki/Lock_Monitoring) <br/>
  ```
  SELECT
        a.application_name,
        l.relation::regclass,
        l.transactionid,
        l.mode,
        l.locktype,
        l.GRANTED,
        a.usename,
        a.query,
        a.pid
  FROM pg_stat_activity a
  JOIN pg_locks l ON l.pid = a.pid
  WHERE a.application_name = 'psql'
  ORDER BY a.pid;
  ```