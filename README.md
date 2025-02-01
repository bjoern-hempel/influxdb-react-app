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

### Create new bucket

Menu:

* Load Data > Buckets > Create bucket > `climate`
* Retention Time > Older than 30 days

### InfluxDb Format

* See: https://docs.influxdata.com/influxdb/v2/reference/syntax/line-protocol/

```text
measurementName,tagKey=tagValue fieldKey="fieldValue" 1465839830100400200
--------------- --------------- --------------------- -------------------
       |               |                  |                    |
  Measurement       Tag set           Field set            Timestamp
```

### Add data

#### Via Line Protocol

* See for good schemas: https://docs.influxdata.com/influxdb/v2/write-data/best-practices/schema-design/

Menu:

* Load Data > Sources > Line Protocol > `climate` > Enter manually

```
weather,city=Dresden,country=de temperature=0.0,humidity=76,pressure=1032,visibility=11.0 1738340461698853888
weather,city=Dresden,country=de temperature=5.5,humidity=87,pressure=1026,visibility=9.9 1738344061698853888
weather,city=Dresden,country=de temperature=4.4,humidity=78,pressure=1026,visibility=10.8 1738347661698853888
weather,city=Dresden,country=de temperature=1.0,humidity=83,pressure=1031,visibility=10.3 1738351261698853888
weather,city=Dresden,country=de temperature=4.9,humidity=78,pressure=1030,visibility=9.5 1738354861698853888
weather,city=Dresden,country=de temperature=3.1,humidity=83,pressure=1035,visibility=8.7 1738358461698853888
weather,city=Dresden,country=de temperature=2.0,humidity=86,pressure=1034,visibility=8.9 1738362061698853888
weather,city=Dresden,country=de temperature=0.9,humidity=80,pressure=1033,visibility=10.2 1738365661698853888
weather,city=Dresden,country=de temperature=4.2,humidity=87,pressure=1033,visibility=10.4 1738369261698853888
weather,city=Dresden,country=de temperature=2.7,humidity=85,pressure=1031,visibility=11.1 1738372861698853888
weather,city=Dresden,country=de temperature=3.3,humidity=79,pressure=1033,visibility=10.8 1738376461698853888
weather,city=Dresden,country=de temperature=2.1,humidity=82,pressure=1030,visibility=9.6 1738380061698853888
weather,city=Dresden,country=de temperature=1.6,humidity=90,pressure=1027,visibility=8.1 1738383661698853888
weather,city=Dresden,country=de temperature=3.8,humidity=79,pressure=1031,visibility=9.0 1738387261698853888
weather,city=Dresden,country=de temperature=5.3,humidity=76,pressure=1029,visibility=11.5 1738390861698853888
weather,city=Dresden,country=de temperature=2.5,humidity=81,pressure=1028,visibility=10.7 1738394461698853888
weather,city=Dresden,country=de temperature=3.0,humidity=85,pressure=1032,visibility=9.4 1738398061698853888
weather,city=Dresden,country=de temperature=1.7,humidity=88,pressure=1034,visibility=8.2 1738401661698853888
weather,city=Dresden,country=de temperature=4.4,humidity=77,pressure=1031,visibility=10.1 1738405261698853888
weather,city=Dresden,country=de temperature=2.8,humidity=80,pressure=1032,visibility=9.8 1738408861698853888
weather,city=Dresden,country=de temperature=3.5,humidity=86,pressure=1029,visibility=9.3 1738412461698853888
weather,city=Dresden,country=de temperature=1.3,humidity=84,pressure=1032,visibility=10.9 1738416061698853888
weather,city=Dresden,country=de temperature=3.9,humidity=75,pressure=1030,visibility=11.2 1738419661698853888
weather,city=Dresden,country=de temperature=4.0,humidity=83,pressure=1031,visibility=10.0 1738423261698853888
```

#### Via http request (raw - curl)

Create API token:

* Load Data > API token > Generate API token > All Access API token

```text
QsY...amA==
```

Get organisation:

* User > About > Organization Profile

```text
docs
```

Get bucket:

* `climate`

Build curl command

```shell
curl -X POST \
--data 'weather,city=Dresden,country=de temperature=4.0,humidity=83,pressure=1031,visibility=10.0 1738423261698853888' \
-H 'Authorization: Token QsY...amA==' \
-H 'Content-Type: text/plain' \
'http://localhost:8086/api/v2/write?bucket=climate&org=docs'
```

### Query data

* See: https://docs.influxdata.com/influxdb/v1/flux/

#### Simple example: Show hourly mean aggregation of humidity and temperature

```javascript
from(bucket: "climate")
  |> range(start: v.timeRangeStart, stop: v.timeRangeStop)
  |> filter(fn: (r) => r["_measurement"] == "weather")
  |> filter(fn: (r) => r["_field"] == "humidity" or r["_field"] == "temperature")
  |> filter(fn: (r) => r["city"] == "Dresden")
  |> filter(fn: (r) => r["country"] == "de")
  |> aggregateWindow(every: 1h, fn: mean, createEmpty: false)
  |> yield(name: "mean")
```


