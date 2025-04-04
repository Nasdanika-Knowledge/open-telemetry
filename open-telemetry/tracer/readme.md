Traces provide a big picture of how requests are processed in a software system. 
[Tracer](https://javadoc.io/doc/io.opentelemetry/opentelemetry-api/latest/io/opentelemetry/api/trace/Tracer.html) is the interface for [Span](../span/index.html) creation and interaction with the in-process context.

The below snippet shows how to obtain a tracer from an OpenTelemetry instance:

```java
Tracer tracer = openTelemetry.getTracer(getClass().getName());
```