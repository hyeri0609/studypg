# studypg

pg_dump --table=export_table --data-only --column-inserts my_database > data.sql

----

http://albertech.blogspot.com/2016/11/dump-table-to-another-database-in.html

To dump a table from PostgreSQL you will often use pg_dump. This works great as long as your tables are defined exactly the same between databases.

However, if you need some variation of a table, you need something else. This can be done with good old pipes and the psql command.

Consider the following example. I have a countries table in one database, and I want to copy it more-or-less to another database, but with a couple modifications. For instance, the new table has a creation_date field whereas the other table didn't. Also we want to rename the field state_label to the more appropriate region_label. 

psql database1 -c \
  'copy (select
     code,
     calling_code,
     now() creation_date,
     currency_code,
     name,
     state_label region_label
   from countries) to stdout' | \
psql database2 -c 'copy countries from stdin'

Basically the above is a pipe between two calls to the psql command. The first one, psql database1 -c executes the SQL statement between the quotes against the first database, database1.

Note that because we're just typing a SQL command we can do things like invent a timestamp field out of thin air using the now() function. It's also easy to alias a field name like state_label to something else.

The second part above is just another psql command running some SQL, except on the second database database2.

The copy statement is significant here too, because we're sending the first psql output to STDOUT and reading from STDIN in the second psql command.
