[Span](https://javadoc.io/doc/io.opentelemetry/opentelemetry-api/latest/io/opentelemetry/api/trace/Span.html) represents a unit of work or operation.
Spans can be organized in a hierarchy and also linked to form a graph.
Spans can have associated [events](../event/index.html), [exceptions](../exception/index.html), attributes.
Log records can be associated with spans.
Span context can be [propagated](../context-propagator/index.html) between execution threads and processes. 

The below code shows how to create nested spans:

```java
Tracer tracer = openTelemetry.getTracer("test-telemetry");        
Span parentSpan = tracer
	.spanBuilder("parent")
	.startSpan();

try (Scope parentScope = parentSpan.makeCurrent()) {
    		        
    Span childSpan = tracer
        	.spanBuilder("child")
        	.startSpan();
    
    try (Scope childScope = childSpan.makeCurrent()) {
		// Perform child span's work
    } finally {
    	childSpan.end();
    }    		        
} finally {
	parentSpan.end();
}
```