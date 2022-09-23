# Prometheus

* Yes, it has a front-end... but you won't like it!

## Introduction

* But, how can we unify all the data together? Prometheus!
* It's just :
    * Configuration to record "scrape targets"
    * A backend to 
        * grok configuration files at startup, 
        * scrape data
        * shove data into the DB
        * answer queries from DB
    * A database to store the collected data -- "time series data"
    * A GUI front-end ('cos, "people")

## Configuration

* Leave it up to Ansible
* It's really simple, this is an excerpt from: /etc/prometheus:

```
  - job_name: 'netdata-scrape'

    metrics_path: '/api/v1/allmetrics'
    params:
      format: [prometheus]
    honor_labels: true

    static_configs:
            - targets: ['mariadb-db-tst-primary-1:19999']
            - targets: ['mariadb-db-tst-secondary-1:19999']
            - targets: ['forest:19999']
            - targets: ['falkirk:19999']
            - targets: ['southampton:19999']
            - targets: ['burton:19999']
            - targets: ['bury:19999']
            - targets: ['masham:19999']
            - targets: ['marlow:19999']
            - targets: ['node-srv-tst-1:19999']
            - targets: ['node-srv-tst-2:19999']
            - targets: ['node-srv-tst-3:19999']
...snip...
```


## GUI: Query language & graphing example

* Follow my example: 
    * Click on [ITS Test Prometheus Server](http://prom-mon-test-1:9090)
    * Query: netdata_cpu_cpu_percentage_average
    * Find the "Graph" tab!
    * Notice the crazy graph!
    * Click on the first one -- notice that the graph responds
    * Run this on brechin: ```stress-ng --cpu 2 -v --timeout 30s```
    * Notice [Netdata reports it](http://brechin:19999/#menu_cpu;after=0;before=0;theme=slate;utc=America/Edmonton)
    * Back in Prometheus, execute: ```netdata_system_cpu_percentage_average{chart="system.cpu", dimension="user", family="cpu", instance="brechin:19999", job="netdata-scrape"}```
        * Keep clicking "execute" in Prometheus
    * Notice you can change the range, use mouse to select a range, etc etc
* Finally: 
    * Alerts
    * Status -> Targets
