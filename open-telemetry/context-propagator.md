[Context propagation](https://javadoc.io/doc/io.opentelemetry/opentelemetry-context/latest/io/opentelemetry/context/propagation/ContextPropagators.html) is used to connect spans in differnet processes.
This pages explains how to propagate context between processes over HTTP and also along [reactive](https://projectreactor.io/) chains.

## Synchronous propagation

If work is performed in a single thread, then use ``Span.makeCurrent()`` method to propagate context:

```java
Tracer tracer = openTelemetry.getTracer("test-telemetry");        
Span parentSpan = tracer
	.spanBuilder("parent")
	.startSpan();

try (Scope parentScope = parentSpan.makeCurrent()) {
	Thread.sleep(200);
    Logger logger = LoggerFactory.getLogger(TestTelemetry.class);
    logger
    	.atInfo()
    	.setMessage("My test telemetry message")
    	.addKeyValue("someKey", "someValue")
    	.log();
    		        
    Span childSpan = tracer
        	.spanBuilder("child")
        	.startSpan();
    
    try (Scope childScope = childSpan.makeCurrent()) {
		Thread.sleep(300);
    } finally {
    	childSpan.end();
    }
    		        
	Thread.sleep(100);		        
} finally {
	parentSpan.end();
}
```

In the above example the log record would be associated with the parent span.
For demonstration purposes the child span is in the same method as the parent span.
However, in general, child spans would be created in methods called directly or indirectly from the scope ``try`` block.

## Propagation along a reactive chain

In reactive programming processing can be performed in multiple threads and context shall be carried along the chain.
To do so:

* Inject context with ``contextWrite()``
* Extract context with ``deferContextual()``

### contextWrite()

```java
Mono<com.azure.ai.openai.models.Embeddings> result = openAIAsyncClient
		.getEmbeddings(model, embeddingOptions)
		.contextWrite(reactor.util.context.Context.of(Context.class, Context.current().with(span)));
```

### deferContextual()

In the below code context injected with the above ``contextWrite()`` is used to set span parent/child relationship:

```java
@Override
public Mono<HttpResponse> process(HttpPipelineCallContext context, HttpPipelineNextPolicy next) {
	return Mono.deferContextual(contextView -> {
		Context parentContext = contextView.getOrDefault(Context.class, Context.current());
	
		long start = System.currentTimeMillis();
		HttpRequest request = context.getHttpRequest();
		String path = request.getUrl().toString();
		if (!Util.isBlank(path) && path.startsWith(endpoint)) {
			path = path.substring(endpoint.length());
		}
		
        Span requestSpan = tracer
	        	.spanBuilder(request.getHttpMethod().toString() + " " + path)
	        	.setSpanKind(SpanKind.CLIENT)
	        	.setParent(parentContext)
	        	.startSpan();
                
        // Trace propagation
        Context telemetryContext = Context.current().with(requestSpan);
        propagator.inject(telemetryContext, request, (rq, name, value) -> rq.setHeader(HttpHeaderName.fromString(name), value));
        
		Mono<HttpResponse> result = next.process();
		
		return 
			result
				.map(response -> {
			        try (Scope scope = requestSpan.makeCurrent()) {
			        	double duration = System.currentTimeMillis() - start;
			        	durationHistogram.record(duration / 1000);
						requestSpan.setAttribute("http.status", response.getStatusCode());
			        	requestSpan.setStatus(StatusCode.OK);
						return response;
			        }
				})
				.onErrorMap(error -> {
			        try (Scope scope = requestSpan.makeCurrent()) {
				        logger.error("Request failed: " + request.getHttpMethod() + " " + request.getUrl() , error);
			        	requestSpan.setStatus(StatusCode.ERROR);
						return error;
			        }
				}).doFinally(signal -> {
					requestSpan.setAttribute("response.thread", Thread.currentThread().getName());
		        	requestSpan.end();					
				});
	});
}
```

Please note that the above code also propagates the request span context downstream via request headers.

## Inter-process propagation

### Injection

```java
TextMapPropagator propagator = openTelemetry.getPropagators().getTextMapPropagator();
...
Context telemetryContext = Context.current().with(requestSpan);
propagator.inject(telemetryContext, request, (rq, name, value) -> rq.setHeader(HttpHeaderName.fromString(name), value));
```

### Extraction

```java
TextMapGetter<HttpServerRequest> mapper = new ExtendedTextMapGetter<HttpServerReguest>() { 

    @Override
    public Iterable<String> keys(HttpServerRequest carrier) {
    	HttpHeaders headers = carrier.requestHeaders();
    	return StreamSupport
    		.stream(headers.spliterator(), false)
    		.map(Entry::getKey)
    		.toList();
    }
    
    @Override
    public String get(HttpServerRequest carrier, String Key) {
    	HttpHeaders headers = carrier.requestHeaders());
    	return StreamSupport
    		.stream(headers.spliterator() , false)
    		.filter(e -> Objects.equals(e.getKey() , key)
    		.map(Entry::getValue)
    		.findAny()
    		.orElse(null);
    }
};

Context telemetrycontext = propagator.extract(
		Context.current(),
		request,
		mapper);

Span span = tracer
    .spanBuilder("My server span")
    .setParent(telemetryContext)
    .setSpanKind(SpanKind.SERVER)
    .startSpan();
```
