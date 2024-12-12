# The Functional Paradigm

<!--

# Programming paradigms

- Procedural.
  - Start, do step 1, do step 2, ... end.
- Object orientated.
  - Operations and data together.
  - Nouns!
- Functional...

---

# Functions as first class citizen

- "First class citizen" in CS parlance means "thing which is basic to the language, can be named (or not) and can be passed around."

---

-->

## Lesson objectives:

- Thinking about OOP as a design choice.
- Contrasting mathematical functions with software functions.
- The three keys of functional programming:
  1.  Pure functions.
  2.  Immutable data structures.
  3.  Side-effect code bridge.
- λ.

## Object-orientated programming

In OOP we design our code around _classes_ (or objects).
These cluster data and/or processing code into one place.
For good OOP design, we typically create classes for _nouns_ that we encounter when talking about our code in natural language.

E.g.

> The API starts by establishing a connection to a database. It writes to the database when it recieves a PUT request.

In this example, there are several nouns which we might use to group our code.
We might have an `WebAPI` class, we probably want a `Database` class -- or maybe even better a `DatabaseConnection` class.

We usually have methods (processing code) that are the verbs we encounter.
In the same example: we might have a `DatabaseConnection::open` method, and probably a `write()` method somewhere.

Here's the **same sentence**, but in Pythonic pseudocode:

```py
class WebAPI:
   def start(self):
      self.db = DatabaseConnection()
      self.db.open()

   def recieve_PUT(self, data):
      self.db.write(data)

class DatabaseConnection:
   def open():
      ...

   def write():
      ...
```

Here is a rough mindmap of the concepts of OOP stacked roughly in order of heirarchy:

![](assets/oop-concepts.svg)

This is a choice that we have made to lay out our code this way.
We could have made another decision, to write everything into global functions, for instance.

> [!TIP]
> The takehome is that using OOP is a _design choice_.

## The functional paradigm

Functional programming is often said to be a paradigm.
It probably is, but for now consider it as an alternative to OOP.

Let's think about a redesigning the OOP way of arranging code.

![](assets/refactor-oop.svg)

We will keep the types, and primative things but remove the idea of the object as a _first-class citizen_.
We need some structure to use to organise our code around.
This is going to be the ✨ function ✨.

In software parlance 'function' means something that is _close to_ but not exactly the mathematical definition of 'function'.

#### Side note

We might naturally write some C++ like this:

```c++
/**
 * @brief Does a thing
 *
 * @param [in] someInput value
 * @param [in/out] vector of doubles
 */
 void doTheThing(int someInput, std::vector<double> &array)
 {
   for (size_t i=0; i<array.size(); ++i) {
      array[i] *= double(someInput);
   }
   return;
 }
```

And C++ would call that thing "a function".

### A quick refresher of some maths you probably did a long time ago

In formal mathematics:

> A function is a relation between two sets $X$ and $Y$ which assigns each element of it's domain, $X$, to one and only one element of codomain $Y$.
>
> $$
> f : X \mapsto Y
> $$

![](https://upload.wikimedia.org/wikipedia/commons/thumb/d/df/Function_color_example_3.svg/1280px-Function_color_example_3.svg.png)
By Function_color_example_3.gif: Wvbailey.The original uploader was Wvbailey at English Wikipedia.derivative work: Zerodamage - This file was derived from: Function color example 3.gif:, CC BY-SA 3.0, https://commons.wikimedia.org/w/index.php?curid=20802095

A trivial example (that is not spectacularly instructive):

$$
y = f(x) := x^2; \,\, x\in \mathcal N
$$

relates every natural number to its square.

Another way of defining a function instead of by formulae envolving the element is by specifiying each relation:

$$
\delta : \mathbb R \mapsto \left\lbrace 0, 1\right\rbrace\\
\delta(0) := 1\\
\delta(x) := 0\, \forall x \neq 0
$$

### OK, great but what about coding?

This fundamental math definition of a function has an interesting property:

> A mathematical function assigns to **one and only one** element of the codomain.

I.e. it always gives you back the same thing for a given input.

A function cannot see anything but its domain (input arguments) and cannot do anything but point to an element in the codomain (return something).

- The global program state?
- The time of day that I ran the code?
- Environment variables?
- File I/O?

## Cornerstones of functional programming

### Pure functions

The core, "first class citizen" in functional programming is the pure function.

A function is pure if it takes input and gives output, and has no other effect, nor is affected by anything other than the inputs.

### Immutable data structures

### Side-effect free - side-effectful bridge

## λ

In the following examples, we'll be using the λ notation.
This is conventionally used for an _anonymous function_.

In many programming languages, _values_ can be anonymous (not assigned to a name).

```py
print(5) # 5 is an anonymous value
```

λ is simply a function without a name.
Confusingly, we can also assign a name (in Python, at least).
But the point is, we don't have to.
The Python syntax is:

```py
lambda x: expression
```

So, for example:

```py
square = lambda x: x**2
pow = lambda x, p: x**p
```

Those are equivalent to:

```py
def square(x):
   return x**2

def pow(x, p):
   return x**p
```

Note that these are _good_ examples of λ functions (short, readable) but bad code examples because we've assigned them names.
More sensible use of a λ is as arguments to _higher order functions_:

```py
reduce(lambda x, y: x+y, list, 0)
```
