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


**how to write clean func avoiding common bugs**

**1. One function, one job**

If a function does multiple unrelated things, it's harder to test, debug, and explain out loud in an interview.

```python
# ❌ does too much
def process(data):
    data = data.dropna()
    data['solved'] = data['solved'].astype(int)
    print(data.describe())
    data.to_csv("clean.csv")
    return data

# ✅ split into single-purpose functions
def clean_data(data):
    return data.dropna()

def cast_types(data):
    data['solved'] = data['solved'].astype(int)
    return data

def save_data(data, path):
    data.to_csv(path)
```

Each function should be describable in one sentence. If you need "and" to describe it ("cleans the data *and* saves it"), split it.

**2. The mutable default argument bug — a real, famous Python trap**

Try to guess the output before I explain:

```python
def add_problem(problem, problem_list=[]):
    problem_list.append(problem)
    return problem_list

print(add_problem("Two Sum"))
print(add_problem("3Sum"))
```

You'd expect `["Two Sum"]` then `["3Sum"]`, right? But you actually get `["Two Sum"]` then `["Two Sum", "3Sum"]` — the **same list is reused across every call**, because default arguments are created *once*, when the function is defined, not fresh each time you call it. This is a genuinely famous Python gotcha, worth having ready in interviews.

Fix — always use `None` as the default, then create the list inside:

```python
def add_problem(problem, problem_list=None):
    if problem_list is None:
        problem_list = []
    problem_list.append(problem)
    return problem_list
```

**3. Meaningful names over comments**

```python
# ❌
def calc(x, y):
    return x / y * 100

# ✅
def calculate_completion_percent(solved, total):
    return solved / total * 100
```

If you need a comment to explain *what* a variable is, rename the variable instead. Comments should explain *why*, not *what* — the code itself should say what.

**4. Guard against bad inputs early (avoid silent wrong answers)**

```python
def calculate_completion_percent(solved, total):
    if total == 0:
        raise ValueError("total can't be zero")
    return solved / total * 100
```

Without this, `solved / 0` throws `ZeroDivisionError` deep inside your code with a confusing stack trace. Checking early gives a clear, intentional error instead — this is exactly the instinct interviewers look for when they hand you an edge case ("what if the input list is empty?").

**5. Avoid deep nesting — use early returns**

```python
# ❌ nested and hard to follow
def check_pattern(pattern, status_dict):
    if pattern in status_dict:
        if status_dict[pattern] == "done":
            return True
        else:
            return False
    else:
        return False

# ✅ flat, easier to read and explain out loud
def check_pattern(pattern, status_dict):
    if pattern not in status_dict:
        return False
    return status_dict[pattern] == "done"
```

Fewer indentation levels = easier for *you* to explain line-by-line in an interview, and easier for an interviewer to follow your reasoning live.

**6. Don't mutate what you don't own**

```python
# ❌ silently changes the caller's original list
def mark_done(patterns):
    patterns.append("done")
    return patterns

# ✅ return a new list instead
def mark_done(patterns):
    return patterns + ["done"]
```

This connects back to the mutable vs immutable discussion from earlier — lists are mutable, so if you modify one *inside* a function without meaning to, it silently changes the original outside too. This is a very real bug source, not a theoretical one.

**Quick check to make sure this landed**: in the `mark_done` example above, if I call `mark_done(my_patterns)` and *don't* reassign the result (`my_patterns = mark_done(my_patterns)`), does the ❌ version still modify `my_patterns` outside the function? Why or why not?