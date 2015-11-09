## camel-metrics

The [camel-metrics](http://camel.apache.org/metrics-component.html) component allows you to define, publish and collect metrics from Camel routes.

```java
from("direct:start")
.to("metric:counter:simple.counter?increment=3")
```
