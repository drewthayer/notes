# PostgreSQL shell
~~~
=# \l                         list dbs
=# CREATE DATABASE dbname;
=# \c dbname                  connect to db
=# \d                         list db contents
=# \d tablename               list table schema
=# DROP TABLE tablename;
=# ALTER TABLE tablename RENAME TO newtablename;
=# ctrl_d                     exit
~~~

### import csv data to postgres db
- create table with schema
~~~
=# CREATE TABLE tablename (
  id SERIAL PRIMARY KEY,      only if exists
  str_col VARCHAR(50),
  int_col INT [NOT NULL],
  bool_col BOOL,
  float_col DOUBLE PRECISION,
  numeric_col NUMERIC(scale, precision),
  <table constraint>
  );
~~~
- import csv with `\copy`
  - `=# \copy tablename FROM '/path/to/file.csv' delimiter ',' csv [header];`
  - `header` arg will read in the header if exists

### create geospatial-aware lat,lon points in a table
- `=# CREATE EXTENSION postgis      only need to do once, creates geo schema`
- add a blank geometry column
  - `=# ALTER TABLE tablename ADD COLUMN geom_field_name geometry(Point, 4326);`
  - use `geometry` datatype, advantages over `geography`
  - `SRID 4326` is WGS84 coordinate system
- populate with "latitude", "longitude" columns
  - `=# UPDATE tablename SET geom=ST_SetSRID(ST_Point(longitude, latitude), 4326);`
- set up a gist index (makes all operations much faster)
  - `=# CREATE INDEX table_idx ON table USING gist (geom_field_name);`

### misc
- back up db with pgdump `% pgdump -U <username> [-W (option for pw)] -F t (file type tar) > /path/to/backupfilename.tar`

# ogr2ogr file conversion

General structure: `ogr2ogr -f <format> <output> <input> -arg1 -arg2`

- importing file to PostgreSQL
  - `% ogr2ogr -f "PostgreSQL" PG:"dbname=rachio_aqueduct user=postgres" /Users/drewthayer/spatial-data/aqueduct-water-risk/Y2019M07D12_Aqueduct30_V01/baseline/annual/arcmap/y2019m07d12_aqueduct30_v01.gdb -skipfailures -progress -nln risk_polygons`
- file conversion: GDB to Shapefile
  - `ogr2ogr -f "ESRI Shapefile" /path/to/new/empty/dir/ /path/to/file.gdb -nln newtablename -progress`
- DB to file
  - `ogr2ogr -f "ESRI Shapefile" rel/path/to/empty/dir/ PG:"host=localhost dbname=rachio_aqueduct user=postgres password=pw" -sql "SELECT * from rachio_savings_10k" -nln rachio_savings_10k`
- args
  - show progress bar `-progress`
  - skip failures `-skipfailures`
  - new layer name `-nln newname`
  - polygons and multipolygons mixed `-nlt PROMOTE_TO_MULTI`

# mac installation
(v10.15 Catalina, with zsh shell)

__install PostgreSQL with homebrew__
- `% brew install postgresql`
- `% brew services start postgresql` start local server on machine
- `% psql postgres` enter psql terminal
- file locations
  - binary in `/usr/local/bin/`
  - data in `/usr/local/car/postgres/`

__set up pgadmin on machine__
- create postgres user with pw (required)
- in terminal, while logged in to postgres db:
  - `% createuser -s username` e.g. "postgres"
  - `% psql postgres`
  - `=# \du` list users
  - `=# ALTER USER username WITH PASSWORD 'password';`
- connect pgadmin to local dbs
  - create server --> connection tab --> host name 'localhost', port 5432, assign a name
