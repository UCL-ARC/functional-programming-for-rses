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

2.

```python

```

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

The standard python `reduce` function comes from `functools` (and is better than my toy implementation).

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

## Some other useful higher-order functions

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
print(list(flip(zip)("Hello", [1,2,3,4,5]))
```

## Partial application and Currying
