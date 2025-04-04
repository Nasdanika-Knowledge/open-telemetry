[Spans](../span/index.html) can create events using ``addEvent()`` methods:

* ``addEvent(String)``
* ``addEvent(String, Attributes)``
* ``addEvent(String, Attributes, Instant)``
* ``addEvent(String, Attributes, long, TimeUnit)``
* ``addEvent(String, Instant)``
* ``addEvent(String, long, TimeUnit)``

As you can see from the above methods, an event has the following properties:

* name
* attributes
* timestamp
