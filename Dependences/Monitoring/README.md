# Welcome to the area Cloud Computing wurh docker :whale:
![ReferenceImage](/images/💻 Monitoring🐋.png)

## Relationed CloudComputing
Grafana and Prometheus are monitoring and visualization tools that integrate to provide detailed insight into system performance and health. Prometheus is responsible for collecting and storing time-series metrics, while Grafana is used to visualize this data in interactive dashboards.

As in cloud computing, Grafana and Prometheus enable real-time monitoring and analysis, making it easy to observe resources and applications. In the cloud, services such as AWS CloudWatch or Google Cloud Monitoring offer similar functionalities for collecting and visualizing metrics and logs.

Scalability is another point of agreement. Grafana and Prometheus can handle large volumes of data and scale on demand, comparable to how cloud services automatically scale resources to handle varying loads.

## Monitoring With Grafana and Prometheus is being simple
![ReferenceImage](/images/GrafaP.png)

In today's world, metrics collection is mandatory in order to effective monitoring. To do so, it is quite common to use [Prometheus](https://prometheus.io) and [Grafana](https://grafana.com/docs/grafana/latest/setup-grafana/installation/docker/).

[Prometheus](https://prometheus.io) is a powerful metrics collection and alerting system, and [Grafana](https://grafana.com/docs/grafana/latest/setup-grafana/installation/docker/) is one of the best visualization tools which can be used with Prometheus.

We can create a dashboard with multiple charts together in Grafana.
## Docker-compose
first we need the images ot be able to make our compse

### *Grafana*
```
docker pull grafana/grafana
```
### Ports
3000:3000
#### [DockerHub-Image/Grafana](https://hub.docker.com/r/grafana/grafana?uuid=9E4A6F83-9251-4C93-B16E-CF90CF11B843)

### *Prometheus*

```
docker pull prom/prometheus
```


### Ports
9090:9090
#### [DockerHub-Image/Prometheus](https://hub.docker.com/r/prom/prometheus?uuid=9E4A6F83-9251-4C93-B16E-CF90CF11B843)

now we have make our compose, in order to do that we have to create a prometeus config yml which will have some basic parameters as `scrape_interval`or `crape_timeout` wich will define times to be searching for changes in the data

Then we are able to create our compose file, it has some important parameter which we want to demostrate here:
```yaml
volumes:
      - ./prometheus:/etc/prometheus
```
this lines set ups a volume local:conatainer
```yaml
networks:
      - monitoring
    depends_on:
      - prometheus
```
this other lines of code sets a network wich will be the chanel of communication bettwen the services, and the `depends_on`line sets that grafana relies in prometeus thus it will give the data to parse and use

### run the containers
```bash 
docker-compose up -d
```
if everything goes right they should be up and running, to stop them execute 
```bash
docker-compose down
```
check out the ports given above in localhost:port, in grafana you have to make some basics configurations to have your services connected, go to `connections` and `Data sources` there add the prometeus source and a config window will open, you have to set:
```
prometheus:9090
```
if you want to research in more configurations check that panel, now you have both services talking to each other.  now monitor you app!