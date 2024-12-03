# Higher-order functions

## Reduce

Starting from somewhere familiar.
In Haskellish pseudocode:

```
reduce(f, [], accumulator) = accumulator
reduce(f, [x], accumulator) = f(x, accumulator)
reduce(f, [x] + list, accumulator) = reduce(f, list, f(x, accumulator))
```

An inefficient pseudoimplementation in Python:

```python
def reduce(f, list, accumulator):
    if list == []:
        return accumulator
    else:
        new_accumulator = f(list[0], accumulator)
        return reduce(f, list[1:], new_accumulator)
```

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

## Currying
