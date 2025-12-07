# Go (Golang)

- [Go (Golang)](#go-golang)
  - [Installation and Hello World](#installation-and-hello-world)
  - [Variables and Constants](#variables-and-constants)
  - [Data Types](#data-types)
  - [Control Flow](#control-flow)
  - [Functions](#functions)
  - [Error Handling](#error-handling)
  - [Structs and Methods](#structs-and-methods)
  - [Interfaces](#interfaces)
  - [Pointers](#pointers)
  - [Packages and Modules](#packages-and-modules)
  - [Concurrency Basics](#concurrency-basics)
  - [Testing and Benchmarking](#testing-and-benchmarking)
  - [Synchronization Primitives](#synchronization-primitives)
  - [Reflection](#reflection)
  - [Advanced Concurrency and Optimization](#advanced-concurrency-and-optimization)

Assume Go 1.21+ for modern features. Use `go run main.go` to execute examples where applicable.

## Installation and Hello World

- Install Go: Download from [golang.org/dl](https://golang.org/dl). Set `$GOPATH` and `$GOROOT` if needed (defaults work for most).
- Hello World:

  ```go
  package main
  import "fmt"

  func main() {
      fmt.Println("Hello, World!")
  }
  ```

- Compile & Run: `go build main.go` creates executable; `go run main.go` for quick execution.
- Comments: `// single-line` or `/* multi-line */`.

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

## Packages and Modules

- Import:

  ```go
  import (
      "fmt"
      "github.com/user/repo"  // Remote
  )
  ```

- Modules: `go mod init module/name` to start.
- Visibility: Capitalized names exported.
- Aliases: `import f "fmt"`.
- Init Func: `func init() { }` runs before main.

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
