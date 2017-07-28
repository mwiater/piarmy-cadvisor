# REF: https://botleg.com/stories/monitoring-docker-swarm-with-cadvisor-influxdb-and-grafana/

RUN:
`docker stack deploy -c docker-stack.yml monitor`

CREATE DB:
```
docker exec `docker ps | grep -i influx | awk '{print $1}'` influx -execute 'CREATE DATABASE cadvisor'
```

http://piarmy01:8080

# CAdvisor
http://piarmy01:8099/

## API v2
## REF: https://github.com/google/cadvisor/blob/master/docs/api_v2.md
http://piarmy01:8099/api/v2.0

```
appmetrics
attributes
events
machine
machinestats
ps
spec
stats
storage
summary
version
```

## Basic Examples:
# REF: https://github.com/google/cadvisor/blob/master/docs/api_v2.md
```
http://piarmy01:8099/api/v2.1/version
http://piarmy01:8099/api/v2.1/attributes       # num_cores is here
http://piarmy01:8099/api/v2.1/machine          # num_cores is here
http://piarmy01:8099/api/v2.1/machinestats
http://piarmy01:8099/api/v2.1/ps
http://piarmy01:8099/api/v2.1/spec
http://piarmy01:8099/api/v2.1/storage
```

# Advanced Examples:
http://piarmy01:8099/api/v2.1/stats?type=docker&recursive=true

## API v1.3
## REF: https://github.com/google/cadvisor/blob/master/docs/api.md
http://piarmy01:8099/api/v1.3

```
containers
docker
events
machine
subcontainers
```

## Basic Examples (v1.3):
# REF: 
```
http://piarmy01:8099/api/v1.3/subcontainers
http://piarmy01:8099/api/v1.3/docker
http://piarmy01:8099/api/v1.3/containers/docker/b349db18b8abae5cd305abf98f230c024feeb9c7f6aeee0558b7f9662e752f30
```

http://monitor_cadvisor.sv97z72mx5zmvpdx5npmkyh2u.r9h2nbj18lye3ok921rb5stgc:8099/api/v1.3/subcontainers

# From inside container to another container on SAME host
sudo wget http://monitor_cadvisor.sv97z72mx5zmvpdx5npmkyh2u.r9h2nbj18lye3ok921rb5stgc:8080/api/v1.3/subcontainers

# From inside container to another container on DIFFERENT host
sudo wget http://monitor_cadvisor.54z6afprdrp77pcnqc3f10t4v.cfrs9jlk52q575233q23ic6bw:8080/api/v1.3/subcontainers

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

# Show series data by docker node id
SHOW SERIES FROM load_average WHERE machine = 'sv97z72mx5zmvpdx5npmkyh2u'

# List captured measurements by docker node id
SHOW MEASUREMENTS WHERE machine = 'sv97z72mx5zmvpdx5npmkyh2u'

```
cpu_usage_per_cpu
cpu_usage_system
cpu_usage_total
cpu_usage_user
fs_limit
fs_usage
load_average
memory_usage
memory_working_set
rx_bytes
rx_errors
tx_bytes
tx_errors
```

# Show load_average values by docker node id
SELECT value FROM load_average WHERE machine = 'sv97z72mx5zmvpdx5npmkyh2u'

# Limit to last 24 hours
SELECT value FROM load_average WHERE machine = 'sv97z72mx5zmvpdx5npmkyh2u' AND time > now() - 24h

# Limit to last 1 hour # Showing all zeros for some reason
SELECT value FROM load_average WHERE machine = 'sv97z72mx5zmvpdx5npmkyh2u' AND time > now() - 1h

# Testing with memory_usage to see if stats appear # Working
SELECT value FROM memory_usage WHERE machine = 'sv97z72mx5zmvpdx5npmkyh2u' AND time > now() - 1h

# Testing with cpu_usage_total to see if stats appear # Working
SELECT value FROM cpu_usage_total WHERE machine = 'sv97z72mx5zmvpdx5npmkyh2u' AND time > now() - 1h

# Trying to parse cpu_total_usage into percent
# REF: https://github.com/google/cadvisor/issues/1232
SELECT DERIVATIVE(value, 1s) / 1000000000 FROM cpu_usage_total WHERE machine = 'sv97z72mx5zmvpdx5npmkyh2u' AND time > now() - 1h


SHOW TAG VALUES FROM "load_average" WITH KEY = "machine"

SHOW FIELD KEYS

SHOW FIELD KEYS FROM cpu_usage_total


SHOW SERIES

SHOW SERIES FROM load_average



SELECT value FROM load_average WHERE machine = 'sv97z72mx5zmvpdx5npmkyh2u'

SELECT value FROM load_average WHERE machine = 'sv97z72mx5zmvpdx5npmkyh2u' AND time > now() - 24h GROUP BY time(10m)

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