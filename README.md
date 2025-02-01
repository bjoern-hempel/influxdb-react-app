# InfluxDB ReactJS App

## Start InfluxDB Server

Copy credential files:

```shell
cp .env.influxdb2-admin-password.dist .env.influxdb2-admin-password
cp .env.influxdb2-admin-username.dist .env.influxdb2-admin-username
cp .env.influxdb2-admin-token.dist .env.influxdb2-admin-token
```

Customize these as you wish.

Start the InfluxDB Server:

```shell
docker compose up -d
```

Find out the InfluxDB Version:

```shell
docker compose exec influxdb2 influxd version
```

```shell
InfluxDB v2.7.11 (git: fbf5d4ab5e) build_date: 2024-12-02T17:48:15Z
```

Open the project in your browser:

* http://localhost:8086 (InfluxDB backend)

> Hint: If you want to use real urls instead of using port numbers, try to use https://github.com/bjoern-hempel/local-traefik-proxy
>
> * https://www.influxdb.localhost

## InfluxDB Terminology

| Term               | Description                                                                                                    |
|--------------------|----------------------------------------------------------------------------------------------------------------|
| **Bucket**         | A named storage container in InfluxDB that holds time-series data. Each bucket has a defined retention period. |
| **Retention Time** | The duration for which data is stored in a bucket before being automatically deleted.                          |
| **Measurement**    | A logical grouping of time-series data, similar to a table in relational databases.                            |
| **Field**          | A key-value pair representing a metric or recorded value. Fields store actual data and are not indexed.        |
| **Value**          | The actual recorded data associated with a field, such as numbers, strings, or booleans.                       |
| **Tag**            | A key-value pair used for metadata to organize and filter data efficiently. Tags are indexed for fast queries. |
| **Point**          | A single data record in InfluxDB that consists of a measurement, timestamp, tags, and fields.                  |
| **Schema**         | The design of how data is structured within InfluxDB, including measurements, fields, and tags.                |
| **Task**           | A scheduled automation script that executes Flux queries to process or transform data periodically.            |
| **Aggregation**    | A function that summarizes data over a time range, such as `mean()`, `sum()`, `min()`, `max()`, or `count()`.  |

## First manual entries

Tbd.
