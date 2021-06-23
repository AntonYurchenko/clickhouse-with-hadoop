# Integration of Yandex Clickhouse with Apache Hadoop

## Environment
For running environment docker-compose is used
```bash
# receiving code
git clone https://github.com/AntonYurchenko/clickhouse-with-hadoop.git
cd clickhouse-with-hadoop

# start 
docker-compose up -d

# after 3-5 minutes restart clickhouse_jdbc_bridge
docker-compose restart clickhouse_jdbc_bridge
```

## Initialization of HIVE
For initialization of HIVE you can use DataGrip. Connection string is: `jdbc:hive2://localhost:10000`
```sql
CREATE DATABASE IF NOT EXISTS db;
DROP TABLE IF EXISTS db.data;
CREATE TABLE db.data (
    event_date  Date,
    text        String
)
    COMMENT 'test table'
    ROW FORMAT DELIMITED
        FIELDS TERMINATED BY '\t'
        LINES TERMINATED BY '\n'
    STORED AS textfile;

INSERT INTO db.data (event_date, text)
VALUES
    ('2021-06-09', 'text of column 1'),
    ('2021-06-10', 'text of column 2'),
    ('2021-06-11', 'text of column 3'),
    ('2021-06-12', 'text of column 4')
;

SELECT * FROM db.data;
-- +----------+----------------+
-- |event_date|text            |
-- +----------+----------------+
-- |2021-06-09|text of column 1|
-- |2021-06-10|text of column 2|
-- |2021-06-11|text of column 3|
-- |2021-06-12|text of column 4|
-- +----------+----------------+

```

## Initialization of Clickhouse
For initialization of Clickhouse you can use DataGrip. Connection string is: `jdbc:clickhouse://localhost:8123`
```sql
DROP DATABASE IF EXISTS HIVE;
CREATE DATABASE HIVE;
CREATE TABLE HIVE.db_data (
    event_date  Date,
    text        Nullable(String)
) ENGINE = JDBC('hive2', 'db', 'data');

SELECT * FROM HIVE.db_data;
-- +----------+----------------+
-- |event_date|text            |
-- +----------+----------------+
-- |2021-06-09|text of column 1|
-- |2021-06-10|text of column 2|
-- |2021-06-11|text of column 3|
-- |2021-06-12|text of column 4|
-- +----------+----------------+
```