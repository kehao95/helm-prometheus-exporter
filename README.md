# helm-prometheus-exporter

## Overview

**helm-prometheus-exporter** is an generalized helm template helps you deploy most **prometheus exporters** in Kubernetes at ease. You will get all features if you have [Prometheus Operator](https://github.com/prometheus-operator/prometheus-operator) installed. But you can also go without it.

The Chart will install the following objects:

- **Deployment:**  the exporter itself, configed by env or args.
- **Service**: the exporter service exposing port named `metrics` for prometheus
- **ServiceMonitor**: (optional) defines monitoring rule for prometheus
- **PrometheusRule**: (optional) defines the prometheus alerting rules, with an default UP alert for the exporter itself.

## Usage

Get an values template from the chart

```bash
helm show values prometheus-exporter > my-exporter.yaml
```

Edit the values according to the document of the exporter you are about to install.

```yaml
exporter: 
  # the exporter image
  image: my/exporter
  tag: latest
  pullPolicy: IfNotPresent
  # the exporter's listen port 
  # eg: danielqsj/kafka-exporter has default port at 9308
  port: 9308
  # config the exporter with args or envs
  args: []
    # - --kafka.server=kafka:9092
  env: []
    # - FOO=bar

service:
  type: ClusterIP
  clusterIP: ""
  port: 80
  
# the serviceMonitor 
serviceMonitor:
  enabled: true
  interval: 30s

# the PrometheusRules
prometheusRule:
  enabled: true
  # default PrometheusName of prometheus-operator is "k8s"
  prometheusName: k8s 
  rules: 
  - alert: sample alert
    expr: up > 0
    labels:
      severity: critical
      runbookUrl: https://example.com
```

Then install it with helm to `monitoring` namespace

```bash
# install
helm install -n monitoring my-exporter prometheus-exporter -f my-exporter.yaml
# upgrade
helm upgrade -n monitoring my-exporter prometheus-exporter -f my-exporter.yaml
```

## Examples:

|exporter|exporter.image|exporter.port|
|-|-|:-:|
|[blackbox_exporter](https://github.com/prometheus/blackbox_exporter)|prom/blackbox-exporter|9115|
|[kafka-exporter](https://github.com/danielqsj/kafka_exporter)|danielqsj/kafka_exporter|9308|
|[redis_exporter](https://github.com/oliver006/redis_exporter)|oliver006/redis_exporter|9121|
|[rabbitmq-exporter](https://github.com/kbudde/rabbitmq_exporter)|kbudde/rabbitmq-exporter|9419|
|[stackdriver-exporter](https://github.com/prometheus-community/stackdriver_exporter)|prometheuscommunity/stackdriver-exporter|9255|


## Why this project?

Basically all promtheus exporters work the same way in general, which make the project possible: 

An exporter configed to extract the metric for a particular system then expose an `/metrics` endpoint for Promtheus to scrape. Then we need to add scrping config and alerting rules for prometheus to make the system work.

Project [Prometheus Operator](https://github.com/prometheus-operator/prometheus-operator) has made great progress in configuring/managing Prometheus in a Kubernetes way. So we can standardize the whole process *(deploy the exporter/config scraping/setup alerting rules)* in a generic template.