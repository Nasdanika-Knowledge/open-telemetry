This is a quick visual reference for [OpenTelemetry in Java](https://opentelemetry.io/docs/languages/java/),
which can also be used as:

* A non-linear learning resource
* A starting point for your own reference - download sources and modify to your needs.

Hover over the diagram elements for tooltips, click to navigate to documentation pages.

Some definitions on this site were copied verbatim from the [official documentation](https://opentelemetry.io/docs/) and Javadoc for the sake of consistency.

## Overview

In a JMV an [OpenTelemetry](jvm/open-telemetry/index.html) instance is used as an entry point to the telemetry functionality. 
It sends collected telemetry data to the [Collector](collector/index.html), which processes it and exports to observability [frontends/backends](front-ends/index.html).

[Tracers](jvm/tracer/index.html) are retrieved from the OpenTelemetry instance and are used to create [spans](jvm/span/index.html), which represent units of work.
Spans can be organized into a parent/child hierarchy and in a graph. 
Spans in a single trace can belong to multiple processes. 
[Context propagators](jvm/context-propagator/index.html) provide means to carry span and baggage (attributes) infomation between processes.
Spans can contain [events](jvm/event/index.html) with [exceptions](jvm/exception/index.html) being a special kind of event.

[Logs Bridge](jvm/logs-bridge/index.html) bridges open telemetry with existing logging frameworks such as [Logback](https://logback.qos.ch/), [Slf4j](https://www.slf4j.org/), [java.util.logging (JUL)](https://docs.oracle.com/en/java/javase/17/docs/api/java.logging/java/util/logging/package-summary.html), and [Log4j](https://logging.apache.org/log4j/2.x/index.html).
Log records can be associated with spans.

[Meters](jvm/meter/index.html) are also retrieved from the OpenTelemetry instance and are used to create metric instruments - 
[counters](jvm/counter/index.html), [gauges](jvm/gauge/index.html), [histograms](jvm/histogram/index.html), and [up/down counters](jvm/up-down-counter/index.html).
Metric instruments are used to collect measurements.

## Javadoc

* [API](https://javadoc.io/doc/io.opentelemetry/opentelemetry-api)
* [Context](https://javadoc.io/doc/io.opentelemetry/opentelemetry-context)
* [SDK](https://javadoc.io/doc/io.opentelemetry/opentelemetry-sdk) - for programmatic configuration

## Site generation

This sources for this site was created from the [Drawio Site Template](https://nasdanika-templates.github.io/drawio-site/). 
The site is generated using [html-app command](https://docs.nasdanika.org/nsd-cli/nsd/drawio/html-app/index.html#diagram-element-configuration) invoked from a [GitHub Action](https://github.com/features/actions). 

### Resources

* [Beyond Diagrams](https://leanpub.com/beyond-diagrams) book, including the sample book
* [Visual communication continuum story](https://medium.com/nasdanika/visual-communication-continuum-4946f44ba853)
