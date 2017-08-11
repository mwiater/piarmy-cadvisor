# piarmy-monitor

#### This is currently in progress. This notice will be removed when ready for deployment and documentation is updated. Assume unusable nonsense below.

## Notes

RUN:
docker stack deploy -c docker-stack.yml monitor

URLS:
monitor_cadvisor: `http://piarmy01:8099/containers/`

monitor_influx: `http://piarmy01:8083`

monitor_grafana: `http://piarmy01:8080/login/` (admin/admin)
