+++
title = "rand"
date = 2023-05-17T11:11:20+08:00
description = ""
isCJKLanguage = true
draft = false
+++
# rand

https://pkg.go.dev/math/rand@go1.20.1



Package rand implements pseudo-random number generators unsuitable for security-sensitive work.

Random numbers are generated by a [Source](https://pkg.go.dev/math/rand@go1.20.1#Source), usually wrapped in a [Rand](https://pkg.go.dev/math/rand@go1.20.1#Rand). Both types should be used by a single goroutine at a time: sharing among multiple goroutines requires some kind of synchronization.

Top-level functions, such as [Float64](https://pkg.go.dev/math/rand@go1.20.1#Float64) and [Int](https://pkg.go.dev/math/rand@go1.20.1#Int), are safe for concurrent use by multiple goroutines.

This package's outputs might be easily predictable regardless of how it's seeded. For random numbers suitable for security-sensitive work, see the crypto/rand package.

##### Example
``` go 
```

##### Example
``` go 
```












## 常量 

This section is empty.

## 变量

This section is empty.

## 函数

#### func [ExpFloat64](https://cs.opensource.google/go/go/+/go1.20.1:src/math/rand/rand.go;l=401) 

``` go 
func ExpFloat64() float64
```

ExpFloat64 returns an exponentially distributed float64 in the range (0, +math.MaxFloat64] with an exponential distribution whose rate parameter (lambda) is 1 and whose mean is 1/lambda (1) from the default Source. To produce a distribution with a different rate parameter, callers can adjust the output using:

```
sample = ExpFloat64() / desiredRateParameter
```

#### func [Float32](https://cs.opensource.google/go/go/+/go1.20.1:src/math/rand/rand.go;l=366) 

``` go 
func Float32() float32
```

Float32 returns, as a float32, a pseudo-random number in the half-open interval [0.0,1.0) from the default Source.

#### func [Float64](https://cs.opensource.google/go/go/+/go1.20.1:src/math/rand/rand.go;l=362) 

``` go 
func Float64() float64
```

Float64 returns, as a float64, a pseudo-random number in the half-open interval [0.0,1.0) from the default Source.

#### func [Int](https://cs.opensource.google/go/go/+/go1.20.1:src/math/rand/rand.go;l=343) 

``` go 
func Int() int
```

Int returns a non-negative pseudo-random int from the default Source.

#### func [Int31](https://cs.opensource.google/go/go/+/go1.20.1:src/math/rand/rand.go;l=340) 

``` go 
func Int31() int32
```

Int31 returns a non-negative pseudo-random 31-bit integer as an int32 from the default Source.

#### func [Int31n](https://cs.opensource.google/go/go/+/go1.20.1:src/math/rand/rand.go;l=353) 

``` go 
func Int31n(n int32) int32
```

Int31n returns, as an int32, a non-negative pseudo-random number in the half-open interval [0,n) from the default Source. It panics if n <= 0.

#### func [Int63](https://cs.opensource.google/go/go/+/go1.20.1:src/math/rand/rand.go;l=328) 

``` go 
func Int63() int64
```

Int63 returns a non-negative pseudo-random 63-bit integer as an int64 from the default Source.

#### func [Int63n](https://cs.opensource.google/go/go/+/go1.20.1:src/math/rand/rand.go;l=348) 

``` go 
func Int63n(n int64) int64
```

Int63n returns, as an int64, a non-negative pseudo-random number in the half-open interval [0,n) from the default Source. It panics if n <= 0.

#### func [Intn](https://cs.opensource.google/go/go/+/go1.20.1:src/math/rand/rand.go;l=358) 

``` go 
func Intn(n int) int
```

Intn returns, as an int, a non-negative pseudo-random number in the half-open interval [0,n) from the default Source. It panics if n <= 0.

##### Example
``` go 
```

#### func [NormFloat64](https://cs.opensource.google/go/go/+/go1.20.1:src/math/rand/rand.go;l=392) 

``` go 
func NormFloat64() float64
```

NormFloat64 returns a normally distributed float64 in the range [-math.MaxFloat64, +math.MaxFloat64] with standard normal distribution (mean = 0, stddev = 1) from the default Source. To produce a different normal distribution, callers can adjust the output using:

```
sample = NormFloat64() * desiredStdDev + desiredMean
```

#### func [Perm](https://cs.opensource.google/go/go/+/go1.20.1:src/math/rand/rand.go;l=370) 

``` go 
func Perm(n int) []int
```

Perm returns, as a slice of n ints, a pseudo-random permutation of the integers in the half-open interval [0,n) from the default Source.

##### Example
``` go 
```

##### Example
``` go 
```

##### Example
``` go 
```

#### func [Shuffle](https://cs.opensource.google/go/go/+/go1.20.1:src/math/rand/rand.go;l=375)  <- go1.10

``` go 
func Shuffle(n int, swap func(i, j int))
```

Shuffle pseudo-randomizes the order of elements using the default Source. n is the number of elements. Shuffle panics if n < 0. swap swaps the elements with indexes i and j.

##### Example
``` go 
```

##### Example
``` go 
```

#### func [Uint32](https://cs.opensource.google/go/go/+/go1.20.1:src/math/rand/rand.go;l=332) 

``` go 
func Uint32() uint32
```

Uint32 returns a pseudo-random 32-bit value as a uint32 from the default Source.

#### func [Uint64](https://cs.opensource.google/go/go/+/go1.20.1:src/math/rand/rand.go;l=336)  <- go1.8

``` go 
func Uint64() uint64
```

Uint64 returns a pseudo-random 64-bit value as a uint64 from the default Source.

## 类型

### type [Rand](https://cs.opensource.google/go/go/+/go1.20.1:src/math/rand/rand.go;l=61) 

``` go 
type Rand struct {
	// contains filtered or unexported fields
}
```

A Rand is a source of random numbers.

#### func [New](https://cs.opensource.google/go/go/+/go1.20.1:src/math/rand/rand.go;l=77) 

``` go 
func New(src Source) *Rand
```

New returns a new Rand that uses random values from src to generate other random values.

#### (*Rand) [ExpFloat64](https://cs.opensource.google/go/go/+/go1.20.1:src/math/rand/exp.go;l=30) 

``` go 
func (r *Rand) ExpFloat64() float64
```

ExpFloat64 returns an exponentially distributed float64 in the range (0, +math.MaxFloat64] with an exponential distribution whose rate parameter (lambda) is 1 and whose mean is 1/lambda (1). To produce a distribution with a different rate parameter, callers can adjust the output using:

```
sample = ExpFloat64() / desiredRateParameter
```

#### (*Rand) [Float32](https://cs.opensource.google/go/go/+/go1.20.1:src/math/rand/rand.go;l=214) 

``` go 
func (r *Rand) Float32() float32
```

Float32 returns, as a float32, a pseudo-random number in the half-open interval [0.0,1.0).

#### (*Rand) [Float64](https://cs.opensource.google/go/go/+/go1.20.1:src/math/rand/rand.go;l=188) 

``` go 
func (r *Rand) Float64() float64
```

Float64 returns, as a float64, a pseudo-random number in the half-open interval [0.0,1.0).

#### (*Rand) [Int](https://cs.opensource.google/go/go/+/go1.20.1:src/math/rand/rand.go;l=112) 

``` go 
func (r *Rand) Int() int
```

Int returns a non-negative pseudo-random int.

#### (*Rand) [Int31](https://cs.opensource.google/go/go/+/go1.20.1:src/math/rand/rand.go;l=109) 

``` go 
func (r *Rand) Int31() int32
```

Int31 returns a non-negative pseudo-random 31-bit integer as an int32.

#### (*Rand) [Int31n](https://cs.opensource.google/go/go/+/go1.20.1:src/math/rand/rand.go;l=136) 

``` go 
func (r *Rand) Int31n(n int32) int32
```

Int31n returns, as an int32, a non-negative pseudo-random number in the half-open interval [0,n). It panics if n <= 0.

#### (*Rand) [Int63](https://cs.opensource.google/go/go/+/go1.20.1:src/math/rand/rand.go;l=95) 

``` go 
func (r *Rand) Int63() int64
```

Int63 returns a non-negative pseudo-random 63-bit integer as an int64.

#### (*Rand) [Int63n](https://cs.opensource.google/go/go/+/go1.20.1:src/math/rand/rand.go;l=119) 

``` go 
func (r *Rand) Int63n(n int64) int64
```

Int63n returns, as an int64, a non-negative pseudo-random number in the half-open interval [0,n). It panics if n <= 0.

#### (*Rand) [Intn](https://cs.opensource.google/go/go/+/go1.20.1:src/math/rand/rand.go;l=177) 

``` go 
func (r *Rand) Intn(n int) int
```

Intn returns, as an int, a non-negative pseudo-random number in the half-open interval [0,n). It panics if n <= 0.

#### (*Rand) [NormFloat64](https://cs.opensource.google/go/go/+/go1.20.1:src/math/rand/normal.go;l=37) 

``` go 
func (r *Rand) NormFloat64() float64
```

NormFloat64 returns a normally distributed float64 in the range -math.MaxFloat64 through +math.MaxFloat64 inclusive, with standard normal distribution (mean = 0, stddev = 1). To produce a different normal distribution, callers can adjust the output using:

```
sample = NormFloat64() * desiredStdDev + desiredMean
```

#### (*Rand) [Perm](https://cs.opensource.google/go/go/+/go1.20.1:src/math/rand/rand.go;l=228) 

``` go 
func (r *Rand) Perm(n int) []int
```

Perm returns, as a slice of n ints, a pseudo-random permutation of the integers in the half-open interval [0,n).

#### (*Rand) [Read](https://cs.opensource.google/go/go/+/go1.20.1:src/math/rand/rand.go;l=271)  <- go1.6

``` go 
func (r *Rand) Read(p []byte) (n int, err error)
```

Read generates len(p) random bytes and writes them into p. It always returns len(p) and a nil error. Read should not be called concurrently with any other Rand method.

#### (*Rand) [Seed](https://cs.opensource.google/go/go/+/go1.20.1:src/math/rand/rand.go;l=84) 

``` go 
func (r *Rand) Seed(seed int64)
```

Seed uses the provided seed value to initialize the generator to a deterministic state. Seed should not be called concurrently with any other Rand method.

#### (*Rand) [Shuffle](https://cs.opensource.google/go/go/+/go1.20.1:src/math/rand/rand.go;l=246)  <- go1.10

``` go 
func (r *Rand) Shuffle(n int, swap func(i, j int))
```

Shuffle pseudo-randomizes the order of elements. n is the number of elements. Shuffle panics if n < 0. swap swaps the elements with indexes i and j.

#### (*Rand) [Uint32](https://cs.opensource.google/go/go/+/go1.20.1:src/math/rand/rand.go;l=98) 

``` go 
func (r *Rand) Uint32() uint32
```

Uint32 returns a pseudo-random 32-bit value as a uint32.

#### (*Rand) [Uint64](https://cs.opensource.google/go/go/+/go1.20.1:src/math/rand/rand.go;l=101)  <- go1.8

``` go 
func (r *Rand) Uint64() uint64
```

Uint64 returns a pseudo-random 64-bit value as a uint64.

### type [Source](https://cs.opensource.google/go/go/+/go1.20.1:src/math/rand/rand.go;l=30) 

``` go 
type Source interface {
	Int63() int64
	Seed(seed int64)
}
```

A Source represents a source of uniformly-distributed pseudo-random int64 values in the range [0, 1<<63).

A Source is not safe for concurrent use by multiple goroutines.

#### func [NewSource](https://cs.opensource.google/go/go/+/go1.20.1:src/math/rand/rand.go;l=50) 

``` go 
func NewSource(seed int64) Source
```

NewSource returns a new pseudo-random Source seeded with the given value. Unlike the default Source used by top-level functions, this source is not safe for concurrent use by multiple goroutines. The returned Source implements Source64.

### type [Source64](https://cs.opensource.google/go/go/+/go1.20.1:src/math/rand/rand.go;l=41)  <- go1.8

``` go 
type Source64 interface {
	Source
	Uint64() uint64
}
```

A Source64 is a Source that can also generate uniformly-distributed pseudo-random uint64 values in the range [0, 1<<64) directly. If a Rand r's underlying Source s implements Source64, then r.Uint64 returns the result of one call to s.Uint64 instead of making two calls to s.Int63.

### type [Zipf](https://cs.opensource.google/go/go/+/go1.20.1:src/math/rand/zipf.go;l=15) 

``` go 
type Zipf struct {
	// contains filtered or unexported fields
}
```

A Zipf generates Zipf distributed variates.

#### func [NewZipf](https://cs.opensource.google/go/go/+/go1.20.1:src/math/rand/zipf.go;l=39) 

``` go 
func NewZipf(r *Rand, s float64, v float64, imax uint64) *Zipf
```

NewZipf returns a Zipf variate generator. The generator generates values k ∈ [0, imax] such that P(k) is proportional to (v + k) ** (-s). Requirements: s > 1 and v >= 1.

#### (*Zipf) [Uint64](https://cs.opensource.google/go/go/+/go1.20.1:src/math/rand/zipf.go;l=58) 

``` go 
func (z *Zipf) Uint64() uint64
```

Uint64 returns a value drawn from the Zipf distribution described by the Zipf object.