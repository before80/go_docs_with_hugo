+++
title = "zlib"
date = 2023-05-17T09:59:21+08:00
weight = 5
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++
# zlib

https://pkg.go.dev/compress/zlib@go1.20.1



Package zlib implements reading and writing of zlib format compressed data, as specified in [RFC 1950](https://rfc-editor.org/rfc/rfc1950.html).

The implementation provides filters that uncompress during reading and compress during writing. For example, to write compressed data to a buffer:

``` go 
var b bytes.Buffer
w := zlib.NewWriter(&b)
w.Write([]byte("hello, world\n"))
w.Close()
```

and to read that data back:

```
r, err := zlib.NewReader(&b)
io.Copy(os.Stdout, r)
r.Close()
```








## 常量 

[View Source](https://cs.opensource.google/go/go/+/go1.20.1:src/compress/zlib/writer.go;l=18)

``` go 
const (
	NoCompression      = flate.NoCompression
	BestSpeed          = flate.BestSpeed
	BestCompression    = flate.BestCompression
	DefaultCompression = flate.DefaultCompression
	HuffmanOnly        = flate.HuffmanOnly
)
```

These constants are copied from the flate package, so that code that imports "compress/zlib" does not also have to import "compress/flate".

## 变量

[View Source](https://cs.opensource.google/go/go/+/go1.20.1:src/compress/zlib/reader.go;l=40)

``` go 
var (
	// ErrChecksum is returned when reading ZLIB data that has an invalid checksum.
	ErrChecksum = errors.New("zlib: invalid checksum")
	// ErrDictionary is returned when reading ZLIB data that has an invalid dictionary.
	ErrDictionary = errors.New("zlib: invalid dictionary")
	// ErrHeader is returned when reading ZLIB data that has an invalid header.
	ErrHeader = errors.New("zlib: invalid header")
)
```

## 函数

#### func NewReader 

``` go 
func NewReader(r io.Reader) (io.ReadCloser, error)
```

NewReader creates a new ReadCloser. Reads from the returned ReadCloser read and decompress data from r. If r does not implement io.ByteReader, the decompressor may read more data than necessary from r. It is the caller's responsibility to call Close on the ReadCloser when done.

The ReadCloser returned by NewReader also implements Resetter.

##### NewReader Example
``` go 
package main

import (
	"bytes"
	"compress/zlib"
	"io"
	"os"
)

func main() {
	buff := []byte{120, 156, 202, 72, 205, 201, 201, 215, 81, 40, 207,
		47, 202, 73, 225, 2, 4, 0, 0, 255, 255, 33, 231, 4, 147}
	b := bytes.NewReader(buff)

	r, err := zlib.NewReader(b)
	if err != nil {
		panic(err)
	}
	io.Copy(os.Stdout, r)

	r.Close()
}
Output:

hello, world
```

#### func NewReaderDict 

``` go 
func NewReaderDict(r io.Reader, dict []byte) (io.ReadCloser, error)
```

NewReaderDict is like NewReader but uses a preset dictionary. NewReaderDict ignores the dictionary if the compressed data does not refer to it. If the compressed data refers to a different dictionary, NewReaderDict returns ErrDictionary.

The ReadCloser returned by NewReaderDict also implements Resetter.

## 类型

### type Resetter  <- go1.4

``` go 
type Resetter interface {
	// Reset discards any buffered data and resets the Resetter as if it was
	// newly initialized with the given reader.
	Reset(r io.Reader, dict []byte) error
}
```

Resetter resets a ReadCloser returned by NewReader or NewReaderDict to switch to a new underlying Reader. This permits reusing a ReadCloser instead of allocating a new one.

### type Writer 

``` go 
type Writer struct {
	// contains filtered or unexported fields
}
```

A Writer takes data written to it and writes the compressed form of that data to an underlying writer (see NewWriter).

#### func NewWriter 

``` go 
func NewWriter(w io.Writer) *Writer
```

NewWriter creates a new Writer. Writes to the returned Writer are compressed and written to w.

It is the caller's responsibility to call Close on the Writer when done. Writes may be buffered and not flushed until Close.

##### NewWriter Example
``` go 
package main

import (
	"bytes"
	"compress/zlib"
	"fmt"
)

func main() {
	var b bytes.Buffer

	w := zlib.NewWriter(&b)
	w.Write([]byte("hello, world\n"))
	w.Close()
	fmt.Println(b.Bytes())
}
Output:

[120 156 202 72 205 201 201 215 81 40 207 47 202 73 225 2 4 0 0 255 255 33 231 4 147]
```

#### func NewWriterLevel 

``` go 
func NewWriterLevel(w io.Writer, level int) (*Writer, error)
```

NewWriterLevel is like NewWriter but specifies the compression level instead of assuming DefaultCompression.

The compression level can be DefaultCompression, NoCompression, HuffmanOnly or any integer value between BestSpeed and BestCompression inclusive. The error returned will be nil if the level is valid.

#### func NewWriterLevelDict 

``` go 
func NewWriterLevelDict(w io.Writer, level int, dict []byte) (*Writer, error)
```

NewWriterLevelDict is like NewWriterLevel but specifies a dictionary to compress with.

The dictionary may be nil. If not, its contents should not be modified until the Writer is closed.

#### (*Writer) Close 

``` go 
func (z *Writer) Close() error
```

Close closes the Writer, flushing any unwritten data to the underlying io.Writer, but does not close the underlying io.Writer.

#### (*Writer) Flush 

``` go 
func (z *Writer) Flush() error
```

Flush flushes the Writer to its underlying io.Writer.

#### (*Writer) Reset  <- go1.2

``` go 
func (z *Writer) Reset(w io.Writer)
```

Reset clears the state of the Writer z such that it is equivalent to its initial state from NewWriterLevel or NewWriterLevelDict, but instead writing to w.

#### (*Writer) Write 

``` go 
func (z *Writer) Write(p []byte) (n int, err error)
```

Write writes a compressed form of p to the underlying io.Writer. The compressed bytes are not necessarily flushed until the Writer is closed or explicitly flushed.