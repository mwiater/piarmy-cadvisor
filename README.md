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