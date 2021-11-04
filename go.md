# Development in Go

## Installation

### Arch-derivatives

Packages:

-	`go`: basic development tools
-	`gopls`: language server (e.g. needed by VS Code)

## Creating and building a simple module

### Initialize a module

```bash
go mod init hello
```

where `hello` is the name of the module.

More on modules: [brief intro][1], [reference][2].

### Add a source file

Add `main.go` (the file that holds the `main` package and within that the `main`
function has to be called `main.go`):

```go
package main

import "fmt"

func main() {
	fmt.Println("Hello world")
}
```

### Run or build the module

The module can be referred to by its name so:

```bash
go run hello
```

will run the module and

```bash
go build hello
```

will build an executable that, by default, will have the name of the module.

## Cross-compiling

The Go compiler can perform a cross-compilation on and to any system it runs on.
Two environment variables need to be set:

-	`GOOS`: the target operating system
-	`GOARCH`: the target architecture

If these variables are not set then the configuration of the host machine is
used as target.

The available OS/arch pairs can be obtained with:

```bash
go tool dist list
```

A few valid pairs:

-	`android/386`
-	`android/amd64`
-	`android/arm`
-	`android/arm64`
-	`darwin/amd64`
-	`darwin/arm64`
-	`linux/386`
-	`linux/amd64`
-	`linux/arm`
-	`linux/arm64`
-	`windows/386`
-	`windows/amd64`
-	`windows/arm`
-	`windows/arm64`

So, for example, to compile to a 64-bit Windows platform, one can issue:

```bash
GOOS=windows GOARCH=amd64 go build
```

The Go tool will make sure all the libraries in the dependency list are acquired
in the needed format and then it creates the artifact for the given platform.

## Functions

### Function types

The `func(int)` type defines a function that receives an `int` and doesn't
return anything.

A function with a receiver is in fact a function with an extra argument at the
first position, so this works:

```go
type Data struct {
	value int
}

func (d *Data) set(i int) {
	d.value = i
}

type ObjectSetter struct {
	setter func(d *Data, i int)
}

func (o *ObjectSetter) set(d *Data, i int) {
	o.setter(d, i)
}

func main() {
	op := ObjectSetter{(*Data).set}
	d := Data{0}
	op.set(&d, 42)
}
```

This way both the type of the `struct` and the argument types are bound to the
type and thus `ObjectSetter` can only store a function that has a `Data`
receiver and that takes `int` as an argument.

Note, that the "method  pointer" was given as: `(*Data).set`.

It is also possible to bind the object to the function value and thus provide a
function that only requires the argument of the original method. This can work
with any `struct` type but the object has to be known at the time of setting (as
opposed to the example above where it is given at the time of calling):

```go
type Data struct {
	value int
}

func (d *Data) set(i int) {
	d.value = i
}

type ObjectSetter struct {
	setter func(int)
}

func (o *ObjectSetter) set(i int) {
	o.setter(i)
}

func main() {
	d := Data{0}
	op := ObjectSetter{d.set}
	op.set(42)
}
```

If we would like to decouple the object and the function, the generic
`interface{}` type can be used with a type assertion and an unnamed function:

```go
type Data struct {
	value int
}

func (d *Data) set(i int) {
	d.value = i
}

type ObjectSetter struct {
	setter func(object interface{}, i int)
}

func (o *ObjectSetter) set(object interface{}, i int) {
	o.setter(object, i)
}

func main() {
	op := ObjectSetter{func(obj interface{}, i int) {obj.(*Data).set(i)}}

	d := Data{0}
	op.set(&d, 42)

	fmt.Printf("Value in data: %d\n", d.value)
}
```

## Testing

### Test setup

Any file that's name ends with `_test.go` is considered a test file. Within test
files, the public functions with names matching `Test...` and taking a
pointer to `testing.T` will become part of the executed test suite.

An example simple test file:

```go
package main

import "testing"

func TestMain(t *testing.T) {
	t.Errorf("I will always fail no matter what you do")
}
```

To run the tests, issue:

```bash
go test
```

As the path crawling is automatic, there's no need to specify the module here.

### Coverage

Go has a built-in coverage tool. It can be called in several ways, the simplest
of which is:

```bash
go test -cover
```

Apart from the usual test summary that `go test` provides, this will also print the coverage in percent.

In order to get a bit deeper insight, we should first have the coverage information printed to a file by issuing:

```bash
go test -coverprofile=coverage.out
```

This will create the `coverage.out` file (the name of which can be freely chosen). The coverage visualization tool of Go can then use this file to produce different outputs:

-	`go tool cover -func=coverage.out`: will print a detailed (per-function) list
	of coverage values -- something like this:

	```
	hello/main.go:5:	addTwo		100.0%
	hello/main.go:9:	main		0.0%
	total:			(statements)	33.3%
	```

-	`go tool cover -html=coverage.out`: will open a browser with a view of the
	source files with their lines coloured to red or green to show whether a given line was covered with a test or not

[1]: https://go.dev/blog/using-go-modules
[2]: https://golang.org/ref/mod
