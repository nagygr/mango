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

Add `main.go` (the file that holds the `main` package and within that the`main`
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

### Tests

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

[1]: https://go.dev/blog/using-go-modules
[2]: https://golang.org/ref/mod
