# benchmarksql

This is a fork of BenchmarkSQL with additionl scripts and libaraies so that it can run on PostgreSQL, MySQL and Oracle.

Currently the lastest version is 4.1.1

There is also a fork of 3.0.9 with MySQL support in the history

## how to run

Instructions for running
------------------------
Use of JDK7 is required.   Sample JDBC Connection Property files are provided as follows:
  props.pg  : for PostgreSQL/EnterpriseDB
  props.ora : for Oracle

0. As the user postgres, create the benchmarksql user with correct permissions.
	postgres=# CREATE USER benchmarksql WITH SUPERUSER PASSWORD 'password';
	postgres=# GRANT ALL PRIVILEGES ON DATABASE postgres TO benchmarksql;

1. Go to the 'run' directory, edit the appropriate "props.???"
   file to point to the database instance you'd like to test.

2. Run the "sqlTableCreates" to create the base tables.

        $ ./runSQL.sh props.pg sqlTableCreates


3. Run the Loader command file to load all of the default data
   for a benchmark:


  A.) Approximately half a million rows (per Warehouse) will be loaded
      across 9 tables.

        $ ./runLoader.sh props.pg numWarehouses 1

      NOTE: You should run the sqlTableTruncates scripts if your tables
            are not already empty.

  B.) Alternatively, for PostgreSQL you may choose to generate the
      load data out to CSV files where it can be efficiently
      bulk loaded into the database as many times as required by your
      testing.

      $ ./runLoader.sh props.pg numWarehouses 1 fileLocation /tmp/csv/

      These CSV files can be bulk loaded as follows:
        $  ./runSQL.sh props.pg sqlTableCopies

      You may truncate the data via:

        $  ./runSQL.sh props.pg sqlTableTruncates

4. Run the "runSQL" command file to execute the SQL script
   "sqlIndexCreates" to create the primary keys & other indexes
   on the tables.

        $  ./runSQL.sh props.pg sqlIndexCreates


5. Optionally run the "runSQL" command file to execute the SQL script
   "sqlExtraCreates.<dbflavor>" to make history.hist_id an auto-increment
   primary key. This column does not exist in the TPC-C standard and is
   provided for convenience of users of replication systems such as
   Bucardo or Slony-I. The benchmark itself will work with or without
   it being an actual auto-incrementing primary key.

        $  ./runSQL.sh props.pg sqlIndexCreates.pg


6. Run the "runBenchmark" command file to test the database. This command
   will create terminals and automatically start the transaction based on
   the parameters set in "props".

       $  ./runBenchmark.sh props.pg

