# REF: https://botleg.com/stories/monitoring-docker-swarm-with-cadvisor-influxdb-and-grafana/

RUN:
docker stack deploy -c docker-stack.yml monitor

CREATE DB:
docker exec `docker ps | grep -i influx | awk '{print $1}'` influx -execute 'CREATE DATABASE cadvisor'

http://piarmy01:8080

GRAPHANA DATA SOURCE:

Name: influx
Default: checked
Type: InfluxDB

URL: http://monitor_influx:8086
Access: proxy

DB: cadvisor

# InfluxDB Config
/etc/influxdb/config.toml

# Query testing
# REF: https://docs.influxdata.com/influxdb/v0.9/query_language/schema_exploration/#explore-measurements-with-show-measurements
# REF: https://docs.influxdata.com/influxdb/v0.9/query_language/schema_exploration/#explore-series-with-show-series

SHOW TAG VALUES FROM "load_average" WITH KEY = "machine"

SHOW MEASUREMENTS WITH MEASUREMENT "load_average" WHERE "machine"="2oja5356noitbnhavj7qmgfyo"

SHOW FIELD KEYS

SHOW FIELD KEYS FROM cpu_usage_total


SHOW SERIES

SHOW SERIES FROM load_average

"2oja5356noitbnhavj7qmgfyo"

SHOW SERIES FROM load_average WHERE machine = '2oja5356noitbnhavj7qmgfyo'

SHOW MEASUREMENTS WHERE machine = '2oja5356noitbnhavj7qmgfyo'

SELECT value FROM load_average WHERE machine = '2oja5356noitbnhavj7qmgfyo'

SELECT value FROM load_average WHERE machine = '2oja5356noitbnhavj7qmgfyo' AND time > now() - 24h GROUP BY time(10m)

SELECT value FROM load_average WHERE machine = '2oja5356noitbnhavj7qmgfyo' GROUP BY time(10m)

select value from load_average where machine = '2oja5356noitbnhavj7qmgfyo' and time > now() - 1d

SELECT mean("value") FROM "cpu_usage_total"  WHERE machine = 'tmjxx4litf8p80oftysjh34o1' ORDER BY time DESC LIMIT 10

# REF: https://forums.lime-technology.com/topic/50495-scripts-for-server-monitoring-using-influx-db-and-grafana-without-telegraf-agent/
# REF: https://docs.influxdata.com/influxdb/v1.2/guides/writing_data/

# READ DATA API

curl -G 'http://piarmy01:8086/query?pretty=true' --data-urlencode "db=cadvisor" --data-urlencode "q=SELECT mean(\"value\") FROM \"cpu_usage_total\""

# COU LOAD TEST

SELECT DERIVATIVE(value, 1s) FROM cpu_usage_total WHERE time > now() - 1h

SELECT DERIVATIVE(value, 1s) / 1000000000 FROM cpu_usage_total WHERE time > now() - 1h
