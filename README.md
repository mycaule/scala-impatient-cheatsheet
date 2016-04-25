# Scala for the Impatient

Cheatsheet taken from Cay S. Horstmann's book "Scala the Impatient Cheatsheet".

## Operators
- Infix notation `x op y` is `x.op(y)`, postfix notation `x op` is `x.op()`.
- Only `+ - ! ~` can be prefix -- define method `unary_op`.
- Assignment `x op= y` is `x = x op y` unless defined seperately.
- Precedence depends on *first* character, except for assignments.

| /Highest  precedence/ | Other operators | * / % | + - | : | < > | ! = | & | ^ | | | Not operator | Assignements | /Lowest precedence/ |
|-----------------------|-----------------|-------|-----|---|-----|-----|---|---|---|--------------|--------------|---------------------|

- Right associative if *last* character is a colon `:`.
- `x(i) = x(j)` is `x.update(i, x.apply(j))`.
- There is no `++` or `--` for numbers. Use `x += 1; y -= 1`.
- Use `x == y` to compare objects -- it calls `equals`.

## Functions
```scala
def triple(x: Int) = 3 * x  // Parameter name: Type
val f = (x: Int) => 3 * x   // Anonymous function
(1 to 10).map(3 * _)        // Function with anonymous parameter
def greet(x: Int) {         // Without =, return type is Unit
  println("Hello, "  + x)
}
def greet(x: Int, salutation: String = "Hello") { // Default argument
  println(salutation + ", " + x)
}
// Call as greet(42), greet(42, "Hi"), greet(salutation = "Hi", x = 42)
def sum(xs: Int*) = {               // * denotes varargs
  var r = 0; for (x <- xs) r += x   // Semicolon separates statements on same line
  r                                 // No return. Last expression is value of block
}
def sum(xs: Int*): Int =            // Return type required for recursive functions
  if (xs.length == 0) 0 else xs.head + sum(xs.tail : _*)  // Sequence as varargs
```

## `for` Loops
```scala
for (i <- 1 to n) println(i)                      // i iterates through all values in 1 to n
for (i <- 1 to 9; j <- 1 to 9) println(i * 10 + j)  // Multiple iterates
for (i <- 1 to 9  if i != 5; j <- 1 to 9 if i != j) println(i * 10 + j)   // Guards
for (i <- 1 to 3; from = 4 - i; j <- from to 3) println(i * 10 + j)       // Variable
val r = for (i <- 1 to n) yield i * i       // r is a sequence 1, 4, 9, ...
for ((x, y) <- pairs) println(x + " " + y)  // Destructures pairs and other values with extractors
```

## Pattern Matching
```scala
val x = r match {
  case '0' => ...                     // Match value
  case ch if someProperty(ch) => ...  // Guard
  case e:Employee => ...              // Match runtime type
  case (x, y) => ...                  // Destructures pairs and other values with extractors
  case Some(v) => ...                 // Case classes have extractors
  case 0 :: tail => ...               // Infix notation for extractors yielding a pair
  case _ => ...                       // Default cases
}

try { ... } catch {   // Use the same syntax for catch clauses
  case _: MalformedURLException => println("Bad URL")
  case ex: IOException => ex.printStackTrace()
}
```

## Classes
```scala
class Point(val x: Double, val y: Double) {
  // Primary constructor defines and initializes fields: new Point(3, 4)
  // val or var in class or primary constructor defines property: p.x
  this() { this(0, 0) }             // Auxilary constructor
  def distance(other: Point) = {    // Method
    val dx = x - other.x; val dy = y - other.y
    math.sqrt(dx * dx + dy * dy)
  }
}
object Point {                      // Companion object
  def distance(a: Double, b: Double) = math.sqrt(a * a + b * b) // Like Java static method
  val origin = new Point(0, 0)                                  // Like Java static field
}
```
## Inheritance
```scala
class Employee(name: String) extends Person(name) {
  // Call primary constructor of superclass
  var salary = 0.0
  override def toString = super.toString + "[salary =" + salary + "]"
    // Use override when overriding a method
}

if (p.isINstanceOf[Employee]) {           // Like Java instanceOf
  val e = p.asInstanceOf[Employee]; ...   // Like Java cast(Employee)
}
if (p.getClass == classOf[Employee]) { ... } // Like Java Employee.class
```

## Traits
```scala
trait Logger {              // Traits can't have constructors
  def log(msg: String)      // Abstract method
  def info(msg: String) = log("INFO: " + msg) // Can have concrete methods
}
class App extends Logger with Auth { ... }    // Mix in any number of traits
trait TimestampLogger extends Logger {
  abstract override def log(msg: String) { // Still abstract
    super.log(new Date() + " " + msg)
  }
}
object App extends ConsoleLogger with TimestampLogger
  // App.log("Hi") calls log of last trait; super.log calls preceding trait
```

## Imports
```scala
import java.awt._                    // _ is wildcard, like * in Java
import java.awt.Color._              // RED is java.awt.Color.RED. Like Java import static
import java.awt.{Color,Font}
import java.awt.{List => AWTList}   // AWTList is java.awt.List
import java.awt.{List => _, _}      // Imports everything but List from java.awt
val x = Some(42)    // Same as scala.Some
                    // scala, scala.Predef, and java.lang are always imported
def sq(x) = { import scala.math._; pow(x, 2) }  // Imports can be anywhere
import math._       // Same as import scala.math._.Imports nest in Scala
```
