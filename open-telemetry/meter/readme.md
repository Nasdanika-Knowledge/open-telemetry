[Meter](https://javadoc.io/doc/io.opentelemetry/opentelemetry-api/latest/io/opentelemetry/api/metrics/Meter.html) provides instruments used to record measurements:

* [Counter](../counter/index.html) - A value that accumulates over time – you can think of this like an odometer on a car; it only ever goes up. There is also an aynchronous flavor of a counter.
* [Gauge](../gauge/index.html) - Measures a current value at the time it is read. An example would be the fuel gauge in a vehicle. 
* [Historgram](../histogram/index.html) - A client-side aggregation of values, such as request latencies. A histogram is a good choice if you are interested in value statistics. For example: How many requests take fewer than 1s?
* [Up/Down Counter](../counter/index.html) - A value that accumulates over time, but can also go down again. An example could be a queue length, it will increase and decrease with the number of work items in the queue. There is also an aynchronous flavor of an up/down counter.

The below snippet shows how to obtain a meter from an OpenTelemetry instance:

```java
Meter meter = openTelemetry.getMeter(getClass().getModule().getName());
```



