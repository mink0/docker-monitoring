# Docker Monitoring services
- ELK
- Sentry
- Grafana + Prometheus

## Startup
- **docker-compose.yml** - for local run and image building
  * startup command:
  ```sh
    docker-compose up
  ```

- **docker-compose.production.yml** - for start at `production01` in non-swarm mode
  * startup command:
  ```sh
    docker-compose -f docker-compose.production.yml up -d
  ```

- **docker-compose-stack.production.yml** - for start at `production01`, `production02` in swarm mode
  * startup command:
  ```sh
    docker stack deploy -c docker-compose-stack.production.yml monitoring
  ```

## Docker images have been used:
General:
* Sentry: https://github.com/getsentry/docker-sentry/tree/master/8.16
* Postgres: https://github.com/docker-library/postgres/tree/master/9.6
* Redis: https://github.com/docker-library/redis/tree/master/3.0
* Logstash: https://github.com/docker-library/logstash/tree/master/5
* Elasticsearch: https://github.com/docker-library/elasticsearch/tree/master/5
* Kibana: https://github.com/docker-library/kibana/tree/master/5
* Logspout: https://github.com/gliderlabs/logspout
* Prometheus: https://github.com/prometheus/prometheus
* Grafana: https://github.com/grafana/grafana-docker

Swarm-mode:
* Docker flow proxy: https://github.com/vfarcic/docker-flow-proxy
* Docker flow swarm listener: https://github.com/vfarcic/docker-flow-swarm-listener

Non swarm mode:
* Nginx proxy: https://github.com/jwilder/nginx-proxy

### Sentry setup

* Start containers (with `docker stack deploy`):

* Get name of sentry container

```sh
SENTRY_WEB=$(docker ps | grep monitoring_sentry-web | awk '{print $12}')
```

* Generate a new secret key if necessary

```sh
$ docker exec -it $SENTRY_WEB sentry config generate-secret-key
```

* Create sentry-env-secret file with private environments

```sh
$ echo 'SENTRY_DB_PASSWORD=sentry' > sentry-env
$ echo 'SENTRY_SECRET_KEY=sa5Jee7aejahthe0ooceipoobaidaenaer3ui5chaar8quai5b' > sentry-env-secret
```

* Initialize database schema

```sh
docker exec -it $SENTRY_WEB sentry upgrade --noinput
```

* Create superuser

```sh
docker exec -it $SENTRY_WEB sentry createuser --email admin@local --password password --superuser
```

* Set url prefix

```sh
docker exec -it $SENTRY_WEB sentry config set system.url-prefix http://sentry.local
```

## Grafana setup
Your should define Prometheus as the data source for your metrics.
- Click `Add data source` button
  - name `Prometheus`
  - type `Prometheus`
  - url `http://prometheus:9090`
  - access `proxy`
  - click `Add`

#### Install Grafana dashboards
In Grafana `Dashboards -> Import`, and then paste the JSON or ID in the required field.

Install any dashboards from here: `https://grafana.com/dashboards`

Tested dashbords:
- https://grafana.com/dashboards/179
- https://grafana.com/dashboards/405

###### If there are absent folder with Dockerfile at the current repository, then it's mean that the docker image was build from official repository without any additions and with similar name of image.
###### Example: Image name docker.local/logspout:3.2.1, then official image's name is logspout:3.2.1

