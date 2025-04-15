[Collector](https://opentelemetry.io/docs/collector/) receives, processes, and exports telemetry data.

## Container image in GCP Cloud 

One way to quickly start with OpenTelemetry is to run a collector with a front end as a Docker container.

The below instructions explain how to start an OpenTelemetry collector with [Grafana](https://grafana.com/oss/opentelemetry/) in [Google Cloud](https://cloud.google.com/):

* Open the [Console](https://console.cloud.google.com/)
* [Create a VM](https://console.cloud.google.com/compute/instancesAdd)
* Enable HTTP
* Modify the firewall HTTP rule by adding ports ``3000``, ``4317``, and ``4318``
* Start the VM and connect to it over SSH
* ``sudo bash`` - become a root
* ``apt install podman``
*  ``podman pull docker.io/grafana/otel-lgtm``
*  ``podman run -p 3000:3000 -p 4317:4317 -p 4318:4318 grafana/otel-lgtm``. See [docker-otel-lgtm](https://github.com/grafana/docker-otel-lgtm/) GitHub repository for details.
* After you restart the VM:
    *  ``podman ps -a``, copy container ID or name 
    *   ``podman start <container name or ID>``
* In your auto-configured Java applications add ``-Dotel.java.global-autoconfigure.enabled=true -Dotel.metrics.exporter=otlp -Dotel.logs.exporter=otlp -Dotel.traces.exporter=otlp -Dotel.exporter.otlp.endpoint=http://<VM external IP>:4317 -Dotel.service.name=<service name>`` properties to the command line. You can also use environment variables. See [Environment variables and system properties](https://opentelemetry.io/docs/languages/java/configuration/#environment-variables-and-system-properties) for more details.   
* Open ``http://<VM external IP>:3000`` in a web browser

## Grafana Cloud

Another option, out of many, is to use [Grafana Cloud](https://grafana.com/products/cloud). 
You would have to install [Grafana Alloy](https://grafana.com/oss/alloy-opentelemetry-collector/) on your machine.

