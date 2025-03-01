---
title: "自定义访问路径"
linkTitle: "自定义访问路径"
weight: 11
description: >
  如何在 grpc-gateway 中添加自定义访问路径？
---

## 概述
默认情况下，grpc-gateway 会在 GRPC 函数上面创建 Restful API。但是在某些情况下，我们希望 grpc-gateway 同时提供自定义的 Restful API，而非 GRPC 方法。

[grpc-gateway](https://grpc-ecosystem.github.io/grpc-gateway/docs/operations/inject_router/) 已经支持了此功能。

## 快速开始
- 安装

```shell script
$ go get github.com/rookie-ninja/rk-boot
$ go get github.com/rookie-ninja/rk-grpc
```

### 1.创建 boot.yaml
```yaml
---
grpc:
  - name: greeter                   # Name of grpc entry
    port: 8080                      # Port of grpc entry
    enabled: true                   # Enable grpc entry
```

### 2.创建 main.go
```go
package main

import (
	"context"
	"github.com/rookie-ninja/rk-boot"
	"github.com/rookie-ninja/rk-grpc/boot"
	"net/http"
)

// Application entrance.
func main() {
	// Create a new boot instance.
	boot := rkboot.NewBoot()

	// Bootstrap
	boot.Bootstrap(context.Background())

	// Get grpc entry with name
	grpcEntry := boot.GetEntry("greeter").(*rkgrpc.GrpcEntry)
    
    // !!!!!!
    // This codes should be located after Bootstrap()
	grpcEntry.GwMux.HandlePath("GET", "/custom", func(w http.ResponseWriter, r *http.Request, pathParams map[string]string) {
		w.Write([]byte("Custom routes!"))
	})

	// Wait for shutdown sig
	boot.WaitForShutdownSig(context.Background())
}
```

### 3.验证
```shell script
$ curl "localhost:8080/custom"
Custom routes!
```

### _**Cheers**_
![](/bootstrapper/user-guide/cheers.png)