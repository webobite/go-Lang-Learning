# GO Lang - Learning

<img src="https://cdn.worldvectorlogo.com/logos/go-8.svg" alt="Go Logo" width="100">

## Go's basic types are

 - bool
- string
- int  int8  int16  int32  int64
- uint uint8 uint16 uint32 uint64 uintptr
- byte // alias for uint8 
- rune // alias for int32
    - // represents a Unicode code point
- float32 float64
- complex64 complex128

The example shows variables of several types, and also that variable declarations may be "factored" into blocks, as with import statements.
The int, uint, and uintptr types are usually 32 bits wide on 32-bit systems and 64 bits wide on 64-bit systems. When you need an integer value you should use int unless you have a specific reason to use a sized or unsigned integer type.

```
package main

import (
	"fmt"
	"math/cmplx"
)

var (
	ToBe   bool       = false
	MaxInt uint64     = 1<<64 - 1
	z      complex128 = cmplx.Sqrt(-5 + 12i)
)

func main() {
	fmt.Printf("Type: %T Value: %v\n", ToBe, ToBe)
	fmt.Printf("Type: %T Value: %v\n", MaxInt, MaxInt)
	fmt.Printf("Type: %T Value: %v\n", z, z)
}

```
**Output :** 
Type: bool Value: false
Type: uint64 Value: 18446744073709551615
Type: complex128 Value: (2+3i)

## Zero values

Variables declared without an explicit initial value are given their zero value.
The zero value is:
* 0 for numeric types,
* false for the boolean type, and
* "" (the empty string) for strings.

## Type conversions

The expression T(v) converts the value v to the type T.
Some numeric conversions:
var i int = 42
var f float64 = float64(i)
var u uint = uint(f)
Or, put more simply:
i := 42
f := float64(i)
u := uint(f)
Unlike in C, in Go assignment between items of different type requires an explicit conversion.

## Type inference

When declaring a variable without specifying an explicit type (either by using the := syntax or var = expression syntax), the variable's type is inferred from the value on the right hand side.
When the right hand side of the declaration is typed, the new variable is of that same type:
var i int
j := i // j is an int
But when the right hand side contains an untyped numeric constant, the new variable may be an int, float64, or complex128 depending on the precision of the constant:
i := 42           // int
f := 3.142        // float64
g := 0.867 + 0.5i // complex128

## Constants

Constants are declared like variables, but with the const keyword.
Constants can be character, string, boolean, or numeric values.
Constants cannot be declared using the := syntax.

## Numeric Constants

Numeric constants are high-precision values.
An untyped constant takes the type needed by its context.
Try printing needInt(Big) too.
(An int can store at maximum a 64-bit integer, and sometimes less.)

```
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
	// fmt.Println(needInt(Big))
}
```

## For

Go has only one looping construct, the for loop.
The basic for loop has three components separated by semicolons:
* the init statement: executed before the first iteration
* the condition expression: evaluated before every iteration
* the post statement: executed at the end of every iteration
The init statement will often be a short variable declaration, and the variables declared there are visible only in the scope of the for statement.
The loop will stop iterating once the boolean condition evaluates to false.
Note: Unlike other languages like C, Java, or JavaScript there are no parentheses surrounding the three components of the for statement and the braces { } are always required.

```
package main

import "fmt"

func main() {
	sum := 0
	for i := 0; i < 10; i++ {
		sum += i
	}
	fmt.Println(sum)
}
```

## For continued

The init and post statements are optional.

```
package main

import "fmt"

func main() {
	sum := 1
	for ; sum < 1000; {
		sum += sum
	}
	fmt.Println(sum)
}
```

## For is Go's "while"

At that point you can drop the semicolons: C's while is spelled for in Go.
```
package main

import "fmt"

func main() {
	sum := 1
	for sum < 1000 {
		sum += sum
	}
	fmt.Println(sum)
}

```

## Forever

If you omit the loop condition it loops forever, so an infinite loop is compactly expressed.
```
package main

func main() {
	for {
	}
}

```

## If

Go's if statements are like its for loops; the expression need not be surrounded by parentheses ( ) but the braces { } are required.

