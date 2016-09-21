
## Snap Plugin Go Library Examples
Here you will find example plugins that cover the basics for writing collector, processor, and publisher plugins.

Snap itself runs as a master daemon with the core functionality that may load and unload plugin processes via either CLI or HTTP APIs.

A Snap plugin is a program, or a set of functions or services, written in Go or any language; that may seamlessly integrate with Snap as executables.

Communication between Snap and plugins uses the GRPC protocol. GRPC uses HTTP2, which improves performance versus HTTP1, and uses [protocol buffers](https://developers.google.com/protocol-buffers/) for simplicity, performance, and smaller memory size.

Before starting writing Snap plugins, check out the Plugin Catalog to see if any suit your needs. If not, you need to reference the plugin packages that defines the type of structures and interfaces inside snap and then write plugin endpoints to implement the defined interfaces.


## Plugin Naming, Files, and Directory
For your plugin, create a new repository and name your plugin project using the following format:

>snap-plugin-[plugin-type]-[plugin-name]

There are three plugin types supported by Snap: collector, processor, publisher

For example: 
>snap-plugin-collector-rand


Example files and directory structure:  
```
snap-plugin-[plugin-type]-[plugin-name]
 |--[plugin-name]
  |--[plugin-name].go  
  |--[plugin-name]_test.go  
 |--main.go
```

### Mandatory package

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

## Starting a plugin

After implementing a type that satisfies one of {collector, processor, publisher} interfaces, all that is left to do is a call the appropriate plugin.StartX() with your plugin specific options. That could be as simple as:

```go
	plugin.StartCollector(rand.RandCollector{}, pluginName, pluginVersion)
```

### Meta options

The available options are defined in [plugin/meta.go](https://github.com/intelsdi-x/snap-plugin-lib-go/tree/master/v/1/plugin/meta.go). You can use some or none of the options. The options with definitions/explanations are below:

```go
// ConcurrencyCount is the max number of concurrent calls the plugin
// should take.  For example:
// If there are 5 tasks using the plugin and its concurrency count is 2,
// snapd will keep 3 plugin instances running.
// ConcurrencyCount overwrites the default (5) for a Meta's ConcurrencyCount.
func ConcurrencyCount(cc int) MetaOpt {
}

// Exclusive == true results in a single instance of the plugin running
// regardless of the number of tasks using the plugin.
// Exclusive overwrites the default (false) for a Meta's Exclusive key.
func Exclusive(e bool) MetaOpt {
}

// Unsecure results in unencrypted communication with this plugin.
// Unsecure overwrites the default (false) for a Meta's Unsecure key.
func Unsecure(e bool) MetaOpt {
}

// RoutingStrategy will override the routing strategy this plugin requires.
// The default routing strategy is Least Recently Used.
// RoutingStrategy overwrites the default (LRU) for a Meta's RoutingStrategy.
func RoutingStrategy(r router) MetaOpt {
}

// CacheTTL will override the default cache TTL for the this plugin. snapd
// caches metrics on the daemon side for a default of 500ms.
// CacheTTL overwrites the default (500ms) for a Meta's CacheTTL.
func CacheTTL(t time.Duration) MetaOpt {
}
```

An example of what using all of them would look like:

```go
	plugin.StartCollector(mypackage.Mytype{},
				pluginName,
				pluginVersion,
				plugin.ConcurrencyCount(a),
				plugin.Exclusive(b),
				plugin.Unsecure(c),
				plugin.RoutingStrategy(d),
				plugin.CacheTTL(e))
```


### Testing

You should specifiy the test size (either small, medium, or large) with the specified build tag `// +build test-size`. The default testing will run all size tests if the build tag is not specified.


For example if you want to run only small tests:
```
// +build small
// you must include at least one line between the build tag and the package name.
package rand
```

For example if you want to run small and medium tests:
```
// +build small medium

package rand
```




