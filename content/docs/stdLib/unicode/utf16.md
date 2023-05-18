+++
title = "utf16"
date = 2023-05-17T09:59:21+08:00
weight = 20
description = ""
isCJKLanguage = true
draft = false
+++
# utf16

https://pkg.go.dev/unicode/utf16@go1.20.1

​	utf16包实现了UTF-16序列的编码和解码。

## 常量 

This section is empty.

## 变量

This section is empty.

## 函数

#### func [AppendRune](https://cs.opensource.google/go/go/+/go1.20.1:src/unicode/utf16/utf16.go;l=89)  <- go1.20

``` go 
func AppendRune(a []uint16, r rune) []uint16
```

​	AppendRune函数将Unicode码点r的UTF-16编码追加到a的末尾，并返回扩展后的缓冲区。如果r不是有效的Unicode码点，则追加U+FFFD的编码。

#### func [Decode](https://cs.opensource.google/go/go/+/go1.20.1:src/unicode/utf16/utf16.go;l=105) 

``` go 
func Decode(s []uint16) []rune
```

​	Decode函数返回由UTF-16编码s表示的Unicode码点序列。

#### func [DecodeRune](https://cs.opensource.google/go/go/+/go1.20.1:src/unicode/utf16/utf16.go;l=37) 

``` go 
func DecodeRune(r1, r2 rune) rune
```

​	DecodeRune函数返回代理对的UTF-16解码。如果代理对不是有效的UTF-16代理对，则DecodeRune返回Unicode替换码点U+FFFD。

#### func [Encode](https://cs.opensource.google/go/go/+/go1.20.1:src/unicode/utf16/utf16.go;l=56) 

``` go 
func Encode(s []rune) []uint16
```

​	Encode函数返回Unicode码点序列s的UTF-16编码。

#### func [EncodeRune](https://cs.opensource.google/go/go/+/go1.20.1:src/unicode/utf16/utf16.go;l=47) 

``` go 
func EncodeRune(r rune) (r1, r2 rune)
```

​	EncodeRune函数返回给定rune的UTF-16代理对r1、r2。如果rune不是有效的Unicode码点或不需要编码，则EncodeRune返回U+FFFD、U+FFFD。

#### func [IsSurrogate](https://cs.opensource.google/go/go/+/go1.20.1:src/unicode/utf16/utf16.go;l=30) 

``` go 
func IsSurrogate(r rune) bool
```

​	IsSurrogate函数报告指定的Unicode码点是否可以出现在代理对中。

## 类型

This section is empty.