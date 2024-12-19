# Exercises

## Lesson objectives

- Hands on experience with the functional style.

## Exercise 1. Luhn Algorithm

> [!TIP]
> Try to write one-liner function definitions where you simply name a function built up from pure or higher-order functions.

Consider the following slight simplification of the [Luhn algorithm](https://en.wikipedia.org/wiki/Luhn_algorithm), for validating large numbers (account numbers, credit card numbers, etc):

- Double the value of every second digit from the right.
- Add the digits of the doubled values and original values.
- Calculate the remainder when the sum is divided by 10.
- If the result equals zero, the number is valid.

Copy this into a file and run `pytest` over it.
Code until you go green!

```python
def luhn_ok(i: int) -> bool:
    # your code here
    pass


def test_luhn():
    assert luhn_ok(4012888888881881)
    assert not luhn_ok(4012888888881881)
```

<details><summary>Click for a hint...</summary>

```python
def to_digits(i: int) -> list[int]:
    """Convert a large number i into a list of it's digits."""
    pass


def test_to_digits():
    assert to_digits(1234567) == [1, 2, 3, 4, 5, 6, 7]


def double_every_other(li: list[int]) -> list[int]:
    """Double every other value in a list."""
    pass


def test_double_every_other():
    assert double_every_other([1, 2, 3, 4]) == [1, 4, 3, 8]
```

</details>
