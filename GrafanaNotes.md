# Grafana

* Visualiser of data sources
  * May be Elasticsearch, Prometheus, or some other source
* Each component on the dashboard is driven by a query.
  * A query counts the instances that match the query over the dashboards currently selected time period.
  * Components may be graphs (Which track multiple metrics), single data points, or some other types.
* There is a similar visualiser built into Kibana called Timelion.
