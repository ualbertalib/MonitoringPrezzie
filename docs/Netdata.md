# About Netdata

* See [the netdata Website](https://www.netdata.cloud/)

## Netdata Overview, Dr. Jekyll

* Netdata is a modern novel reinvention of monitoring. It puts the metrics of your OS on the web,and makes them simple to consume in an automated fashion.
* I struggle to describe it: 
    *uh... "your os as source of metrics"?
    * maybe... "the contents of /proc/, visualized"?
* easy to install; very little to configure
* it brings your OS into the modern world of "observability" -- per-second metrics with little CPU impact
* ***Certain features require custom configuration *** -- MariaDB, PostgreSQL, HAProxy, httpd, Redis, etc are all supported, just tweak the configuration to enable

## Dr. Jekyll in your Browser

* See: [HAProxy Primary](http://telford.library.ualberta.ca:19999) & notice the "haproxy via socket"
* See: [PostgreSQL Primary](http://pg-db-prd-primary-1:19999) & notice "Postgres local"
* See: ["Big Data" Server](http://data-srv-tst-1:19999/) & notice "Aapche local & "web log apache"
* See: [DMP web server](http://dmp-web-prd-1:19999) (boring!)
* See: [Jupiter web server](http://era-app-prd-1:19999/)  (boring!)


# But there's JSON, Mr. Hyde! JSON I say!

* Hold onto your hat: 

```
[nmacgreg@itslapnm1 MonitoringPrezzie]$ curl http://telford.library.ualberta.ca:19999/api/v1/data?chart=system.cpu\&points=1\&after=-10\&options=seconds
{
 "labels": ["time","guest_nice","guest","steal","softirq","irq","user","system","nice","iowait"],
    "data":
 [
 [ 1663951010,0,0,0,0.2025641,0,1.8150424,1.7167958,0.7548301,0]
]
}
```

* [Netdata API doc](https://learn.netdata.cloud/docs/agent/web/api/queries/)
* Yes, this means you can just curl *any metric you want, for any time scale* - very powerful!

# Other Options for Reporting Metrics

* ... but there are other options! 
    * [node-exporter](https://github.com/prometheus/node_exporter) for *nix
    * [Windows exporter](https://github.com/prometheus-community/windows_exporter)


* Introduce Rails / Python modules for Application observability

## Tease: 

* "Say, wouldn't it be nice, if you could scrape ALL the metrics from ALL the servers into a single centralized database, where you could query and combine these metrics?"  
* That's [Prometheus](./Prometheus.md)!
