# Go (Golang)

- [Go (Golang)](#go-golang)
  - [Installation and Hello World](#installation-and-hello-world)
  - [Packages and Modules](#packages-and-modules)
    - [**`cmd/` — Main Application Entrypoints**](#cmd--main-application-entrypoints)
    - [**`internal/` — Private Application Code (Not Importable Externally)**](#internal--private-application-code-not-importable-externally)
    - [**`pkg/` — Public Packages (Optional)**](#pkg--public-packages-optional)
    - [**`api/` — API Definitions (Optional)**](#api--api-definitions-optional)
    - [**`configs/` — Configuration Files**](#configs--configuration-files)
    - [**`web/` — Web Assets (Optional)**](#web--web-assets-optional)
    - [**`scripts/` — Helper Scripts**](#scripts--helper-scripts)
    - [**`deployments/` / `infra/` — Deployment Files**](#deployments--infra--deployment-files)
    - [**`test/` — Integration or End-to-End Tests (Optional)**](#test--integration-or-end-to-end-tests-optional)
    - [Go Modules](#go-modules)
  - [Variables and Constants](#variables-and-constants)
  - [Data Types](#data-types)
  - [Control Flow](#control-flow)
  - [Functions](#functions)
  - [Error Handling](#error-handling)
  - [Structs and Methods](#structs-and-methods)
  - [Interfaces](#interfaces)
  - [Pointers](#pointers)
  - [Concurrency Basics](#concurrency-basics)
  - [Testing and Benchmarking](#testing-and-benchmarking)
  - [Synchronization Primitives](#synchronization-primitives)
  - [Reflection](#reflection)
  - [Advanced Concurrency and Optimization](#advanced-concurrency-and-optimization)

Assume Go 1.21+ for modern features. Use `go run main.go` to execute examples where applicable.

## Installation and Hello World

1. Download and install:
   Go is available at the official website: <golang.org/dl>. Installers exist for Windows, macOS, and Linux.
2. Environment variables:
   Modern Go versions set up most paths automatically, but traditionally you have:

   - GOROOT – Where Go itself is installed. This is usually set by the installer and rarely needs manual editing.
   - GOPATH – Workspace directory for your Go code and downloaded dependencies.

     - Default:
       - Linux/macOS: `~/go`
       - Windows: `%USERPROFILE%\go`
     - Inside `$GOPATH`, you will find `bin/`, `pkg/`, and `src/`.
   - PATH: Ensure `$GOROOT/bin` and `$GOPATH/bin` are in your `PATH` so you can run `go` globally and run installed binaries.

You can verify your installation with:

```bash
go version
go env
```

- Creating a “Hello, World!” Program:

Create a file called `main.go`:

```go
package main

import "fmt"

func main() {
    fmt.Println("Hello, World!")
}
```

- Run without compiling to a standalone binary:

  ```bash
  go run main.go
  ```

  This compiles and runs in one step.

- Build an executable:

  ```bash
  go build main.go
  ```

  Produces a binary named after your directory or file (`main`, `main.exe` on Windows).

- Install into `$GOPATH/bin` or module-aware bin dir:
  If inside a module with `package main`:

  ```bash
  go install
  ```

  The executable goes into `$GOPATH/bin` or `$GOBIN` if set.

- Single-line comments:

  ```go
  // This is a single-line comment
  ```

- Multi-line comments:

  ```go
  /*
     This is a multi-line comment
     spanning multiple lines
  */
  ```

## Packages and Modules

Go uses import paths that correspond to folder/module structures.

Example:

```go
import (
    "fmt"               // Standard library
    "math/rand"         // Another stdlib example
    "github.com/user/repo" // Remote module
)
```

Useful to shorten names or avoid conflicts:

```go
import f "fmt"

func main() {
    f.Println("aliased fmt")
}
```

Sometimes used for side effects (e.g., registering drivers):

```go
import _ "github.com/mattn/go-sqlite3"
```

A minimal Go module looks like this:

```text
myproject/
│
├── go.mod
├── go.sum
└── main.go
```

- **`go.mod`** — Defines your module path and lists dependencies.
- **`go.sum`** — Checksums for dependency verification (auto-generated).
- **`.go` files** — Your source code.

This is enough for tiny projects or command-line utilities.

As your project grows, Go developers often use a structure like:

```text
myproject/
│
├── cmd/
├── internal/
├── pkg/
├── api/
├── internal/
├── configs/
├── web/
├── scripts/
├── deployments/
├── test/
└── go.mod
```

Below is what each folder *typically* means.

### **`cmd/` — Main Application Entrypoints**

Contains one folder per executable your module builds.

Example:

```text
cmd/
  myproject/
    main.go
  migrate/
    main.go
```

Each directory under `cmd/` should contain a `main.go` that imports your internal packages.

**When used:**

- Multi-binary applications
- Tools shipped with your project

### **`internal/` — Private Application Code (Not Importable Externally)**

Anything under `internal/` **cannot** be imported by modules outside your repo.

Example:

```text
internal/
  db/
    connection.go
  service/
    user.go
    auth.go
```

**Purpose:**

- Enforce encapsulation
- Hide implementation details
- Prevent external projects from depending on unstable code

### **`pkg/` — Public Packages (Optional)**

Code here is **meant to be imported by other modules**.

Example:

```text
pkg/
  logger/
    logger.go
  utils/
    strings.go
```

**NOTE:**
Many modern Go developers try to avoid `pkg/` and instead rely on root-level packages or `internal/`, but it remains common in open-source ecosystems.

### **`api/` — API Definitions (Optional)**

Common for services.

Could include:

```text
api/
  openapi.yaml
  grpc/
    user.proto
```

### **`configs/` — Configuration Files**

Anything like:

```text
configs/
  dev.yaml
  prod.yaml
  app.toml
```

Useful for deployments or environment-specific settings.

### **`web/` — Web Assets (Optional)**

If your Go project serves HTML templates, static files, or frontend assets:

```text
web/
  templates/
  static/
    css/
    js/
    img/
```

### **`scripts/` — Helper Scripts**

Automation scripts:

```text
scripts/
  build.sh
  test.sh
  migrate_db.py
```

### **`deployments/` / `infra/` — Deployment Files**

Contains Docker/Kubernetes/Terraform/etc:

```text
deployments/
  docker/
  k8s/
  terraform/
```

### **`test/` — Integration or End-to-End Tests (Optional)**

Unit tests usually sit *next to the code*, but complex integration tests are placed in a dedicated folder:

```text
test/
  integration/
  e2e/
    auth_test.go
```

- Application code:

  - If it's internal logic → `internal/`
  - If it’s shared across binaries → `internal/` or `pkg/`
  - If it’s part of a single service and unlikely to be reused → root packages

Example root-level structure:

```text
/user
   service.go
   repository.go
/order
   service.go
```

This is simple and works well for medium projects.

- Unit Tests

Place tests right next to the source file:

```text
internal/user/service.go
internal/user/service_test.go
```

Rules:

- Test file ends with `_test.go`
- Usually in the same package

You can also test via an external test package:

```go
package user_test
```

This enforces public API testing and discourages internal access.

- Integration / E2E Tests

Place them under:

- `test/` folder
- OR `integration/` folder under project root

Example:

```text
test/integration/
  db_test.go
  server_test.go
```

These often run against real DBs or full services.

- Example Practical Project Structure

```text
myapp/
│
├── cmd/
│   └── myapp/
│       └── main.go
│
├── internal/
│   ├── user/
│   │    ├── service.go
│   │    └── service_test.go
│   ├── auth/
│   └── db/
│
├── pkg/
│   └── logger/
│       └── logger.go
│
├── api/
│   └── openapi.yaml
│
├── configs/
│   └── prod.yaml
│
├── web/
│   ├── templates/
│   └── static/
│
├── test/
│   └── e2e/
│       └── user_flow_test.go
│
└── go.mod
```

### Go Modules

- Initialize a Module

Run in your project folder:

```bash
go mod init github.com/yourname/project
```

This creates a `go.mod` file that tracks dependencies and module metadata.

- Adding Dependencies

Imports in your code trigger automatic fetching:

```bash
go get github.com/user/repo
```

- Updating and Tidying

```bash
go get -u ./...   # Update deps
go mod tidy       # Clean up unused deps
```

- Package Visibility and Export Rules

- Capitalized identifiers are exported (public)

  ```go
  func DoThing() {}    // Exported
  ```

- Lowercase identifiers are unexported (private)

  ```go
  func doThing() {}    // Internal to the package
  ```

This applies to functions, variables, structs, fields, and methods.

- The `init()` Function

Go lets you define one or more `init` functions per file:

```go
func init() {
    fmt.Println("init runs before main")
}
```

Rules & behavior:

- `init()` runs **automatically** before `main()` and after package imports are resolved.
- Useful for:
  - Setting up configuration
  - Registering components
  - Validating environment assumptions
- Avoid heavy logic; it can make code harder to reason about.

Execution order:

1. Package-level variable initialization
2. `init()` functions in import dependency order
3. `main()` finally runs

## Variables and Constants

- Variables: Declare with `var` or short `:=`.

  ```go
  var x int = 5  // Explicit
  y := "hello"   // Inferred type (string)
  ```

- Constants: Immutable, use `const`.

  ```go
  const Pi float64 = 3.14159
  const (        // Grouped
      A = 1
      B = 2
  )
  ```

- Zero Values: Uninitialized vars default to zero (e.g., int=0, string="").
- Shadowing: Inner scopes can redeclare vars.

## Data Types

- Primitives: `int`, `float64`, `bool`, `string`, `byte` (`uint8`), `rune` (`int32` for Unicode).

  ```go
  var b bool = true
  var s string = "Go"
  ```

- Arrays: Fixed size.

  ```go
  var arr [3]int = [3]int{1, 2, 3}
  ```

- Slices: Dynamic views on arrays.

  ```go
  slice := []int{1, 2, 3}  // Literal
  slice = append(slice, 4)
  ```

- Maps: Key-value pairs.

  ```go
  m := make(map[string]int)
  m["key"] = 42
  ```

- Strings

```go
import (
    "strings"
    "unicode/utf8"
    "strconv"
)

// Immutable – strings are read-only byte slices
s := "Hello, 世界"

// Length in bytes vs runes
len(s)            // 13 bytes (Hello, + 6 bytes for 世界)
utf8.RuneCountInString(s) // 8 runes (correct visual length)

// Indexing & slicing (bytes, not characters!)
s[0]   // 72  ('H' as byte)
s[7:]  // "世界"

// Convert to []rune for safe character access
runes := []rune(s)
runes[7] = 'go'  // Safe modification
s = string(runes)

// Common strings package operations
strings.Contains(s, "world")      // false
strings.HasPrefix(s, "Hello")     // true
strings.HasSuffix(s, "世界")       // true
strings.Index(s, "世")            // 7 (byte index)
strings.ReplaceAll(s, "Hello", "Hi") // "Hi, 世界"
strings.Split(s, ",")             // ["Hello" " 世界"]
strings.Join([]string{"a","b"}, "-") // "a-b"
strings.ToUpper(s)                // "HELLO, 世界"
strings.TrimSpace("  hi  ")       // "hi"

// Case-insensitive comparison
strings.EqualFold("Go", "go")     // true

// Building strings efficiently
var sb strings.Builder
sb.WriteString("Hello")
sb.WriteRune(' ')
sb.Write([]byte("世界"))
result := sb.String()             // Most efficient for concatenation

// Alternative: bytes.Buffer (also very fast)
// fmt.Fprintf(&buf, "%s %d", str, num)

// Conversion
i, _ := strconv.Atoi("123")       // string → int
f, _ := strconv.ParseFloat("3.14", 64)
s = strconv.Itoa(42)              // int → string
s = strconv.FormatBool(true)      // bool → string

// Formatting verbs
fmt.Sprintf("%s has %d runes", "世界", utf8.RuneCountInString("世界"))
// → "世界 has 2 runes"
```

**Key Takeaways**:

- Strings are immutable byte slices (UTF-8 encoded).
- Use `[]rune` or `utf8.RuneCountInString()` for correct character counting.
- Prefer `strings.Builder` or `bytes.Buffer` over `+=` in loops (avoids O(n²)).
- Most string operations are in the `strings` package; use it!

## Control Flow

- If/Else:

  ```go
  if x > 0 {
      fmt.Println("Positive")
  } else if x < 0 {
      fmt.Println("Negative")
  }
  ```

- For Loops: Only loop construct (no while/do-while).

  ```go
  for i := 0; i < 5; i++ { }  // Traditional
  for range slice { }         // Range over collections
  ```

- Switch:

  ```go
  switch day {
  case "Mon": fmt.Println("Monday")
  default:    fmt.Println("Other")
  }
  ```

- Break/Continue: Work with labels for nested loops.

## Functions

- Declaration:

  ```go
  func add(a int, b int) int {
      return a + b
  }
  ```

- Multiple Returns:

  ```go
  func divide(x, y float64) (float64, error) {
      if y == 0 { return 0, errors.New("division by zero") }
      return x / y, nil
  }
  ```

- Variadic:

  ```go
  func sum(nums ...int) int {
      total := 0
      for _, n := range nums { total += n }
      return total
  }
  ```

- Anonymous/Closures: `func() { }()` or capture vars.

## Error Handling

- Errors: Built-in interface; use `errors.New` or `fmt.Errorf`.

  ```go
  if err != nil {
      return err  // Propagate
  }
  ```

- Panic/Recover:

  ```go
  defer func() {
      if r := recover(); r != nil {
          fmt.Println("Recovered:", r)
      }
  }()
  panic("Oh no!")  // Throws
  ```

- Custom Errors: Implement `Error() string`.
- Best Practice: Handle errors explicitly; no try-catch.

## Structs and Methods

- Structs:

  ```go
  type Person struct {
      Name string
      Age  int
  }
  p := Person{Name: "Alice", Age: 30}
  ```

- Embedding:

  ```go
  type Employee struct {
      Person
      Salary int
  }
  ```

- Methods: Receiver before func name.

  ```go
  func (p *Person) Birthday() {
      p.Age++
  }
  ```

- Pointers vs Values: Use pointers for mutation.

## Interfaces

- Definition:

  ```go
  type Shape interface {
      Area() float64
  }
  ```

- Implementation: Implicit if methods match.

  ```go
  type Circle struct { Radius float64 }
  func (c Circle) Area() float64 { return math.Pi * c.Radius * c.Radius }
  ```

- Type Assertions/Switches:

  ```go
  var s Shape = Circle{Radius: 5}
  if c, ok := s.(Circle); ok { }  // Assert
  ```

- Empty Interface: `interface{}` holds any type (like any in TS).

## Pointers

- Basics:

  ```go
  var x int = 42
  ptr := &x    // Address
  *ptr = 100   // Dereference
  ```

- New:

  ```go
  p := new(int)  // Allocates zeroed int
  ```

- Passing: Functions take values by default; use pointers for reference.
- Avoid Nil: Check `if ptr != nil` to prevent panics.
- Arrays/Slices: Slices are reference types; arrays are value.

## Concurrency Basics

- Goroutines: Lightweight threads.

  ```go
  go func() {
      fmt.Println("Concurrent")
  }()
  ```

- Channels: Communication.

  ```go
  ch := make(chan int)
  go func() { ch <- 42 }()
  val := <-ch  // Receive
  ```

- Buffered Channels: `make(chan int, 10)` for capacity.
- Close: `close(ch)`; check with `v, ok := <-ch`.

## Testing and Benchmarking

- Unit Tests: Files end in `_test.go`.

  ```go
  func TestAdd(t *testing.T) {
      if add(2, 3) != 5 { t.Error("Failed") }
  }
  ```

- Run Tests: `go test`.
- Benchmarks:

  ```go
  func BenchmarkAdd(b *testing.B) {
      for i := 0; i < b.N; i++ { add(2, 3) }
  }
  ```

- Table-Driven: Use slices of structs for inputs/expected.
- Mocks: Use interfaces for dependency injection.

## Synchronization Primitives

- Mutex:

  ```go
  var mu sync.Mutex
  mu.Lock()
  // Critical section
  mu.Unlock()
  ```

- RWMutex: For read-heavy; `RLock()`/`RUnlock()`.
- WaitGroup:

  ```go
  var wg sync.WaitGroup
  wg.Add(1)
  go func() { defer wg.Done() }()
  wg.Wait()
  ```

- Once: `sync.Once` for single execution.
- Atomic: `atomic.AddInt64(&val, 1)` for lock-free ops.

## Reflection

- Basics: Package `reflect`.

  ```go
  v := reflect.ValueOf(x)
  t := reflect.TypeOf(x)
  ```

- Inspect/Modify:

  ```go
  if v.CanSet() { v.SetInt(42) }
  ```

- Tags: In structs, e.g., `json:"name"`.

  ```go
  field := t.Field(0)
  tag := field.Tag.Get("json")
  ```

- Dynamic Calls: `v.MethodByName("Func").Call([]reflect.Value{})`.
- Use Sparingly: Performance hit; prefer static types.

## Advanced Concurrency and Optimization

- Select:

  ```go
  select {
  case v := <-ch1: // Handle
  case <-ch2:
  default:
  }
  ```

- Context: For cancellation/timeouts.

  ```go
  ctx, cancel := context.WithTimeout(context.Background(), time.Second)
  defer cancel()
  // Pass ctx to goroutines
  ```

- Worker Pools: Channels + WaitGroups for parallelism.
- Profiling: `go tool pprof` for CPU/memory.
- Garbage Collection: Tune with `GOGC`; use `runtime.GC()`.
- Advanced: Fan-in/out patterns, rate limiting with tickers, error groups (e.g., via `golang.org/x/sync/errgroup`).
