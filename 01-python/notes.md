# Python Fundamentals for Data Science

## Concept
Python for DS isn't about knowing every language feature — it's about being fluent in the handful of things you'll use constantly: lists/dicts for data wrangling, list comprehensions instead of loops, functions that take/return dataframes or arrays, and enough OOP to read sklearn's source when you're debugging.

The habit that actually matters: writing code that's *readable six months later*, because you'll be re-explaining this exact code in interviews.

## Example
Instead of writing a loop to filter and transform:
```python
# Loop version
result = []
for x in data:
    if x > 0:
        result.append(x ** 2)

# Comprehension version (what you should default to)
result = [x ** 2 for x in data if x > 0]
```
Both do the same thing, but the comprehension is what you'll see in real codebases and in interview code review.

## Common interview question
**Q:** What's the difference between a list and a tuple, and when would you use each?
**A:** Lists are mutable (can be changed after creation), tuples are immutable. Use tuples for fixed collections (like coordinates, or dict keys since they need to be hashable) and lists when you expect the collection to grow/shrink or be modified.

## Gotchas / things I got wrong first
- Mutable default arguments (`def f(x, lst=[])`) — the list persists across calls, which caused a bug I didn't expect.
- Confusing `is` (identity) with `==` (equality) — matters for None checks (`if x is None`, not `if x == None`).