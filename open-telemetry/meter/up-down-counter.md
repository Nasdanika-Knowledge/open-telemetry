[Up/Down Counter](https://javadoc.io/doc/io.opentelemetry/opentelemetry-api/latest/io/opentelemetry/api/metrics/LongUpDownCounter.html) collects measurements of a value that accumulates over time, but can also go down again. An example could be a queue length, it will increase and decrease with the number of work items in the queue.
Counters are obtained from a [Meter](../meter/index.html) as shown below:

```java
LongCounter upDownCounter = meter
	.counterBuilder("my-up-downcounter")
	.setDescription("My test up/down counter")
	.setUnit("my-unit")
	.build();			

upDownCounter.add(25);
```