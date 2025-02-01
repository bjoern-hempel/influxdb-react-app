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

Open the project in your browser:

* http://localhost:8086 (InfluxDB backend)

> Hint: If you want to use real urls instead of using port numbers, try to use https://github.com/bjoern-hempel/local-traefik-proxy
>
> * https://www.influxdb.localhost
