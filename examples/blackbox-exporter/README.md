## blackbox exporter

blackbox-exporter is an exception that you won't get meaningful metrics at exporter's `/metrics` endpoint. So there's no need to set up `serviceMonitor` object.  
PromtheusOperator has provided an CRD [`Probe`](https://github.com/prometheus-operator/prometheus-operator/blob/master/Documentation/api.md#probe) for you to define Probe rules against trgets using blackbox-exporter as prober.  
The example provided two `Probe` a `PrometheusRule` files for you to set up monitoring using TCP and HTTP. 