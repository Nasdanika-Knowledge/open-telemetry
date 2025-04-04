[Histogram](https://javadoc.io/doc/io.opentelemetry/opentelemetry-api/latest/io/opentelemetry/api/metrics/DoubleHistogram.html) is a client-side aggregation of values, such as request latencies. A histogram is a good choice if you are interested in value statistics. For example: How many requests take fewer than 1s?.
Histograms are obtained from a [Meter](../meter/index.html) as shown below:

```java
DoubleGauge histogram = meter
	.gaugeBuilder("my-histogram")
	.setDescription("My test histogram")
	.setUnit("my-unit")
	.build();			

histogram.set(25);
```