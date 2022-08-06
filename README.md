# Prometheus-tutorial
## Prerequisites
* `Docker`
* `Docker-compose`
* `Slack`

## Project structure
```
prometheus-tutorial
|── prometheus
|    └── alertmanager.yml
|    └── docker-compose.yml
|    └── prometheus.yml
|    └── rules.yml
|    └── grafana/provisioning/datasources
|         └── prometheus_ds.yml    
└── scripts
     └── setup.sh
```
## Tasks to accomplish
- The main idea of this project is to show how we can monitorize our infra in an easy way using `docker-compose`.
- Configure `prometheus`, `alertmanager`, `node-export` and `grafana` using `docker-compose`.
- Use `Slack` to recieve the alerts.

## How to install the tools
I have included a custom script `setup.sh` that allows you to install `docker` and `docker-compose` on `Debian`.
I recommend to download it and change it with your `username` because I have decided to add my `user` to `docker group`. It is a good practice to run docker with a user instead of as `root`.

## How to setup this project locally
- First we should download it with either `git clone` or as `.zip`.
- Then we will modify `/scripts/setup.sh` with our `username` and we will execute it.

## First task: configure the monitoring stack
- In order to do this we are gonna use `docker-compose.yml`. The whole idea of using `docker-compose` is to simplify it as much as posible because we could install all these tools as services. So where are gonna use `docker images` and `volumes` to both install and put the config files in containers.
- Once we have all ready, we just have to:

````
$ docker-compose up -d
Creating network "prometheus_default" with the default driver
Creating prometheus_nodeexporter_1 ... done
Creating prometheus_alertmanager_1 ... done
Creating prometheus_grafana_1      ... done
Creating prometheus_prometheus_1   ... done

````
- To check the status of the containers:
````
$ docker ps
CONTAINER ID   IMAGE                       COMMAND                  CREATED         STATUS         PORTS                                       NAMES
b72ca97a0667   prom/prometheus:latest      "/bin/prometheus --c…"   7 seconds ago   Up 3 seconds   0.0.0.0:9090->9090/tcp, :::9090->9090/tcp   prometheus_prometheus_1
0f2068810e7e   prom/node-exporter:latest   "/bin/node_exporter …"   7 seconds ago   Up 3 seconds   0.0.0.0:9100->9100/tcp, :::9100->9100/tcp   prometheus_nodeexporter_1
34dc1741b869   grafana/grafana:latest      "/run.sh"                7 seconds ago   Up 4 seconds   0.0.0.0:3000->3000/tcp, :::3000->3000/tcp   prometheus_grafana_1
b3851b4e662b   prom/alertmanager:latest    "/bin/alertmanager -…"   7 seconds ago   Up 3 seconds   0.0.0.0:9093->9093/tcp, :::9093->9093/tcp   prometheus_alertmanager_1
````

## Second task: configure Slack
- Now we will just have to install `Slack` and create a channel. Then we will just create a `webhook` for that channel and we will include it in `alermanager.yml`.

## To sum up
Now we can have access to all these tools with a web browser.
- To access prometheus
````
localhost:9090
````
- To access alertmanager
````
localhost:9093
````
- To access grafana
````
localhost:3000
````
Let´s explain a little bit more about how this stack works. 
- `Prometheus` --> this tool allows to scrap data from servers and stores it in time-series format.
- `Alertmanager` --> taking the data we have in Prometheus, we are able to create alerts. These alerts can be sent to programs such us `telegram`, `teams` or in this case `slack`. We can use email to send this alerts too.
- `Node-exporter` --> this is the agent in charge of exposing the metrics in the server/container for `prometheus` to scrape from. There are more exporters, for example we can use `win-exporter` if our server is a windows one.
- `Grafana` --> this software allows us to create dashboards to have all our data configured in a good looking way. It will connect to prometheus in order to do so.
- `Slack` --> if your company uses this communication tool, you can propose it as an alert reciever too. With this set up all the alerts go to a single channel but we could configure more than one. We can use tags in `rules.yml` to change the severity or even the enviroment of the alerts and we can send them to one channel or another.

## To improve
- As we can see this is a very simple project. It works but for example it only contains a couple of alerts and not a single grafana dashboard. So creating more alerts and some dashboards would be a huge improvement.
- As we have said before, we could create more alerts and tag them so we can create more channels in Slack. We could also use a "frontend" such us `alerta.io` or `karma` in order to display these alerts in we wanted to. 
- As it is, we use 4 different docker images, one for each tool. In order to improve this we could create a custom docker image and build it. Also with this custom docker image we could create a `CI pipeline` with, for example, `GitHub Actions` in order to push into a container registry.
