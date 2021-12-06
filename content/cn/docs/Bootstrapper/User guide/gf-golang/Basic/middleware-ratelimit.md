---
title: "限流拦截器"
linkTitle: "限流拦截器"
weight: 10
description: >
  启动限流拦截器。
---

## 安装
```shell script
go get github.com/rookie-ninja/rk-boot
```

## 通用选项
> 启动器包含了如下通用选项，这些选项是启动 GoFrame 服务的必要选项。

| 名字 | 描述 | 类型 | 默认值 |
| ------ | ------ | ------ | ------ |
| gf.name | GoFrame 服务名称 | string | N/A |
| gf.port | GoFrame 服务端口 | integer | nil, 服务不会启动 |
| gf.enabled | GoFrame 服务启动开关 | bool | false |
| gf.description | GoFrame 服务的描述 | string | "" |

## 限流选项
| 名字 | 描述 | 类型 | 默认值 |
| ------ | ------ | ------ | ------ |
| gf.interceptors.rateLimit.enabled | 启动限流拦截器 | boolean | false |
| gf.interceptors.rateLimit.algorithm | 限流算法， 支持 tokenBucket 和 leakyBucket | string | tokenBucket |
| gf.interceptors.rateLimit.reqPerSec | 全局限流值 | int | 0 |
| gf.interceptors.rateLimit.paths.path | 访问路径 | string | "" |
| gf.interceptors.rateLimit.paths.reqPerSec | 基于访问路径的限流值 | int | 0 |

## 快速开始
### 1.创建 boot.yaml
```yaml
---
gf:
  - name: greeter
    port: 8080
    enabled: true
    commonService:
      enabled: true          # Enable common service for testing
    interceptors:
      rateLimit:
        enabled: true
        algorithm: "leakyBucket"
        reqPerSec: 100
        paths:
          - path: "/rk/v1/healthy"
            reqPerSec: 0
```

### 2.创建 main.go
```go
package main

import (
	"context"
	"github.com/rookie-ninja/rk-boot"
)

// Application entrance.
func main() {
	// Create a new boot instance.
	boot := rkboot.NewBoot()

	// Bootstrap
	boot.Bootstrap(context.Background())

	// Wait for shutdown sig
	boot.WaitForShutdownSig(context.Background())
}
```

### 3.验证
> 发送请求

```shell script
$ curl -X GET localhost:8080/rk/v1/healthy
{
    "error":{
        "code":429,
        "status":"Too Many Requests",
        "message":"",
        "details":[
            "slow down your request"
        ]
    }
}
```

### _**Cheers**_
![](/bootstrapper/user-guide/cheers.png)