```
package main

import (
	"fmt"
	"math"
)

func sqrt(x float64) string {
	if x < 0 {
		return sqrt(-x) + "i"
	}
	return fmt.Sprint(math.Sqrt(x))
}

func main() {
	fmt.Println(sqrt(2), sqrt(-4))
}

```

## If with a short statement

Like for, the if statement can start with a short statement to execute before the condition.
Variables declared by the statement are only in scope until the end of the if.

```
package main

import (
	"fmt"
	"math"
)

func pow(x, n, lim float64) float64 {
	if v := math.Pow(x, n); v < lim {
		return v
	}
	return lim
}

func main() {
	fmt.Println(
		pow(3, 2, 10),
		pow(3, 3, 20),
	)
}
```
Output :
9 20

## If and else

Variables declared inside an if short statement are also available inside any of the else blocks.
(Both calls to pow return their results before the call to fmt.Println in main begins.)

```
package main

import (
	"fmt"
	"math"
)

func pow(x, n, lim float64) float64 {
	if v := math.Pow(x, n); v < lim {
		return v
	} else {
		fmt.Printf("%g >= %g\n", v, lim)
	}
	// can't use v here, though
	return lim
}

func main() {
	fmt.Println(
		pow(3, 2, 10),
		pow(3, 3, 20),
	)
}

```

Output: 
27 >= 20
9 20


## Switch

A switch statement is a shorter way to write a sequence of if - else statements. It runs the first case whose value is equal to the condition expression.
Go's switch is like the one in C, C++, Java, JavaScript, and PHP, except that Go only runs the selected case, not all the cases that follow. In effect, the break statement that is needed at the end of each case in those languages is provided automatically in Go. Another important difference is that Go's switch cases need not be constants, and the values involved need not be integers.

```
package main

import (
	"fmt"
	"runtime"
)

func main() {
	fmt.Print("Go runs on ")
	switch os := runtime.GOOS; os {
	case "darwin":
		fmt.Println("OS X.")
	case "linux":
		fmt.Println("Linux.")
	default:
		// freebsd, openbsd,
		// plan9, windows...
		fmt.Printf("%s.\n", os)
	}
}
```

## Switch evaluation order

Switch cases evaluate cases from top to bottom, stopping when a case succeeds.
(For example,
switch i {
case 0:
case f():
}
does not call f if i==0.)

```
package main

import (
	"fmt"
	"time"
)

func main() {
	fmt.Println("When's Saturday?")
	today := time.Now().Weekday()
	switch time.Saturday {
	case today + 0:
		fmt.Println("Today.")
	case today + 1:
		fmt.Println("Tomorrow.")
	case today + 2:
		fmt.Println("In two days.")
	default:
		fmt.Println("Too far away.")
	}
}
```

Switch with no condition
Switch without a condition is the same as switch true.
This construct can be a clean way to write long if-then-else chains.

```
package main

import (
	"fmt"
	"time"
)

func main() {
	t := time.Now()
	switch {
	case t.Hour() < 12:
		fmt.Println("Good morning!")
	case t.Hour() < 17:
		fmt.Println("Good afternoon.")
	default:
		fmt.Println("Good evening.")
	}
}
```

## Defer

A defer statement defers the execution of a function until the surrounding function returns.
The deferred call's arguments are evaluated immediately, but the function call is not executed until the surrounding function returns.

```
package main

import "fmt"

func main() {
	defer fmt.Println("world")

	fmt.Println("hello")
}
```
Output :

world

hello

Link : https://www.linkedin.com/pulse/golang-best-practices-defer-radhakishan-surwase/

## Stacking defers

Deferred function calls are pushed onto a stack. When a function returns, its deferred calls are executed in last-in-first-out order.
```
package main

import "fmt"

func main() {
	fmt.Println("counting")

	for i := 0; i < 10; i++ {
		defer fmt.Println(i)
	}

	fmt.Println("done")
}

```
Output 

counting

done

9

8

7

6

5

4

3

2

1

0

Link : https://go.dev/blog/defer-panic-and-recover


## Some online resources :

 1. https://go.dev/blog/playground
 2. https://go.dev/blog/declaration-syntax
