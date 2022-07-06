# Installation

## Arch-derivatives

Packages:

-	`go`: basic development tools
-	`gopls`: language server (e.g. needed by VS Code)

## Vim support

[This project][13] provides a Vim package with e.g. many custom commands,
better highlighting, embedded [Delve](#debugging) support.

# Creating and building a simple module

## Initialize a module

```bash
go mod init hello
```

where `hello` is the name of the module.

More on modules: [brief intro][1], [reference][2].

## Add a source file

Add `main.go` (the file that holds the `main` package and within that the `main`
function has to be called `main.go`):

```go
package main

import "fmt"

func main() {
	fmt.Println("Hello world")
}
```

## Run or build the module

The module can be referred to by its name so:

```bash
go run hello
```

will run the module and

```bash
go build hello
```

will build an executable that, by default, will have the name of the module.

## Formatting the code

Go comes with a code formatter that makes sure that the code is in accordance
with the standards in terms of formatting. It can be called on a file by
issuing e.g.:

```bash
go fmt main.go
```

This overwrites the file (can add/remove whitespaces, can remove unnecessary
imports). Most IDE's call this utility upon saving to disc.

If there are many Go files to format in subdirectories, the command can be used
in the following format:

```bash
go fmt ./internal/...
```

## Code analysis

Go comes with a tool called `vet` which performs code analysis to find
suspicious constructs. It uses heuristics so not all reports will be genuine
problems but typically it's wise to conform to it.

In order to analyse the package in the current directory, simply issue:

```bash
go vet
```

If the packages reside in subdirectories, `vet` can be asked to parse them
recursively:

```bash
go vet ./my/project/...
```

More can be found on `vet` [here][15].

## Linters

There are many linters for Go and there's a tool, called `golangci-lint` that
can run them all in parallel. This is a program that has to be installed
manually (on Arch it can be done using the AUR package of the same name).

It can be configured using a yaml (or toml) file (e.g. `.golangci.yaml`):

```yaml
linters:
  disable-all: true

  enable:
    # Enabled By Default Linters
    - deadcode # Finds unused code
    - errcheck # Errcheck is a program for checking for unchecked errors in go programs. These unchecked errors can be critical bugs in some cases
    - gosimple # Linter for Go source code that specializes in simplifying a code
    - govet # Vet examines Go source code and reports suspicious constructs, such as Printf calls whose arguments do not align with the format string
    - ineffassign # Detects when assignments to existing variables are not used
    - staticcheck # Staticcheck is a go vet on steroids, applying a ton of static analysis checks
    - structcheck # Finds unused struct fields
    - typecheck # Like the front-end of a Go compiler, parses and type-checks Go code
    - unused # Checks Go code for unused constants, variables, functions and types
    - varcheck # Finds unused global variables and constants

    # Disabled By Default Linters
    # - asciicheck # Simple linter to check that your code does not contain non-ASCII identifiers
    - bodyclose # Checks whether HTTP response body is closed successfully
    # - cyclop # Checks function and package cyclomatic complexity
    # - depguard # Go linter that checks if package imports are in a list of acceptable packages
    # - dogsled # Checks assignments with too many blank identifiers (e.g. x, , , _, := f())
    - dupl # Tool for code clone detection
    - durationcheck # Check for two durations multiplied together
    # - errorlint # go-errorlint is a source code linter for Go software that can be used to find code that will cause problemswith the error wrapping scheme introduced in Go 1.13.
    # - exhaustive # Check exhaustiveness of enum switch statements
    # - exhaustivestruct # Checks if all struct's fields are initialized
    # - exportloopref # Checks for pointers to enclosing loop variables
    # - forbidigo - Forbids identifiers
    # - forcetypeassert - finds forced type assertions
    - funlen # Tool for detection of long functions
    # - gci # Gci control golang package import order and make it always deterministic.
    # - gochecknoglobals # Check that no global variables exist
    # - gochecknoinits # Checks that no init functions are present in Go code
    # - gocognit # Computes and checks the cognitive complexity of functions
    - goconst # Finds repeated strings that could be replaced by a constant
    # - gocritic # Provides many diagnostics that check for bugs, performance and style issues. Extensible without recompilation through dynamic rules. Dynamic rules are written declaratively with AST patterns, filters, report message and optional suggestion.
    # - gocyclo # Computes and checks the cyclomatic complexity of functions
    # - godot # Check if comments end in a period
    - godox # Tool for detection of FIXME, TODO and other comment keywords
    # - goerr113 # Golang linter to check the errors handling expressions
    - gofmt # Gofmt checks whether code was gofmt-ed. By default this tool runs with -s option to check for code simplification
    # - gofumpt # Gofumpt checks whether code was gofumpt-ed.
    # - goheader # Checks is file header matches to pattern
    - goimports # Goimports does everything that gofmt does. Additionally it checks unused imports
    # - golint # Golint differs from gofmt. Gofmt reformats Go source code, whereas golint prints out style mistakes
    # - gomnd # An analyzer to detect magic numbers.
    # - gomodguard # Allow and block list linter for direct Go module dependencies. This is different from depguard where there are different block types for example version constraints and module recommendations.
    - goprintffuncname # Checks that printf-like functions are named with f at the end
    - gosec # Inspects source code for security problems
    # - ifshort # Checks that your code uses short syntax for if-statements whenever possible
    # - importas # Enforces consistent import aliases
    # - interfacer # Linter that suggests narrower interface types
    - lll # Reports long lines
    - makezero # Finds slice declarations with non-zero initial length
    # - maligned # Tool to detect Go structs that would take less memory if their fields were sorted
    # - misspell # Finds commonly misspelled English words in comments
    # - nakedret # Finds naked returns in functions greater than a specified function length
    - nestif # Reports deeply nested if statements
    # - nilerr # Finds the code that returns nil even if it checks that the error is not nil.
    # - nlreturn # nlreturn checks for a new line before return and branch statements to increase code clarity
    # - noctx # noctx finds sending http request without context.Context
    - nolintlint # Reports ill-formed or insufficient nolint directives
    # - paralleltest # paralleltest detects missing usage of t.Parallel() method in your Go test
    # - prealloc # Finds slice declarations that could potentially be preallocated
    - predeclared # find code that shadows one of Go's predeclared identifiers
    - revive # Fast, configurable, extensible, flexible, and beautiful linter for Go. Drop-in replacement of golint.
    # - rowserrcheck # checks whether Err of rows is checked successfully
    # - scopelint # Scopelint checks for unpinned variables in go programs
    # - sqlclosecheck # Checks that sql.Rows and sql.Stmt are closed.
    # - stylecheck # Stylecheck is a replacement for golint
    # - testpackage # linter that makes you use a separate _test package
    # - thelper # thelper detects golang test helpers without t.Helper() call and checks the consistency of test helpers
    # - tparallel # tparallel detects inappropriate usage of t.Parallel() method in your Go test codes
    # - unconvert # Remove unnecessary type conversions
    - unparam # Reports unused function parameters
    - wastedassign # wastedassign finds wasted assignment statements.
    # - whitespace # Tool for detection of leading and trailing whitespace
    - wrapcheck # Checks that errors returned from external packages are wrapped
    # - wsl # Whitespace Linter - Forces you to use empty lines!

# all available settings of specific linters
linters-settings:
  nolintlint:
    # Enable to ensure that nolint directives are all used. Default is true.
    allow-unused: false
    # Disable to ensure that nolint directives don't have a leading space. Default is true.
    allow-leading-space: false
    # Enable to require an explanation of nonzero length after each nolint directive. Default is false.
    require-explanation: true
    # Enable to require nolint directives to mention the specific linter being suppressed. Default is false.
    require-specific: true

issues:
  exclude-rules:
    # Exclude some linters from running on tests files.
    - path: _test\.go
      linters:
        - lll
        - goconst
        - bodyclose
        - errcheck
        - funlen
        - goerr113
        - dupl
        - wrapcheck

    # Exclude lll for struct tags
    - linters:
      - lll
      source: "^\\W*\\w+\\W+[\\.\\w]+\\W+\\x60\\w+(:\".+?\")( \\w+(:\".+?\"))*\\x60$" # struct param with tags (\x60 is the backtick)

    # Exclude lll for pragmas
    - linters:
      - lll
      source: "^//(go:|nolint:)"

    # Don't enforce pre-defining and wrapping errors in main packages. Note,
    # that this rule only applies if golangci-lint is called from the main
    # directory, see https://github.com/golangci/golangci-lint/issues/1178
    - path: ^cmd/
      linters:
        - goerr113
        - wrapcheck

    # Don't suggest to replace http.ResponseWriter with io.Writer.
    - source: "w http\\.ResponseWriter"
      text: "`w` can be `io.Writer`"
      linters:
        - interfacer
```

It can then be run by issuing:

```bash
golangci-lint run -v --timeout 5m
```

in the root directory of a project.

If a line in the code should be left out by the linter, a comment has to be
added with the following elements:

```
//nolint:<lint-rule> // <Reason why it is excluded>
```

Please note that spaces are important in the above line. More than one lint
rule can be added as a list separated by comma.

E.g.:

```go
return (*os.File)(tf).Read(p) //nolint:wrapcheck // The same error has to be returned: it can be io.EOF
```

or

```go
//nolint:revive //Stutter removal doesn't apply here: package and class name are unrelated
type MainWindow struct {
```

More on it can be learned [here][18].

# Module and package handling

## Automatic downloading on build

Most tutorials say that one should run

```bash
go get <module-name>
```

before building a project to gather all the required libraries, have them written
to `go.mod` and have `go.sum` generated.

This can also be automated: just add the imports to the sources and run

```bash
go build -mod=mod
```

This will automatically get all the missing modules, add them to `go.mod` and
get on with the building process as usual.

## Packages in a directory hierarchy

The package files can reside in a directory hiearchy which affects the `import`
statements.

In the following example the module name will be: `vcshost.com/user/module` and
the directory tree will be:

```
go.mod
main.go
[lib]
|
+-- pkg.go
```

The `go.mod` file:

```go
module vcshost.com/user/module

go 1.17
```

The `lib/pkg.go` file:

```go
package pkg

type MyType struct {
	value int
}

func MakeMyType(value int) MyType {
	return MyType{value}
}

func (myType *MyType) Get() int {
	return myType.value
}

func (myType *MyType) set(i int) {
	myType.value = i
}
```

The `main.go` file:

```go
package main

import (
	"fmt"

	"vcshost.com/user/module/lib"
)

func main() {
	t := pkg.MakeMyType(42)

	fmt.Printf("The answer is: %d\n", t.Get())
}
```

>	**Note**
>
>	The symbols (type and function names) that should be accessible
>	from outside of a package need to start with a capital letter. For
>	example, the `set` function in the `lib/pkg.go` package is not accessible
>	from `main.go` (or from anywhere else outside of `pkg.go` for that matter).

>	**Note**
>
>	The name of the file containing a package doesn't need to be the same
>	as that of the package. The file `lib/pkg.go` can have an arbitrary
>	name. In fact, a package can reside in multiple files: the contents of
>	all the files that contain the same package declaration are united.

### Real-life project structure

Big Go projects are structured in a bit more complex way. The details can be
found [here][5] but the gist is that the code resides in three directories:

-	`cmd`: the executables (if there are any) go under this directory. If there
	is more than one of them, they all have their own folders below `cmd`.
-	`internal`: private application and library code goes here -- more details
	on this can be found [here][6]
-	`pkg`: public packages, library code goes here -- the code here is safe to
	be used by others

There are further directories used for other purposes (e.g. `test` for test
data and test applications, etc.).

A typical structure could be:

```
[cmd]
|
+-- [my_exe_1]
|   |
|   +-- main.go
|   +-- ...
|
+-- [my_exe_2]
    |
    +-- main.go
    +-- ...

[internal]
|
+-- [package_1]
|   |
|   +-- type_1.go
|   +-- type_2.go
|
+-- [package_2]
    |
    +-- type_3.go

[pkg]
|
+-- [package_3]
    |
    +-- type_4.go

go.mod
go.sum
```

An executable under `cmd` can be built from the repo root by passing the path
to its `main.go` to `go build`, e.g:

```bash
go build ./cmd/my_exe_1
```

>	**Note**
>
>	Please note, that although it is not necessary to name the package within a
>	directory the same as the directory, but this is done in practice. So, for
>	example the code in `type_3.go` would reside in `package package_2`.

### More on imports

Imports can be given *aliases* to make it easier to refer to them in the code:

```go
import dg "github.com/bwmarrin/discordgo"

func main() {
    err := dg.New()
}
```

A *dot import* unifies namespaces, i.e. it imports a package in a way that all
the symbols from that package are added to the current namespace, thus they
don't need to be prepended with the package name. This can, of course, cause
name clashes and should be used sparingly (if ever):

```go
import (
    . "math"
    "fmt"
)

fmt.Println(Pi) // instead of math.Pi
```

A *blank import* can be used during development to stop go from complaining
about an unused import (and `go fmt` or IDEs using `go fmt`) from removing them
automatically:

```go
import (
    "database/sql"
    _ "github.com/go-sql-driver/mysql"
)
```

## Documentation

Documentation is supported by the `godoc` tool which can be installed by
issuing:

```bash
go get golang.org/x/tools/cmd/godoc
```

This command will create an executable called `godoc` in `$GOPATH/bin/`
(typically `~/go/bin` on Linux machines).

It can be run by issuing:

```bash
godoc -http=:6060
```

This should be executed in a package's root directory and the port can of course
be changed.

It will read documentation comments which in Go are basically simple textual
comments directly above a declaration with the first sentence starting with the
name of the element (and "Package <packagename>" for packages). It's basically
as simple as this, but there a few more rules [here][3].

If the overview documentation of a package is too long, it can be moved to a
separate file called `doc.go` in the same directory. The package declaration
should be inserted to that file as well and the documentation should go directly
above it in a comment (just as with normal source files).

The documentation of a given package will then be available at:
`http://localhost:6060/pkg/<package-name>`, e.g.:
`http://localhost:6060/pkg/github.com/nagygr/saxparser/`.

By default, `godoc` hides the documentation of the internal packages (the
packages under the folder called `internal`. The behaviour can be changed by
adding an extra argument (`?m=all`) to the URL:

```
http://localhost:6060/pkg/<package-name>/?m=all
```

## Publishing a package

When publishing a package the commits that will act as versions of the library
shall be tagged. This can also be done after the commit has been pushed. Just
issue:

```bash
git tag v0.1.0
git push origin v0.1.0
```

The library can then be referenced in `go.mod` as e.g.:

```
module saxtest

go 1.17

require github.com/nagygr/saxparser v0.1.0
```

Without the tags, virtual tags will be created automatically for the head but
updating a package with automatic tags is cumbersome, so explicit tagging should
be used. When a version is updated in `go mod`, issuing

```bash
go mod tidy
```

or

```bash
go build -mod=mod
```

downloads the latest version.

Packages downloaded by `go build` or `go get` are automatically entered into
the [official go module directory][4] (it takes a few minutes for the changes
to get through). Every module has its own page here, where important details
(`go.mod` file, license, versions) are shown together with the projects readme
markdown document and `godoc` documentation. A link to the repository is also
shown.

If, for some reason, the package doesn't show up, the following command can be
issued from within a go module:

```bash
GOPROXY=https://proxy.golang.org GO111MODULE=on go get github.com/nagygr/ooxml2txt@v0.2.0
```

or it can be requested on the page.

Go handles module versions 2 and beyond in a special way. [This article][17]
gives details about the subject.

# Compilation

## Build tags

Build tags provide a way for a project to define what goes into a build at
compilation time. This can be used e.g. to define *free*, *pro* and
*enterprise* features for an application.

The granularity of this feature is file-level: an entire go source file will be
compiled or left out depending on the tags set at compilation time.

The following example makes use of the fact that the `init` function of a
module (which runs before `main`) can be used to initialize resources at a
module level and there can exist more than `init`s for the same module. In such
a case, the order that the `init` functions of a module are run is not
guaranteed but all of them will get called and they will run before the `main`
function of an application.

The `main.go` can look like this:

```go
package main

import "fmt"

var features = []string{
  "Free Feature #1",
  "Free Feature #2",
}

func main() {
  for _, f := range features {
    fmt.Println(">", f)
  }
}
```

In a separate file, the *pro* features can be added with a tag. A tag is
defined in a comment with the `+build` prefix. The `pro.go` file will look like
this:

```go
// +build pro

package main

func init() {
  features = append(features,
    "Pro Feature #1",
    "Pro Feature #2",
  )
}
```

If we want to add the *pro* features to the application, it has to be built
with the following command line arguments:

```bash
go build -tags pro
```

There can be more than one tag defined for an application. This can mean
however that sometimes a logic has to be defined between the tags. For example,
if we want the *enterprise* features to also contain the *pro* features, than
the `pro.go` file should be defined as follows:

```go
// +build pro enterprise

package main

func init() {
  features = append(features,
    "Pro Feature #1",
    "Pro Feature #2",
  )
}

```

And the `enterprise.go` will look like this:

```go
// +build enterprise

package main

func init() {
  features = append(features,
    "Enterprise Feature #1",
    "Enterprise Feature #2",
  )
}
```

When tags are listed with a space separator, this realizes an *OR* function, so
if either tag is defined, the file will be compiled into the project.

*AND* logic can be acheived in two ways (comma separated list (no spaces!) or
separate build lines for the tags):

```go
// +build pro,enterprise
```

or

```go
// +build pro
// +build enterprise
```

When setting more than one tags on the command line, they have to be placed
inside a string literal:

```bash
go build -tags "pro enterprise"
```

There's a third logic operator as well: `// +build !pro` will compile the given
file when *pro* is not set.

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

## Viewing compiler decisions

Compiler decisions (function inlining, variable placement (stack vs. heap)) can be visualized by sending a compiler flag to the compiler tool.

Flags can be sent down to the compiler from the `build` command using the
`-gcflags` switch which takes a string that contains the compiler switches:

-	`-m`: tells the compiler to print the decisions made during compilation
-	`-l`: tells the compiler to refrain from inlining (inlining can hide stack vs. heap information)

So to build a project and get the decisions, issue:

```bash
go build -gcflags '-m -l'
```

## Viewing the assembly output

The actual assembly listing that the Go program is turned into can be viewed by issuing:

```bash
go tool compile -S <source-code>
```

This command directly addresses the compiler and it expects an actual Go file as
its argument (won't work with the current module automatically as `build` does).

# Interfaces

Interfaces can be used to declare a set of public functions that a type must
implement. Interface implementation is not shown in syntax, it is implicit
(much like with templates in C++ -- duck typing).

A function that shall be able to use any instance that implements the interface
should take an interface *by value*. The vtable-like behaviour that enables
this functionality is then provided by the language. On the calling side a
pointer of a complete type should be passed in.

The following "trick" makes the compiler check and emit an error if a concrete
type doesn't implement an interface:

```go
var _ InterfaceName = (*ConcreteTypeName)(nil)
```

>	**Note**
>
>	In the line above we declare an unnamed variable (which will be optimized
>	out) and try to assign a pointer of a concrete type (in fact a `nil`
>	pointer cast to the concrete type so as to avoid instantiation on this side
>	as well). If the concrete type fails to implement the interface then this
>	will emit a compiler error.

An example:

```go
package main 

type Apple interface {
	f(int) int
}

type Banana struct {
	 i int
}

func (b *Banana) f(v int) int {
	return b.i * v
}

type Grape struct {}

func (g *Grape) f() int {
	return 42
}

func main() {
	var _ Apple = (*Banana)(nil)
	var _ Apple = (*Grape)(nil)   // (!) FAILS
}
```

Trying to compile this code results in the following error message:

```
/a.go:25:16: cannot use (*Grape)(nil) (value of type *Grape) as type apple in variable declaration:
	*Grape does not implement Apple (wrong type for f method)
		have f() int
		want f(int) int
```

# Embedding

Embedding is a feature of Go that supports the composition over inheritance
principle (there is no inheritence in Go). An embedded type's fields and
methods can be used on the embedding type as if they were its own.

During initialization, the embedded type has to be created explicitly. A
"constructor" function can also be called for the embedded struct but it has to
be dereferenced as embedding is by-value and constructors typically return
pointers.

A field or a method of the embedded type can be shadowed in which case it can
be referenced with the type's name.

In case of interface embedding a diamond situation is possible (when the same
method is embedded from two sources (e.g. once directly and once through an
interface that already embeds it)). From version 1.14 this works as expected,
before that it resulted in compilation error.

The following example shows the basic and the shadowing scenarios:

```go
package main

import (
	"fmt"
)

type Base struct {
	data int
	other float32
}

func (b *Base) ShowFields() {
	fmt.Printf("%d %f\n", b.data, b.other)
}

func (b *Base) PrintFields() {
	fmt.Printf("%d %f\n", b.data, b.other)
}

type Sub struct {
	Base
	data string
}

func NewSub(idata int, fdata float32, sdata string) *Sub {
	return &Sub{
		Base{idata, fdata},
		sdata,
	}
}

func (s *Sub) PrintFields() {
	s.Base.PrintFields()
	fmt.Printf("%s (%d, %f)\n", s.data, s.Base.data, s.other)
}

func main() {
	s := NewSub(42, 42.0, "Douglas Adams")

	s.PrintFields()
	s.ShowFields()
}
```

Embedding structs in struct result in both data reuse and code reuse, embedding
interfaces in interfaces unifies the methods of the interfaces.

In case of embedding an interface into a struct it becomes possible for the
struct to be used anywhere where the interface is expected and to set the
actual behaviour upon initialization. I.e. the same struct initialized with
different structs implementing the interface can have a different behaviour
from instance to instance but all of them will implement the interface when
seen from the outside.

```go
type Fooer interface {
  Foo() string
}

type Container struct {
  Fooer
}

// sink takes a value implementing the Fooer interface.
func sink(f Fooer) {
  fmt.Println("sink:", f.Foo())
}

// TheRealFoo is a type that implements the Fooer interface.
type TheRealFoo struct {
}

func (trf TheRealFoo) Foo() string {
  return "TheRealFoo Foo"
}

co := Container{Fooer: TheRealFoo{}}
sink(co)
```

A typical use-case for this is wrappers, when we wrap an actual struct,
"overwrite" any number of its methods (with the technique shown above) and then
pass it on to any place where the interface that it implements is expected.
This way new features can be added to a struct similarly to how the Decorator
pattern works in OOP.

The following example shows how a network connection can be decorated with a
statistics feature and then passed on to functions where the original
connection would have been passed.

```go
type StatsConn struct {
  net.Conn

  BytesRead uint64
}

// Adding functionality to net.Conn:
func (sc *StatsConn) Read(p []byte) (int, error) {
  n, err := sc.Conn.Read(p)
  sc.BytesRead += uint64(n)
  return n, err
}

// Forwarding calls where necessary:
func (sc *StatsConn) Close() error {
  return sc.conn.Close()
}

func main() {
	conn, err := net.Dial("tcp", u.Host+":80")
	if err != nil {
	  log.Fatal(err)
	}

	// Decorating the actual instance:
	sconn := &StatsConn{conn, 0}

	// Using it where the original is usually passed:
	resp, err := ioutil.ReadAll(sconn)
	if err != nil {
	  log.Fatal(err)
	}
}
```

A similar example is from the standard library. The `sort.Sort` function takes
a `sort.Interface` as an argument and uses its functions to parametrize the
sort algorithm:

```go
type Interface interface {
    // Len is the number of elements in the collection.
    Len() int
    // Less reports whether the element with
    // index i should sort before the element with index j.
    Less(i, j int) bool
    // Swap swaps the elements with indexes i and j.
    Swap(i, j int)
}

func main() {
	lst := []int{4, 5, 2, 8, 1, 9, 3}
	sort.Sort(sort.IntSlice(lst))
	fmt.Println(lst)
}
```

The `sort.IntSlice` type implements `sort.Interface` so the algorithm can work
on `int` slices.

By adding the following definitions, it becomes possible perform a reverse sort
on any type of containers:

```go
type reverse struct {
  sort.Interface
}

func (r reverse) Less(i, j int) bool {
  return r.Interface.Less(j, i)
}

func Reverse(data sort.Interface) sort.Interface {
  return &reverse{data}
}

func main() {
	lst := []int{4, 5, 2, 8, 1, 9, 3}
	sort.Sort(sort.Reverse(sort.IntSlice(lst)))
	fmt.Println(lst)
}
```

The examples above were taken from Eli Bendersky's articles on Go embedding
([part 1][10], [part 2][11], [part3][12]). They have more details on the
subject.

# Functions

## Function types

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

### Conversions between function types

Unfortunately, a function returning a concrete type that implements an
interface cannot be cast to a function returning the interface. This is a
problem when factory functions for the concrete types of an interface are to be
handled together.

A possible solution is to add a dedicated constructor to the implementing
structs that will return an interface.

```go
type Document interface {
	GetReader() io.Reader
}

type TextFile struct {
	...
}

func NewTextFile(path string) (*TextFile, error) {
	...
}

func NewTextFileAsDocument(path string) (Document, error) {
	return NewTextFile(path)
}

func (tf *TextFile) GetReader() io.Reader {
	...
}

type DocFactory func (string) (Document, error)

type DocMux struct {
	registry map[string]DocFactory
}

func NewDocMux() (*DocMux, error) {
	registry := map[string]DocFactory {
		".txt": NewTextFileAsDocument}   // NewTextFile would not compile here
	
	return &DocMux{registry: registry}
}

func (dm *DocMux) GetDocument(path string) (Document, error) {
	doc, present := dm.registry[filepath.Ext(path)]
	...
	return doc(path)

}

func main() {
	...
	path := os.Args[1]
	dm, err := NewDocMux()
	...
	doc, err := dm.GetDocument(path)
	...
	reader = doc.GetReader()
}
```

## Deferring calls

The `defer` keyword can be used to instruct the compiler to make a call to a
given function right before the given function returns (in any way -- panics
included). This can be used to free/close resources (similar to RAII in C++ but
in Go the deferred functions are called at the end of the function and not the
end of the code block).

The argument of the deferred function are evaluated immediately, it's only the
call that is made at the end of the function.

If there are more than one deferred functions: the calls to them happen in a
reverse order as they are stored in a stack. The following example is not a
realistic one but it shows how the ordering of defers can be crucial.

```go
package main

import "fmt"

type Object struct {
	value *int
}

func MakeObject() Object {
	vptr := new(int)
	*vptr = 42
	return Object{value: vptr}
}

func (o *Object) Clear() {
	*o.value = 0
}

func (o *Object) Nil() {
	o.value = nil
}

func main() {
	o := MakeObject()
	defer o.Nil()
	defer o.Clear()

	/*
	// This would be the logical order (the order that the functions should be
	// called), but it will cause a nil pointer dereference panic because the
	// deferred functions are stored in a stack and thus the call order is
	// reversed.
	defer o.Clear()
	defer o.Nil()
	*/

	fmt.Printf("%v, %d\n", o.value, *o.value)
}
```

# Strings

## Stringer interface

The `fmt.Stringer` interface makes it possible for variables to be convertable
to string. Functions like `Printf` can then take them and print them using the
`%s` annotation verb.

The interface expects one function:

```go
func String() string
```

The receiver can be either a value or a pointer but it is important that if a
user struct has a `String()` method that takes a pointer receiver then the
objects of that struct have to be passed to Printf-like functions as a pointer.
Otherwise they won't recognize it as a `Stringer` and will output a generic
string representation instead of calling the method.

# Handling time

## Time formatting

Time is handled by the `time` standard package. Parsing time from strings is
done using the so called layout strings. These are strings where a certain date
and time instance needs to be written in the required format (pattern-based
formatting). The tricky thing is that a predefined date has to be used for each
field and the error messages returned when a value is not right can be hard to
decode.

The predefined date in a certain format is:

```go
layout := "2006-01-02 15:04:05.000 -0700 MST"
```

This layout is then used for parsing a time instance from a string with
`time.Parse(layout, value string) (Time, error)` and also to turn a variable
of `time.Time` into a string with `(t time.Time) Format(layout string) string`.

More on this can be read [here][19] and [here][20].

# Errors

Go doesn't have exceptions. Instead it has functions that can return multiple
values, one of which is typically a value of type `error`. If the returned
`error` value is not `nil`, it means that the function encountered an error and
couldn't perform its task succesfully. This should always be checked and acted
upon. Quite often errors are forwarded up the call stack to a point where they
can be handled correctly.

Errors can be created in two ways: the `errors.New(string)` function takes a
string that becomes the error message. One needs `fmt.Sprintf` to get a
formatted string into it though. This is where `fmt.Errorf(string, ...any)`
comes into play: it takes a format string and any number of parameters and
returns an error with the created string as a message.

Some packages define functions with the name `Must`. They are usually stand-ins
for other functions, but with a single return value (no error). They panic
instead.

# Testing

## Test setup

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

If the project contains subdirectories (subpackages) and tests reside in them,
the command that runs the tests should be issued in the following form to
instruct it to perform a recursive crawling:

```bash
go test ./...
```

If tests want to refer to files in the repository, the paths should be given as
relative paths from the given test file.

The `go test` command also runs a sanity check on the code so it is worth
issuing it even without actual tests.

## Methods of testing.T

There are two types of functions for failing a test:

1.	failure is registered but execution continues:
	-	`Fail()`
	-	`Error()`, `Errorf()`
2.	immediately fails the function, execution stops:
	-	`FailNow()`
	-	`Fatal()`, `Fatalf()`

Logging can be achieved with `Log()` or `Logf()`. Logs are only printed for
failing tests or if the `-test.v` flag is set. For benchmark tests, the test is
always printed (to avoid performance dependence on the state of the flag).

A test can be skipped using any of the `Skip()`, `SkipNow()`, `Skipf()`
functions.

The function `Cleanup(f func())` can be used to set a cleanup function to run
after the test and its subtests.

## Test case concurrency

By default the test functions within a package are run sequentially but the
tests in different packages are run concurrently.

If tests in different packages may collide, package-level concurrency can be
disabled using the `-p 1` command-line switch.

```bash
go test -p 1 ./pkg/...
```

Test case-level concurrency is opt-in and the `T.Parallel()` method has to be
called to enable it. More on this [here][21].

## Coverage

Go has a built-in coverage tool. It can be called in several ways, the simplest
of which is:

```bash
go test -cover
```

Apart from the usual test summary that `go test` provides, this will also print
the coverage in percent.

In order to get a bit deeper insight, we should first have the coverage
information printed to a file by issuing:

```bash
go test -coverprofile=coverage.out
```

This will create the `coverage.out` file (the name of which can be freely
chosen). The coverage visualization tool of Go can then use this file to
produce different outputs:

-	`go tool cover -func=coverage.out`: will print a detailed (per-function)
	list of coverage values -- something like this:

	```
	hello/main.go:5:	addTwo		100.0%
	hello/main.go:9:	main		0.0%
	total:			(statements)	33.3%
	```

-	`go tool cover -html=coverage.out`: will open a browser with a view of the
	source files with their lines coloured to red or green to show whether a
	given line was covered with a test or not

## Example codes

Example codes can be added to the source documentation but they should not go
in the comment section. Go has a way of checking the examples to that broken or
incomplete examples are not published.

Example functions are functions that have a name starting with "Example...".

By default example functions are only compiled but not executed. If they
include concluding line comments where the first line is either "Output:" or
"Unordered output:" then the functions are compiled and their standard output
is compared to the line below the output comment line. In the case of unordered
output, the order of the output doesn't matter.

Here are some examples from the official documentation:

```go
func ExampleHello() {
    fmt.Println("hello")
    // Output: hello
}

func ExampleSalutations() {
    fmt.Println("hello, and")
    fmt.Println("goodbye")
    // Output:
    // hello, and
    // goodbye
}

func ExamplePerm() {
    for _, value := range Perm(5) {
        fmt.Println(value)
    }
    // Unordered output: 4
    // 2
    // 1
    // 3
    // 0
}
```

The naming convention to declare examples for the package, a function F, a type
T and method M on type T are:

```go
func Example() { ... }
func ExampleF() { ... }
func ExampleT() { ... }
func ExampleT_M() { ... }
```

Multiple example functions for a package/type/function/method may be provided
by appending a distinct suffix to the name. The suffix must start with a
lower-case letter.

```go
func Example_suffix() { ... }
func ExampleF_suffix() { ... }
func ExampleT_suffix() { ... }
func ExampleT_M_suffix() { ... }
```

# Debugging

It is [possible to debug][7] Go applications using `gdb` but it is not trivial
to set things up. There's however a dedicated Go debugger that has an interface
very similar to that of `gdb`'s: [delve][8]. It has an extensive documentation
within the repository and also has an API that allows the creation of clients.

It can be installed by issuing:

```bash
go install github.com/go-delve/delve/cmd/dlv@latest
```

This command will put the `dlv` executable to `~/go/bin`.

Debugging can be started by issuing:

```bash
dlv debug
```

This only works if the working directory contains `main.go` with a `main`
function. Otherwise the path to the package that contains the code for the
executable has to be given, e.g:

```bash
dlv debug ./cmd/mypackage
```

If the executable takes command line arguments, they can be passed to delve
after a double dash (`--`):

```bash
dlv debug ./cmd/mypackage -- "arguments" "4" "the_executable"
```

Debugging starts with the `continue` (`c`) command. The rest of the commands
are very similar to gdb's commands:

-	`break` (`b`): adds a breakpoint (e.g. `b filename.go:42`)
-	`continue` (`c`): runs the program until the next breakpoint or until it ends
-	`step` (`s`): steps through the program (steps into function calls)
-	`next` (`n`): steps over to the next line (executes call without stepping inside)
-	`stepout` (`so`): steps out of current function
-	`print` (`p`): evaluates expression
-	`locals`: shows local variables
-	`quit`: exits delve

For more commands and details on how to use them, refer to [the
documentation][9].

# Concurrency

## Channels and wait groups

Channels are a means of communication between goroutines (Go's green threads)
and can also be used for synchronization. To synchronize/wait for multiple
threads, wait groups (`sync.WaitGroup`) come handy.

Channels are created with the built-in `make` function. They are strongly typed,
the data type needs to be set in the declaration: `ch := make(chan int)`.

Channels can be buffered or unbuffered. The size of the buffer is the second
(and optional) argument to `make`. The default value is 1 which creates an
unbuffered channel that blocks the writer until the data written to the channel
is read.

Writing to (`ch <- data`) and reading from (`data <- ch`) channels is done in a
synchronized and atomic manner. It is also blocking for the writers and readers
when the channel's buffer is full or empty respectively. Reading can also be
performed with a for-range loop. The exit condition for the loop will be the
closing of the channel (`close(ch)`).

Wait groups are setup with the number of threads to wait for (e.g. `wg.Add(2)`),
the threads shall call `wg.Done()` on them when they finish, and at the
synchronization point `wg.Wait()` shall be called.

The following example shows how these two can be used to create a simple single
writer multiple reader situation.

```go
package main

import (
	"fmt"
	"sync"
)

func source(c chan string) {
	data := [...]string {
		"apple", "banana", "cherry", "pear", "grape", "ananas",
		"potato", "carrot", "beetroot", "pea", "bean", "cabbage",
	}

	for _, d := range data {
		c <- d
	}

	close(c)
}

func sink(c chan string, id int, wg *sync.WaitGroup) {
	for d := range c {
		fmt.Printf("[%d]: %s\n", id, d)
	}

	wg.Done()
}

func main() {
	var wg sync.WaitGroup
	wg.Add(2)

	ch := make(chan string, 2)

	go source(ch)
	go sink(ch, 0, &wg)
	go sink(ch, 1, &wg)

	wg.Wait()
}
```

## Hardware concurrency

Although threads in Go are lightweight, it can be a good idea to scale
the number of parallel threads with respect to the number of available cores.
This information can be retrieved using the `runtime` package:

```go
import "runtime"

cores := runtime.NumCPU()
```

The size of channels and the number of parallel goroutines can be based on this
value.

The number of cores that a Go program can use can be retrieved and set by the
`runtime.GOMAXPROCS()` function. If it is called with a value less than 1, then
it simply reports the value but doesn't change it. Otherwise it sets the value
and constrain the program to use the given number of CPUs. This value can also
be set using an environment variable of the same name. The value defaults to
the value returned by `runtime.NumCPU()`.

Exactly because it can be set using an environment variable, applications
should use the value returned by `runtime.GOMAXPROCS()` instead of
`runtime.NumCPU()`, because this way they can honour the request of the user
(and still default to the maximum available cores).

## Yielding

Sometimes, especially when a goroutine is doing intensive calculation, it is
advisable to yield the processor and allow other goroutines to run. There's a
call that does exactly this: `runtime.Gosched()`. It doesn't stop the current
goroutine, it only allows the scheduler to get on with its work and start
others.

[This article][16] has more details on this. Apparently a lot of built-in
function make calls to this function.

## Error groups

The problem with `WaitGroup`s is that the return value of the functions is lost
therefore they make correct error handling impossible. The solution for this
problem is `errgroup.Group`. This is very similar to a `WaitGroup`, the main
difference is that its `Wait` method returns an `error` that comes from a
failing task. If any of the tasks in the group returns a non-nil error, then
the `Wait` returns immediately (closing the other tasks) and the error will be
available to be processed.

```go
package main

import (
	"fmt"
	"log"
	"math/rand"

	"golang.org/x/sync/errgroup"
)

func Task(task int) error {
	if rand.Intn(10) == task {
		return fmt.Errorf("Task %v failed\n", task)
	}
	fmt.Printf("Task %v completed\n", task)
	return nil
}

func main() {
	eg := &errgroup.Group{}
	for i := 0; i < 10; i++ {
		task := i
		eg.Go(func() error {
			return Task(task)
		})
	}
	if err := eg.Wait(); err != nil {
		log.Fatal("Error: ", err)
	}
	fmt.Println("Completed successfully!\n")
}
```

>	**Note**
>
>	By changing the condition in `func Task` to `task % 3 == 0` it becomes
>	evident that `Wait` returns immediately after receiving an error no matter
>	how many unfinished tasks are still working.

>	**Note**
>
>	Note, how the iterator variable of the `for` loop (`i`) is copied into the
>	local `task` variable which is then passed to the goroutine. This is
>	because by passing `i`, it would be shared among every goroutine, thus it
>	would create a datarace.
>
>	In fact, he goroutines won't start until `Wait` is called, as wait groups
>	and error groups wait for the collection of all goroutines, so if `i` was
>	directly passed into the goroutines, each would receive the final value of
>	`i` which is 10 and that's not what we want here.

## Contexts

Contexts are objects that can be used to share data and to control concurrent
threads (e.g. cancellation).

A simple example showing how data can be set and read is the following:

```go
package main

import (
	"context"
	"fmt"
)

func main() {
	ctx := context.Background()
	ctx = addValue(ctx)
	readValue(ctx)
}

func addValue(ctx context.Context) context.Context {
	return context.WithValue(ctx, "key", "test-value")
}

func readValue(ctx context.Context) {
	val := ctx.Value("key")
	fmt.Println(val)
}
```

>	**Note**
>
>	It is important to note that the functions  that take and mutate `Context`
>	objects always return a new `Context` that has to be used instead of the
>	old one. `WithValue` for example takes the original context, adds a
>	key-value pair and returns a `Context` with every previously set value and
>	the new piece of information. This pattern is used by every method that handle
>	`Context`s.

In the following example, a handler middleware adds a *guid* to the
`http.Request` object using a `Context`. This gets written to the log (and thus
the standard out) when `localhost:8080/ishealthy` is requested with a *get*
method.

```go
package main

import (
	"context"
	"log"
	"net/http"

	"github.com/google/uuid"
	"github.com/gorilla/mux"
)

func main() {
	router := mux.NewRouter()
	router.Use(guidMiddleware)
	router.HandleFunc("/ishealthy", handleIsHealthy).Methods(http.MethodGet)
	http.ListenAndServe(":8080", router)
}

func handleIsHealthy(w http.ResponseWriter, r *http.Request) {
	w.WriteHeader(http.StatusOK)
	uuid := r.Context().Value("uuid")
	log.Printf("[%v] Returning 200 - Healthy", uuid)
	w.Write([]byte("Healthy"))
}

func guidMiddleware(next http.Handler) http.Handler {
	return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
		uuid := uuid.New()
		r = r.WithContext(context.WithValue(r.Context(), "uuid", uuid))
		next.ServeHTTP(w, r)
	})
}
```

## Errgroup with cancellable context

A cancellable context is a context for parallel goroutines that can be used to
stop all of them in a programmatic, safe manner. The example below starts three
goroutines, two of them runs tickers that tick in contant intervals and the
third one is waiting for OS signals (and will exit on `ctrl-c`).

Theres is also a timed function call using `time.AfterFunc` that will run the
closure it is given as the second argument after a given timeout.

```go
package main

import (
	"context"
	"fmt"
	"os"
	"os/signal"
	"syscall"
	"time"

	"golang.org/x/sync/errgroup"
)

func main() {

	ctx, done := context.WithCancel(context.Background())
	g, gctx := errgroup.WithContext(ctx)

	// goroutine to check for signals to gracefully finish all functions
	g.Go(func() error {
		signalChannel := make(chan os.Signal, 1)
		signal.Notify(signalChannel, os.Interrupt, syscall.SIGTERM)

		select {
		case sig := <-signalChannel:
			fmt.Printf("Received signal: %s\n", sig)
			done()
		case <-gctx.Done():
			fmt.Printf("closing signal goroutine\n")
			return gctx.Err()
		}

		return nil
	})

	// just a ticker every 2s
	g.Go(func() error {
		ticker := time.NewTicker(2 * time.Second)
		for {
			select {
			case <-ticker.C:
				fmt.Printf("ticker 2s ticked\n")
			case <-gctx.Done():
				fmt.Printf("closing ticker 2s goroutine\n")
				return gctx.Err()
			}
		}
	})

	// just a ticker every 1s
	g.Go(func() error {
		ticker := time.NewTicker(1 * time.Second)
		for {
			select {
			case <-ticker.C:
				fmt.Printf("ticker 1s ticked\n")
			case <-gctx.Done():
				fmt.Printf("closing ticker 1s goroutine\n")
				return gctx.Err()
			}
		}
	})

	// force a stop after 20s
	time.AfterFunc(20*time.Second, func() {
		fmt.Printf("force finished after 20s\n")
		done()
	})

	// wait for all errgroup goroutines
	if err := g.Wait(); err == nil || err == context.Canceled {
		fmt.Println("finished clean")
	} else {
		fmt.Printf("received error: %v\n", err)
	}
}
```

>	**Note**
>
>	Please note how the parallel tasks can be cancelled by calling the `done`
>	function of the cancellable context. The tasks need to listen for the done
>	signal using a `select`.

# Interfacing the OS

## Running external commands

External commands can be run using the `Cmd` struct within `os/exec`. The
`Command(cmd string, args []string)` function takes a command and its arguments
and returns a pointer to a `Cmd`.

-	`cmd.Run()`: simply runs the command and returns an error
-	`cmd.Output()`: returns the standard output of the command as a `[]byte`
	and an error

The standard input and output streams of the command can also be set and
controlled.

The following example turns command strings into slices split at spaces so that
they can be passed to `Command()` and then executes the commands. Please note
how the first element is handled separately in the command slices as
`Command()` expects the command name separately from its arguments.

```go
package main

import (
	"fmt"
	"log"
	"os/exec"
	"strings"
)

var (
	commands = []string{
		"ls",
		"find . -name \"*.go\"",
	}
)

func main() {
	for i, cmdTxt := range commands {
		var (
			args = strings.Split(cmdTxt, " ")
			cmd *exec.Cmd
		)

		log.Printf("Args (%d): %v", len(args), args)

		if len(args) == 1 {
			cmd = exec.Command(args[0])
		} else {
			cmd = exec.Command(args[0], args[1:]...)
		}

		fmt.Printf("Executing command %d (%s)\n", i, cmdTxt)
		stdout, err := cmd.Output()

		log.Println(string(stdout))

		if err != nil {
			log.Printf(
				"The following command failed:\n\n%s\n\nwith this error:\n\n:%s\n",
				cmd, err.Error(),
			)
		}
	}
}
```

The following example is on [zetcode][14]. It feeds input through the standard
input of the command and also takes its standard output:

```go
package main

import (
    "bytes"
    "fmt"
    "log"
    "os/exec"
    "strings"
)

func main() {
    cmd := exec.Command("tr", "a-z", "A-Z")

    cmd.Stdin = strings.NewReader("and old falcon")

    var out bytes.Buffer
    cmd.Stdout = &out

    err := cmd.Run()

    if err != nil {
        log.Fatal(err)
    }

    fmt.Printf("translated phrase: %q\n", out.String())
}
```

There are further examples on [zetcode][14] showing e.g. how pipes can be
used to connect commands to eachother or connect internal writers/readers to
commands.

# Web applications

## Simple file server

The following simple code creates a file server that serves on port 8100 and
displays the `index.html` in the current directory or, if that is not present,
the directory listing:

```go
package main

import (
	"flag"
	"log"
	"net/http"
)

func main() {
	port := flag.String("p", "8100", "port to serve on")
	directory := flag.String("d", ".", "the directory of static file to host")
	flag.Parse()

	http.Handle("/", http.FileServer(http.Dir(*directory)))

	log.Printf("Serving %s on HTTP port: %s\n", *directory, *port)
	log.Fatal(http.ListenAndServe(":"+*port, nil))
}
```

## Getting the hosts local IP

The following snippet gets the local IP:

```go
package main

import (
	"fmt"
    "log"
    "net"
)

// Get preferred outbound ip of this machine
func GetOutboundIP() net.IP {
    conn, err := net.Dial("udp", "8.8.8.8:80")
    if err != nil {
        log.Fatal(err)
    }
    defer conn.Close()

    localAddr := conn.LocalAddr().(*net.UDPAddr)

    return localAddr.IP
}

func main() {
	fmt.Printf("%w", GetOutboundIP())
}
```

Or, here's another way of doing it:

```go
package main

import (
	"fmt"
	"net"
	"os"
)

func main() {
	name, err := os.Hostname()
	if err != nil {
		 fmt.Printf("Error: %v\n", err)
		 return
	}

	addrs, err := net.LookupHost(name)
	if err != nil {
		fmt.Printf("Error: %v\n", err)
		return
	}

	for _, a := range addrs {
		fmt.Println(a)
	}  
}
```

# File formats

## XML

### Unmarshalling XMLs

The standard `encoding/xml` package provides means to unmarshal XMLs to structs
in an automated way, using just tags. It can handle elements, attributes, an
array of elements and custom types as well. An XML element can contain
instances of itself recursively, this is also handled.

Each struct that corresponds to an XML tag should have a field with the type
`xml.Name` called `XMLName` that will connect the struct to the XML tag. The
connection is created by a tag for this field: `xml:"<tagname>"`. All other
exported fields of the struct can correspond to either child tags of attributes
of the given tag. An attribute is different from a child tag in that it has
",attr" after its name in the tag. Similarly the character data of a tag can be
explicitly exported by adding ",chardata" after its name.

Custom types can be unmarshalled as well if they implement the `Unmarshaller`
or `UnmarshallerAttr` interfaces.

The following snippet contains examples of these features.

```go
package main

import (
	"encoding/xml"
	"fmt"
	"log"
	"strconv"
	"strings"
	"time"
)

type Size struct {
	Height int
	Width int
	Length int
}

func (s *Size) UnmarshalXMLAttr(attr xml.Attr) error {
	dimensionsStr := strings.Split(attr.Value, "×")

	if dims := len(dimensionsStr); dims != 3 {
		return fmt.Errorf(
			"The number of dimensions in size string is not enough (%d instead of 3)",
			dims,
		)
	}

	dimensions := [3]int{}

	for i := 0; i < 2; i++ {
		var err error
		dimensions[i], err = strconv.Atoi(dimensionsStr[i])

		if err != nil {
			return fmt.Errorf(
				"Dimension no. %d could not be parsed as int: %s",
				i, err.Error(),
			)
		}
	}

	s.Height = dimensions[0]
	s.Width = dimensions[1]
	s.Length = dimensions[2]

	return nil
}

type PackTime time.Time

func (s *PackTime) UnmarshalXML(d *xml.Decoder, start xml.StartElement) error {
    const shortForm = "20060102" // yyyymmdd date format

    var v string
    d.DecodeElement(&v, &start)
    parsedTime, err := time.Parse(shortForm, v)
    
	if err != nil {
        return fmt.Errorf(
			"Error parsing pack time: %s", err.Error(),
		)
    }
    
	*s = PackTime(parsedTime)
    return nil
}

type Owner struct {
	Name string `xml:"name"`
	Age int `xml:"age"`
}

type Box struct {
	XMLName xml.Name `xml:"box"`
	Owner Owner `xml:"owner"`
	Contents string `xml:"contents"`
	Size Size `xml:"size,attr"`
	PackTime PackTime `xml:"packedAt"`
	Boxes []Box `xml:"box"`
}

func main() {
	xmlText := `
		<box size="45×50×80">
			<owner>
				<name>Patrick</name>
				<age>3</age>
			</owner>
			<contents>Toys</contents>
			<packedAt>20220623</packedAt>
			<box size="25×16×30">
				<owner>
					<name>Patrick</name>
					<age>3</age>
				</owner>
				<contents>Toy cars</contents>
				<packedAt>20220620</packedAt>
			</box>
		</box>
	`

	var box Box
	err := xml.Unmarshal([]byte(xmlText), &box)

	if err != nil {
		log.Fatalf(
			"Error parsing XML document: %s", err.Error(),
		)
	}

	fmt.Printf("%v\n", box)
}
```

### Parsing large XMLs

Large XML files should be parsed in a SAX-like manner to avoid loading the
entire structure in memory. The standard `encoding/xml` package provides means
to that (and many other things -- e.g. marchalling and unmarshalling). The
`Decoder` struct can be used to advance in an XML file token by token. Its
`Token()` method returns the upcoming token or (nil, io.EOF) when the end of
file is reached.

Here's a small example showing how this works:

```go
package main

import (
	"encoding/xml"
	"fmt"
	"io"
	"log"
	"os"
)

func main() {
	var xmlFile string = "data/test.xml"

	osm, err := os.Open(xmlFile)

	if err != nil {
		log.Fatalf("Error while opening xml file (%s): %s", xmlFile, err.Error())
	}

	var decoder = xml.NewDecoder(osm)

	for {
		token, err := decoder.Token()

		if err == io.EOF {
			fmt.Println("Successfully processed the xml file.")
			break
		} else if err != nil {
			log.Fatalf("Error while parsing xml file (%s): %s", xmlFile, err.Error())
		}

		switch t := token.(type) {
			case xml.CharData:
				fmt.Printf("Char data: %s\n", t)
			case xml.StartElement:
				fmt.Printf("Name: %s\n", t.Name.Local)
				for _, a := range t.Attr {
					fmt.Printf("\tAttribute: %s %s\n", a.Name.Local, a.Value)
				}
			case xml.ProcInst:
				fmt.Printf("ProcInst: %s\n", t.Target)
			default:
				fmt.Println("Other XML element")
		}
	}
}
```

The token types to check are: `StartElement`, `EndElement`, `CharData`,
`Comment`, `ProcInst` and `Directive`.

In the snippet above an infinite `for` loop was used and it breaked out of the
loop when the end of the file was reached. This is idiomatic, but not so good
for readability, especially with longer loop bodies. The following solution can
be used instead:


```go
	// ... (snippet, see full program code above)

	for token, err := decoder.Token(); err == nil; token, err = decoder.Token() {

		switch t := token.(type) {
			case xml.CharData:
				fmt.Printf("Char data: %s\n", t)
			case xml.StartElement:
				fmt.Printf("Name: %s\n", t.Name.Local)
				for _, a := range t.Attr {
					fmt.Printf("\tAttribute: %s %s\n", a.Name.Local, a.Value)
				}
			case xml.ProcInst:
				fmt.Printf("ProcInst: %s\n", t.Target)
			default:
				fmt.Println("Other XML element")
		}
	}

	if err == io.EOF {
		fmt.Println("Successfully processed the OSM file.")
	} else if err != nil {
		log.Fatalf("Error while parsing osm file (%s): %s", osmFile, err.Error())
	}

	// ...
```

## PDF

### Reading PDFs

There are several libraries that extract the text from a PDF file. The one that
seemed to handle different files the best from the free libraries was `fitz`.

As it contains C code as well, it needs to be cross-compiled with `CGO`
enabled:

```bash
GOOS=windows GOARCH=amd64 CGO_ENABLED=1 CC=x86_64-w64-mingw32-gcc go build -mod=mod
```

Here's a simple example, that extracts the text from a PDF:

```go
package main

import (
	"fmt"
	"github.com/gen2brain/go-fitz"
	"log"
)

func main() {
	doc, err := fitz.New("test.pdf")

	if err != nil {
		log.Panicf("Error creating new fitz (%s)", err.Error())
	}

	defer doc.Close()

	for n := 0; n < doc.NumPage(); n++ {
		text, err := doc.Text(n)
		if err != nil {
			log.Panic(err)
		}

		fmt.Printf("\n\nPage %d:\n%s\n", n, text)
	}
}
```

Fitz has several build tags for different purposes but the simple, tagless
build also works well.

# GUI

## Cross-platform GUI with Fyne

Fyne is an OpenGL-based cross-platform GUI library that uses the Material Design
concepts to design its widgets.

### Installing on Arch

Fyne itself is a Go module:

```bash
go get fyne.io/fyne/v2
```

### Cross-compilation

#### With CGO

In order to be able to compile to other platforms, the specific toolchains need
to be installed.

To compile to Windows on Arch, install:

```bash
pacman -S mingw-w64-gcc
```


To compile a module for Windows:

```bash
GOOS=windows GOARCH=amd64 CGO_ENABLED=1 CC=x86_64-w64-mingw32-gcc go build
```

#### With fyne-cross

Another possibility is `fyne-cross` which uses a designated Docker image for
each platform.

It can be installed it by issuing:

```bash
go install github.com/fyne-io/fyne-cross@latest
```

First, Docker needs to be started:

```bash
sudo systemctl start docker
```

The latest image for a given platform can be pulled by:

```bash
fyne-cross windows --pull
```

This command also builds the current project. If `--pull` is omitted then the
build is performed with the current Docker image.

### Simple application

```go
package main

import (
	"fmt"
	"image/color"

	"fyne.io/fyne/v2"
	"fyne.io/fyne/v2/app"
	"fyne.io/fyne/v2/canvas"
	"fyne.io/fyne/v2/container"
	"fyne.io/fyne/v2/dialog"
	"fyne.io/fyne/v2/widget"
)

func main() {
	myApp := app.New()
	myWindow := myApp.NewWindow("Gopher")

	fileMenu := fyne.NewMenu("File",
		fyne.NewMenuItem("Quit", func() { myApp.Quit() }),
	)

	helpMenu := fyne.NewMenu("Help",
		fyne.NewMenuItem("About", func() {
			dialog.ShowCustom("About", "Close", container.NewVBox(
				widget.NewLabel("Fyne demo"),
			), myWindow)
		}))
	mainMenu := fyne.NewMainMenu(
		fileMenu,
		helpMenu,
	)
	myWindow.SetMainMenu(mainMenu)

	buttonPresses := 0
	randomBtn := widget.NewButton("Random", nil)

	randomBtn.OnTapped = func() {
		buttonPresses += 1
		s := fmt.Sprintf("Button pressed for the %d. time", buttonPresses)
		randomBtn.SetText(s)
	}
	randomBtn.Importance = widget.HighImportance

	box := container.NewVBox(
		text,
		randomBtn,
	)

	myWindow.SetContent(box)

	myWindow.Canvas().SetOnTypedKey(func(keyEvent *fyne.KeyEvent) {

		if keyEvent.Name == fyne.KeyEscape {
			myApp.Quit()
		}
	})

	myWindow.ShowAndRun()
}
```

### Initial layout issue on i3wm

On i3wm Fyne windows can look bad and the widgets may be partially unresponsive upon startup. A resize or move 
fixes the issue which can be automated with the following workaround:

Instead of

```go
myWindow.ShowAndRun()
```

at the end, write:

```go
myWindow.Show()
go func() {
	time.Sleep(1 * time.Second)
	myWindow.Hide()
	myWindow.Resize(fyne.NewSize(600, 400))
	myWindow.Show()
}()
myApp.Run()
```

To do this only on Linux, place the declaration and calling of the goroutine inside the following if:

```go
if runtime.GOOS == "linux" {
	...
}
```

To also add a condition checking the window manager, the following code can be
used:

```go
if runtime.GOOS == "linux" && strings.HasPrefix(os.Getenv("DESKTOP_SESSION"), "i3") {
	...
}
```

>	**Note**
>
>	The `strings.HasPrefix` call is used above to check for both "i3" and "i3wm". A
>	correct solution for checking this could be:
>
>	```go
>	if runtime.GOOS == "linux" {
>		if map[string]bool{"i3": true, "i3wm": true}[os.Getenv("DESKTOP_SESSION")] {
>			go func() {
>				time.Sleep(1 * time.Second)
>				myWindow.Hide()
>				myWindow.Resize(fyne.NewSize(600, 400))
>				myWindow.Show()
>			}()
>		}
>	}
>	```
>
>	The expression in the if statement uses the fact that maps return a type's zero value
>	for missing keys (and, obviously, the zero value for booleans is false). 

>	**Note**
>
>	There seems to be a simple and much less obstrusive solution to the problem:
>
>	```go
>	myWindow.Show()
>	if runtime.GOOS == "linux" {
>		if map[string]bool{"i3": true, "i3wm": true}[os.Getenv("DESKTOP_SESSION")] {
>			myWindow.Content().Refresh()
>		}
>	}
>	myApp.Run()
>	```

## GTK

Go binding for GTK3 are provided by `github.com/gotk3/gotk3`. First it needs to
be installed (it can take quite some time):

```bash
go install github.com/gotk3/gotk3/gtk
```

Then the following code should compile with `go build`:

```go
package main

import (
	"fmt"
	"log"

	"github.com/gotk3/gotk3/gtk"
)

func main() {
	gtk.Init(nil)

	win, err := gtk.WindowNew(gtk.WINDOW_TOPLEVEL)
	if err != nil {
		log.Fatal("Unable to create window:", err)
	}
	win.SetTitle("Simple Example")
	win.Connect("destroy", func() {
		gtk.MainQuit()
	})

	l, err := gtk.LabelNew("Hello, gotk3!")
	if err != nil {
		log.Fatal("Unable to create label:", err)
	}

	b, err := gtk.ButtonNewWithLabel("Press me")

	if err != nil {
		log.Fatal("Unable to create button: ", err)
	}

	b.Connect("clicked", func() { fmt.Println("I've been clicked") })

	box, err := gtk.BoxNew(gtk.ORIENTATION_VERTICAL, 10)

	if err != nil {
		log.Fatal("Couldn't create box: ", err)
	}

	box.Add(l)
	box.Add(b)

	win.Add(box)

	win.SetDefaultSize(800, 600)

	win.ShowAll()

	gtk.Main()
}
```

### Cross-compilation from Linux to Windows (broken)

On Arch, the cross-compilation needs an installation with the right toolkit:

```bash
PKG_CONFIG_PATH=/usr/x86_64-w64-mingw32/lib/pkgconfig \
CGO_ENABLED=1 CC=x86_64-w64-mingw32-cc GOOS=windows GOARCH=amd64\
go install github.com/gotk3/gotk3/gtk
```

This fails if `aur/mingw-w64-gtk3`, `aur/mingw-w64-cairo` and
`aur/mingw-w64-gettext` are not installed. Unfortunately, on Arch/Manjaro these
fail with several `target not found` errors as of 26-11-2021.


## Goey

This is a simple framework still in heavy development. It is based on GTK, but
unlike gotk, it can be cross-compiled with ease.

In order for `goey` to work, it needs the gtk development packages to be available (on Arch: `extra/gtk3`).

The following snippet shows a simple example, which can be compiled to the host with `go build -mod=mod`:

```go
// This package provides an example application built using the goey package
// that shows a single button.  The button is centered in the window, and, when
// the button is clicked, the button's caption is changed to keep a running
// total.
package main

import (
	"fmt"
	"os"
	"strconv"

	"bitbucket.org/rj/goey"
	"bitbucket.org/rj/goey/base"
	"bitbucket.org/rj/goey/loop"
)

var (
	mainWindow *goey.Window
	clickCount int
)

func main() {
	err := loop.Run(createWindow)
	if err != nil {
		fmt.Fprintf(os.Stderr, "error: %s\n", err)
		os.Exit(1)
	}
}

func createWindow() error {
	// This is the callback used to initialize the GUI state.  For this simple
	// example, we need to create a new top-level window, and set a child
	// widget.
	mw, err := goey.NewWindow("One Button", render())
	if err != nil {
		return err
	}

	// We store a copy of the pointer to the window so that we can update the
	// GUI at a later time.
	mainWindow = mw

	return nil
}

func updateWindow() {
	// To update the window, we generate a new widget for the contents of the
	// top-level window.
	err := mainWindow.SetChild(render())
	if err != nil {
		fmt.Fprintf(os.Stderr, "error: %s\n", err)
	}
}

func render() base.Widget {
	// The text for the button will depend on how many times it has been
	// clicked.  Build the string for the button's caption.
	text := "Click me!"
	if clickCount > 0 {
		text = text + "  (" + strconv.Itoa(clickCount) + ")"
	}

	// We return a widget describing the desired state of the GUI.  Note that
	// this is data only, and no changes have been effected yet.
	return &goey.Padding{
		Insets: goey.DefaultInsets(),
		Child: &goey.Align{
			Child: &goey.Button{Text: text, OnClick: func() {
				// Side-effect for clicking the button.
				clickCount++
				// Update the contents of the top-level window.
				updateWindow()
			}},
		},
	}
}
```

### Cross-compilation

A goey project can be compiled for Windows on Arch by issuing:

```bash
GOOS=windows GOARCH=amd64 CGO_ENABLED=1 CC=x86_64-w64-mingw32-gcc go build -mod=mod
```

[1]: https://go.dev/blog/using-go-modules
[2]: https://golang.org/ref/mod
[3]: https://go.dev/blog/godoc
[4]: https://pkg.go.dev/
[5]: https://github.com/golang-standards/project-layout
[6]: https://go.dev/doc/go1.4#internalpackages
[7]: https://go.dev/doc/gdb
[8]: https://github.com/go-delve/delve/
[9]: https://github.com/go-delve/delve/tree/master/Documentation/cli
[10]: https://eli.thegreenplace.net/2020/embedding-in-go-part-1-structs-in-structs/
[11]: https://eli.thegreenplace.net/2020/embedding-in-go-part-2-interfaces-in-interfaces/
[12]: https://eli.thegreenplace.net/2020/embedding-in-go-part-3-interfaces-in-structs/
[13]: https://github.com/fatih/vim-go
[14]: https://zetcode.com/golang/exec-command/
[15]: https://pkg.go.dev/cmd/vet
[16]: https://winder.ai/cpu-hogging-in-golang/
[17]: https://go.dev/blog/v2-go-modules
[18]: https://golangci-lint.run/
[19]: https://www.geeksforgeeks.org/time-formatting-in-golang/
[20]: https://yourbasic.org/golang/format-parse-string-time-date-example/
[21]: https://pkg.go.dev/testing#T.Parallel
