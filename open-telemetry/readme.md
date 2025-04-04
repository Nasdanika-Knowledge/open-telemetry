[OpenTelemetry](https://javadoc.io/doc/io.opentelemetry/opentelemetry-api/latest/io/opentelemetry/api/OpenTelemetry.html) is the entry point to telemetry functionality for [tracing](../tracer/index.html), [metrics](../meter/index.html), [logging](../logs-bridge/index.html),  and [context propagation](../context-propagator/index.html).

This page explains how to obtain an instance of OpenTelemetry using auto-configuration and configure log bridging to logback.

## Maven dependencies

```xml
<dependency>
    <groupId>io.opentelemetry</groupId>
    <artifactId>opentelemetry-sdk-extension-autoconfigure</artifactId>
    <version>1.48.0</version>
</dependency>
<dependency>
    <groupId>io.opentelemetry</groupId>
    <artifactId>opentelemetry-exporter-logging</artifactId>
    <version>1.48.0</version>
</dependency>
<dependency>
    <groupId>io.opentelemetry</groupId>
    <artifactId>opentelemetry-exporter-otlp</artifactId>
    <version>1.48.0</version>
</dependency>	
        
<dependency>
    <groupId>io.opentelemetry.instrumentation</groupId>
    <artifactId>opentelemetry-logback-appender-1.0</artifactId>
    <version>2.14.0-alpha</version>
    <scope>runtime</scope>
</dependency>
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-core</artifactId>
    <version>1.5.18</version>
</dependency>
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>1.5.18</version>
</dependency>
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-api</artifactId>
    <version>2.0.17</version>
</dependency>	
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>jul-to-slf4j</artifactId>
    <version>2.0.17</version>
</dependency>        
```

## Obtain an instance

To obtain an auto-configured instance:

```java
OpenTelemetry openTelemetry = GlobalOpenTelemetry.get();
```

By default auto-configuration is disabled. 
Set ``otel.java.global-autoconfigure.enabled`` to true to enable auto-configuration. 
Then use [Environment variables and system properties](https://opentelemetry.io/docs/languages/java/configuration/#environment-variables-and-system-properties) to configure the global instance.

This is an example of Java command line properties to configure telemetry repoting to a collector over OTLP protocol: 
``-Dotel.java.global-autoconfigure.enabled=true -Dotel.metrics.exporter=otlp -Dotel.logs.exporter=otlp -Dotel.traces.exporter=otlp -Dotel.exporter.otlp.endpoint=http://<VM external IP>:4317 -Dotel.service.name=<service name>``.

## Configure log bridging

In a modular Java application reflection has to be used to configure log bridging due to a problem with automatic module naming.
Below is configuration code. 
It shall be executed only once.

```java
Class<?> openTelemetryAppenderClass = getClass().getClassLoader().loadClass("io.opentelemetry.instrumentation.logback.appender.v1_0.OpenTelemetryAppender");
Method installMethod = openTelemetryAppenderClass.getMethod("install", OpenTelemetry.class);
installMethod.invoke(null, openTelemetry);				
```

## Nasdanika Core Telemetry

With [Nadanika Core Telemetry](https://docs.nasdanika.org/core/telemetry/index.html) module you can obtain an instance of OpenTelemetry as a service using the [Nasdanika Capability Framework](https://docs.nasdanika.org/core/capability/index.html).
The telemetry module takes care of initializing log bridging just once and also has all the above dependencies in its ``pom.xml``, so you'd need to add just one dependency.
