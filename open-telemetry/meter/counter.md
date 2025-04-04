[Counter](https://javadoc.io/doc/io.opentelemetry/opentelemetry-api/latest/io/opentelemetry/api/metrics/LongCounter.html) collects measurements of a value that accumulates over time – you can think of this like an odometer on a car; it only ever goes up.
Counters are obtained from a [Meter](../meter/index.html) as shown below:

```java
LongCounter counter = meter
	.counterBuilder("my-counter")
	.setDescription("My test counter")
	.setUnit("my-unit")
	.build();			

counter.add(25);
```