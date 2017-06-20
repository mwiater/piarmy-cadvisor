# REF: https://botleg.com/stories/monitoring-docker-swarm-with-cadvisor-influxdb-and-grafana/

RUN:
docker stack deploy -c docker-stack.yml monitor

docker exec `docker ps | grep -i influx | awk '{print $1}'` influx -execute 'CREATE DATABASE cadvisor'

influxdb 10.0.1.11

InfluxDB URL should be: http://monitor_influx:8086