

include:
* general overview of plugin-lib
* example folder
* plugin-lib-go modules
* general overview of examples folder


## About This
Here you will find example plugins that cover the basics for writing collector, processor, and publisher plugins.

## Mandatory package

There is one mandatory package every plugin must use. Your plugin should import the plugin package from *snap-plugin-lib-go* to utilize all libraries:

```
	import (
		"github.com/intelsdi-x/snap-plugin-lib-go/v1/plugin"
	)
```

### Writing a collector plugin
A Snap collector plugin collects telemetry data by communicating with the Snap daemon. A collector plugin must implement the following methods:

```    
	GetConfigPolicy() (ConfigPolicy, error)
    GetMetricTypes(Config) ([]Metric, error)
    CollectMetrics([]Metric) ([]Metric, error)
```

### Writing a processor plugin
A Snap processor plugin filters, agregates, or decorates data in the Snap pipeline. A processor plugin must implement the following methods:

```
	GetConfigPolicy() (ConfigPolicy, error)
    Process([]Metric, Config) ([]Metric, error)
```

### Writing a publisher plugin
A Snap publisher plugin is a sink in the Snap pipeline.  It publishes data into another System, completing a Workflow path. A publisher plugin must implement the following methods:

```
	GetConfigPolicy() (ConfigPolicy, error)
    Publish([]Metric, Config) error
```



## Mandatory package

There is one mandatory package every plugin must use. Your plugin should import the plugin package from *snap-plugin-lib-go* to utilize all libraries:

```
	import (
		"github.com/intelsdi-x/snap-plugin-lib-go/v1/plugin"
	)
```















