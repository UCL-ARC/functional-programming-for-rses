# Higher-order functions

## Lesson objectives:

- Start from what is already familiar.
- Know the names of the common higher-order function abstractions.
- Understand partial application and Currying.

## Map

Starting from somewhere familiar.
Haskellish pseudocode:

```
f: Function of one argument
map(f, []) = []
map(f, [x]) = [f(x)]
map(f, [x] + rest_of_list) = [f(x)] + map(f, rest_of_list))
```

Here is an inefficient pseudoimplementation in Python:

```py
def map(f, li):
    if is_empty(li):
        return li
    return [f(li[0])] + map(f, li[1:])
```

Where a nice Pythonic way to check for an empty list is:

```py
def is_empty(li):
    return not len(li)
```

<details><summary>Type signature, if you like that sort of thing...</summary>

```python
T, R = TypeVar("T"), TypeVar("R")
def map(f: Callable[[T] R], li: list[T]) -> list[R]:
    ...
```

</details>

Though you're probably already familiar with `map`.
It lives in the standard library! So you shouldn't redefine it!
It takes a more generic `iterable` type (it's not restricted to `list`).

> [!NOTE]
> For non-Pythonistas, the builtin `map` doesn't return a list, but a more general `map object`. You can force this to become a list -- if you like.
>
> ```py
> squares = map(lambda x: x**2, range(1,101))
> print(type(squares))
> squares = list(squares)
> print(type(squares))
> print(squares[:10])
> ```

### Quiz

1. Is `map` a pure function?

## Reduce

In Haskellish pseudocode:

```
f: Function of two arguments
reduce(f, [], accumulator) = accumulator
reduce(f, [x], accumulator) = f(x, accumulator)
reduce(f, [x] + rest_of_list, accumulator) = reduce(f, rest_of_list, f(x, accumulator))
```

An inefficient pseudoimplementation in Python:

```python
def reduce(f, li, accumulator):
    if not len(li):
        return accumulator
    new_accumulator = f(li[0], accumulator)
    return reduce(f, li[1:], new_accumulator)
```

<details><summary>Type signature, if you like that sort of thing...</summary>

```python
T, R = TypeVar("T"), TypeVar("R")
def reduce(f: Callable[[T, R] R], li: list[T], accumulator: R) -> R:
    ...
```

</details>

Different languages have conventions for the order of arguments.

The standard python `reduce` function comes from [the builtin `functools` library](https://docs.python.org/3/library/functools.html) (and is better than my toy implementation).

```python
from functools import reduce

my_list = [1, 2, 3, 4]
reduce(lambda a, b: a + b, my_list, 0)
```

The `functools.reduce` keyword argument for "accumulator" is `initial`.
The "functional programming" nomenclature for that is "accumulator" and that's what you'll find if you Google in general.

### Quiz

Using `functools.reduce`, here are some re-implementations of standard functions.
Can you name them?

1.

```python
reduce(lambda a, b: a * b, list, 1)
```

2.

```python
reduce(lambda a, b: a if a > b else b, list, 0)
```

3.

```python
reduce(lambda l, x: l + [x] if f(x) else acc, list, [])
```

<details>

```python
def is_even(x: int):
    return x % 2 == 0

T = TypeVar("T")
def filter(f: Callable[[T], bool], li: list[T]) -> list[T]:
    return reduce(lambda l, x: l + [x] if f(x) else acc, li, [])

numbers = list(range(1,101))
print(filter(is_even, numbers))
```

</details>

4.

```python
reduce(lambda l, x: l + [f(x)], li, [])
```

<details>

```python
T, R = TypeVar("T"), TypeVar("R")
def map_via_reduce(f: Callable[[T], R], li: list[T]) -> list[R]:
    return reduce(lambda l, x: l + [f(x)], li, [])

squares = list(map(lambda x: x**2, [1, 2, 3, 4]))
squares_via_reduce = map_via_reduce(lambda x: x**2, [1, 2, 3, 4])
```

</details>

> [!NOTE]
> This is a nice abstraction which we will revisit in a later lesson.

## Some other useful higher-order functions

### Take / Head / Tail / Reverse

In Python these functions are rather trivial but they're useful to define:

```python
def take(n, li):
    return li[:n]

def head(li):
    return li[0] if len(li) else None
```

> [!NOTE]
> Slight difference here with what you might expect from unix `head` of a file, or `pandas.DataFrame.head`.

There is also `tail`:

```python
def tail(li):
    return li[1:]
```

`reversed` is builtin in Python.
Though -- as with `map` -- it doesn't return a `list` by default but a more general `reverseiterator`.
You can force it to be a `list` if you need it, with:

```python
natural_numbers = list(range(1,11))
print(type(reversed(natural_numbers)))

countdown = list(reversed(natural_numbers))
print(type(countdown))
```

### Flip

Take the arguments and reverse them.

```python
def flip(f: Callable) -> Callable:
    def new(*args):
        return f(*args[::-1])
    return new
```

```py
print(list(zip("Hello", [1,2,3,4,5])))
print(list(flip(zip)("Hello", [1,2,3,4,5])))
```

## Partial application and Currying

Currying is named for [Haskell Curry](https://en.wikipedia.org/wiki/Haskell_Curry).
It is very useful, though might not seem it at first.
_To Curry_ a function of n arguments is to create a chain of n functions each taking one argument.

In Haskell, it is a built in feature of the syntax.
In Python we don't have it by default.
There are a number of ways to get there (or close to there).

To explain, we're going to use the most pointless function:

```python
def add(x, y):
    return x + y
```

Let's imagine we wanted (for some inexplicable reason) to add 2.

```python
def add2(y):
    return add(2, x)
```

The silliness of this example is actually directly related to the idea of Currying.
This approach doesn't scale, we're going to be defining loads of functions.

_A_ way of doing this in Python is via a decorator.

Unfortunately, a Curry decorator isn't implemented in `functools` so we _could_ use a third-party library: [`toolz`](https://toolz.readthedocs.io/en/latest/).

> [!NOTE]
> Toolz likes to add 'z's in place of 's'.
> So take care not to lose track of what's coming from where:
>
> ```python
> from functools import reduce  # built into Python
> from toolz.functooz import curry  # needs `pip install`ing and has a z
> ```

Here's what a Curry decorator looks like:

```python
# ![uv] pip install toolz

from toolz.functoolz import curry

@curry
def add(x, y):
    return x+y

print(add(2))
print(add(2)(3))
```

Something that _is_ builtin is the more general **partial application**.
Partial application is Currying but where we are not limited to a chain of functions with one argument each, we can provide k arguments and we will obtain a function which takes n-k arguments.

Consider a Currying a function with many arguments:

```python
@curry
def add_many_numbers(a, b, c, d, e, f):
    return a+b+c+d+e+f

add_many_numbers(1)(2)(3)(4)(5)(6)
```

With partial application:

```python
from functools import partial

add2 = partial(add_many_numbers, a=1, b=1)
print(type(add2))
```

Currying or partial application, really comes into it's own when used in conjunction with, e.g. `map`:

```python
double_offset = list(map(partial(add, 2), list(range(1,11))))
```

Note that we've been defining the `add` function ourselves.
But better is to use the [`operator` library that comes built into Python](https://docs.python.org/3/library/operator.html).

```python
from operator import add, sub, pow, mul, truediv, floordiv
```

Or leave the namespace because `Namespaces are one honking great idea`.

```python
import operator
from functools import partial

double_offset = list(map(partial(operator.add, 2), list(range(1,11))))
```

We leave it to the student to decide whether using `partial` and `operator.add` is more readable than a `lambda` here:

```python
double_offset = list(map(lambda x: 2+x, list(range(1, 11))))
```

But for more complex functions or your own custom functions, bear `functools.partial` in mind!

### Extra credit: our own `@curry` decorator

If we wanted to avoid third-party-libaraies for some reason, it's relatively simple to make our own decorator for Currying using `functools.partial`.
Here we leave the namespace for clarity:

```python
import functools

def curry(f):
    @functools.wraps(f)
    def curried_f(*args, **kwargs):
        return functools.partial(f, *args, **kwargs)
    return curried_f


@curry
def add(a, b):
    return a+b

print(add(1)(2))
```
