+++
title = "rpc/jsonrpc"
date = 2023-05-17T11:11:20+08:00
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++
# jsonrpc

https://pkg.go.dev/net/rpc/jsonrpc@go1.20.1



Package jsonrpc implements a JSON-RPC 1.0 ClientCodec and ServerCodec for the rpc package. For JSON-RPC 2.0 support, see https://godoc.org/?q=json-rpc+2.0



## 常量 

This section is empty.

## 变量

This section is empty.

## 函数

#### func Dial 

``` go 
func Dial(network, address string) (*rpc.Client, error)
```

Dial connects to a JSON-RPC server at the specified network address.

#### func NewClient 

``` go 
func NewClient(conn io.ReadWriteCloser) *rpc.Client
```

NewClient returns a new rpc.Client to handle requests to the set of services at the other end of the connection.

#### func NewClientCodec 

``` go 
func NewClientCodec(conn io.ReadWriteCloser) rpc.ClientCodec
```

NewClientCodec returns a new rpc.ClientCodec using JSON-RPC on conn.

#### func NewServerCodec 

``` go 
func NewServerCodec(conn io.ReadWriteCloser) rpc.ServerCodec
```

NewServerCodec returns a new rpc.ServerCodec using JSON-RPC on conn.

#### func ServeConn 

``` go 
func ServeConn(conn io.ReadWriteCloser)
```

ServeConn runs the JSON-RPC server on a single connection. ServeConn blocks, serving the connection until the client hangs up. The caller typically invokes ServeConn in a go statement.

## 类型

This section is empty.