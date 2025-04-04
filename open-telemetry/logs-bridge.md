Logs bridge enables bridging logs from other log frameworks. 
See [OpenTelemetry Log Bridging](open-telemetry/index.html#configure-log-bridging) for details about setting up a bridge.

The below snippet shows how to associate a log record with a span:

```java
Logger logger = LoggerFactory.getLogger(TestTelemetry.class);
Tracer tracer = openTelemetry.getTracer("test-telemetry");        
Span span = tracer
	.spanBuilder("parent")
	.startSpan();

try (Scope parentScope = span.makeCurrent()) {
    logger
    	.atInfo()
    	.setMessage("My test telemetry message")
    	.addKeyValue("someKey", "someValue")
    	.log();
} finally {
	span.end();
}
```