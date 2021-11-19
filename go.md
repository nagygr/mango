# Installation

## Arch-derivatives

Packages:

-	`go`: basic development tools
-	`gopls`: language server (e.g. needed by VS Code)

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

# Compilation

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

## Coverage

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

# File formats

## XML

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

# GUI

## Cross-platform GUI with Fyne

Fyne is an OpenGL-based cross-platform GUI library that uses the Material Design
concepts to design its widgets.

### Installing on Arch

Fyne itself is a Go module:

```bash
go get fyne.io/fyne/v2
```

In order to be able to compile to other platforms, the specific toolchains need
to be installed.

To compile to Windows on Arch, install:

```bash
pacman -S mingw-w64-gcc
```

### Cross-compilation

To compile a module for Windows:

```bash
GOOS=windows GOARCH=amd64 CGO_ENABLED=1 CC=x86_64-w64-mingw32-gcc go build
```

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

[1]: https://go.dev/blog/using-go-modules
[2]: https://golang.org/ref/mod
