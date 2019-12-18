# ginlogr

Alternative [gin-gonic](https://github.com/gin-gonic/gin) middleware logging through [logr](https://github.com/go-logr/logr).

The Majority of the code used here was lifted from [ginzap](https://github.com/gin-contrib/zap).

## Usage

### Start using it

Download and install it:

```sh
$ go get github.com/alron/ginlogr
$
```

Import it in your code:

```go
import "github.com/alron/ginlogr"
```

## Example

[embedmd]:#

```go
package main

import (
    "fmt"
    "time"

    "github.com/gin-gonic/gin"
    "github.com/go-logr/zapr"
    "go.uber.org/zap"
    "github.com/alron/ginlogr"
)

func main() {
    r := gin.New()
    // We use zap and zapr here, but you can really use any of the loggers
    // supported by logr
    zl, _ := zap.NewProduction()
    logger := zapr.NewLogger(zl)

    // Add a ginlogr middleware, which:
    //   - Logs all requests, like a combined access and error log.
    //   - Logs to stdout.
    //   - RFC3339 with UTC time format.
    r.Use(ginlogr.Ginlogr(logger, time.RFC3339, true))

    // Logs all panic to error log
    //   - RFC3389 with UTC time format.
    //   - stack means whether output the stack info.
    r.Use(ginlogr.RecoveryWithLogr(logger, time.RFC3339, true, true))

    // Example ping request.
    r.GET("/ping", func(c *gin.Context) {
        c.String(200, "pong "+fmt.Sprint(time.Now().Unix()))
    })

    // Example when panic happen.
    r.GET("/panic", func(c *gin.Context) {
        panic("An unexpected error happen!")
    })

    // Listen and Server in 0.0.0.0:8080
    r.Run(":8080")
}
```
