# JSON messages in InfluxDB / Grafana DEMO

This is a project demonstrating consuming json messages from rabbitmq into influxdb, with a grafana deployment for frontend.

*DO NOT JUST USE THIS IN PRODUCTION* Change default passwords, implement ssl, etc. before use.

## Getting Started

run `docker-compose up` from the root of this repository.

## Environment

This project will deploy a few containers:

### rabbitmq

this is your message queue with a default exchange `influxdb` and queue `influxq` deployed. Admin interface is enabled [here](http://localhost:15672). Username and password are default (guest/guest).

### telegraf

telegraf consumes messages from the queue using the amqp_consumer plugin, and writes to influxdb.

### influxdb

influxdb is the time series backend. authentication is disabled. The database `telegraf` and the measurement `amqp_consumer` are where the messages sent to rabbitmq will show up. infux cli is also installed on the container for easy data layer access. To query directly, run `docker exec -it influxdb influx` example:

```shell
$ docker exec -it influxdb influx
Connected to http://localhost:8086 version 1.7.7
InfluxDB shell version: 1.7.7
> use telegraf
Using database telegraf
> show measurements
name: measurements
name
----
amqp_consumer
> select * from amqp_consumer
name: amqp_consumer
time                duration host         remoteEndpoint_port timestamp
----                -------- ----         ------------------- ---------
1565577211989689900 113553   03f91f84d77d 57748               1565377374580103
```

### grafana
this is the frontend for writing queries and showing results in a pretty dashboard. You can access this [here](http://localhost:3000). Username and password are default (admin/password). One data source is created pointing to influxdb. One [sample dashboard](http://localhost:3000/d/mt6nBMdWz/test-zipkin-data-dashboard?orgId=1) is created that just does a `select * from amqp_consumer` to show you your data.

## Use for Demo

1. Publish a message to the queue using the rabbitmq admin interface. Any valid JSON string will do. Use this example as sample output from Zipkin:
```json
[{"traceId":"77c11db4a4474ad9","id":"77c11db4a4474ad9","kind":"SERVER","name":"get /v1/entities/{entityguid}/accounts","timestamp":1565377374580103,"duration":113553,"localEndpoint":{"serviceName":"disbursementprocessingservice","ipv4":"10.100.12.249"},"remoteEndpoint":{"ipv6":"::1","port":57748},"tags":{"http.method":"GET","http.path":"/v1/entities/0eb0adbd-331d-4b67-bd3e-b121d327272e/accounts","mvc.controller.class":"MerchantBankController","mvc.controller.method":"getBankAccounts"}}]
```
2. View your results in Grafana test dashboard

## Authors

- Ben Minahan - Initial work

## License

This project is licensed under the MIT License - see the [LICENSE.md](LICENSE.md) file for details
