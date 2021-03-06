## packages

- every Go program is made up of packages
- program start running in package `main`

```golang
package main

import (
	"fmt"
	"rsc.io/quote/v4"
)

func main() {
	fmt.Println(quote.Go())
}
```

- the package name is the same as the last element of the import path.

## import
1. factored import statement
```golang
import (
    "fmt"
    "math"
)
```
2. multiple import statement
```golang
import fmt
import math
```

## exported names

- in Go, a name is exported if it begins with a capital letter.
- for example, `Pizza` is an exported name.
- when importing a package, you can refer only to its exported name.
- any unexported names are not accessible from outside the package

## Functions
```
package main

import (
	"fmt"
)

func add(x int, y int) int {
	return x + y
}

func main() {
	fmt.Println(add(1,2))
}
```

## Functions continued
> when two or more consecutive named function parameters share a type, you can emit the type from all but the last.

```golang
package main

import (
	"fmt"
)

func add(x, y int) int {
	return x + y
}

func main() {
	fmt.Println(add(1,2))
}
```

## Multiple Results

> a function can return any number of results
```golang
package main

import "fmt"

func swap(x, y string) (string, string) {
	return y, x
}

func main() {
	a, b := swap("Hello", "World")
	fmt.Println(a, b)
}
```

## Named return values

> Go's return values may be named
> if so, they are treated as variables defined at the top of the function

> **naked return** returns the named return values.

```golang
package main

import "fmt"

func split(sum int) (x, y int) {
	x = sum + 10
	y = sum - x
	return
}

func main() {
	fmt.Println(split(12))
}
```

> naked return statements should be used only in short functions
> they can harm readability in longer functions

## Variables

> the `var` statement declares a list of variables, the type is last
> a `var` statement can be at package or function level
```golang
package main

import "fmt"

var c, python, java bool

func main() {
	var i int
	fmt.Println(i, c, python, java)
}
```

## Variables with initializers

> a `var` can include initializers, one per variable
> if an initializer is present, the type can be omitted
> the variable will take the type of the initializer

```golang
package main

import "fmt"

var i, j int = 1, 2

func test() {
	var c, java = true, "java"
	fmt.Println(c, java)
}
```

## short variable declarations

> inside a function, the `:=` short assignment statement can be used in place of a `var`
> outside a function, every statement begins with a keyword (`var`, `func`)

```golang
func test5() {
	var i, j int = 1, 3
	k := 4
	fmt.Println(i, j, k)
	c, Java := false, "java"
	fmt.Println(c, Java)
}
```

## Basic types
1. bool
2. string
3. int int8 int16 int32 int64
4. unit unit8 unit16 unit32 unit64 unitptr
5. byte // alias for unit8
6. rune // alias for int32, represents a Unicode code point
7. float32 float64
8. complex64 complex128

- `unitptr`
: an unsigned integer large enough to store the uninterpreted bits of a pointer value

> `int`, `unit`, `unitptr` are 32bits wide on 32-bit systems
> 64bits wide on 64-bit systems
> when you need an integer value you should use `int`
> unless you have a specific reason to use a sized or unsigned integer type.

## zero values.

> variables declared without an explicit initial value are given their **zero value**
> the **zero value** is:
1. 0 for numeric types
2. false for boolean type
3. "" empty string for strings

## type conversions

> the expression `T(v)` converts the value `v` to type `T`

```golang
func test7() {
	i := 42
	f := float64(i)
	u := uint(f)
	fmt.Println(u)
}
```

> in Go, between different type requires an explicit conversion.

## type inference

> when declaring a variable without sepecifying an explicit type (either `:=` or `var=`) 
> the variable's type is inferred from the value on the right hand side.

> when the right hand side of the declaration is typed, the new variable is of that same type.
```golang
var i int
j := i
```

> when the right hand side contains an untyped numeric constant
> the new variable may be an `int`, `float64`, or `complex128`
> depending on the precision of the constant

```golang
i := 42 // int
f := 3.142 // float64
g := 0.867 + 0.5i // complex128
```

## constants

> `const` keyword
> constants can be character, string, boolean or numeric values.
> **constants cannot be declared using the := syntax**

```golang
	const Pi = 3.14
	const Truth = true
```

## Numeric Constants
> Numeric Constants are high-precision values.
> an untyped constant takes the type needed by its context.

```golang
package main

import "fmt"

const (
	// Create a huge number by shifting a 1 bit left 100 places.
	// In other words, the binary number that is 1 followed by 100 zeroes.
	Big = 1 << 100
	// Shift it right again 99 places, so we end up with 1<<1, or 2.
	Small = Big >> 99
)

func needInt(x int) int { return x*10 + 1 }
func needFloat(x float64) float64 {
	return x * 0.1
}

func main() {
	fmt.Println(needInt(Small))
	fmt.Println(needFloat(Small))
	fmt.Println(needFloat(Big))
}
```
