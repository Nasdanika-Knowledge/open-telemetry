[Gauge](https://javadoc.io/doc/io.opentelemetry/opentelemetry-api/latest/io/opentelemetry/api/metrics/DoubleGauge.html) measures a current value at the time it is read. An example would be the fuel gauge in a vehicle.
Gauges are obtained from a [Meter](../meter/index.html) as shown below:

```java
DoubleGauge gauge = meter
	.gaugeBuilder("my-gauge")
	.setDescription("My test gauge")
	.setUnit("my-unit")
	.build();			

gauge.set(25);
```