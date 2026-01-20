# functions

**inputs go in. values come out.**

---

## user functions

### definition

```slop
fn name(param1 type, param2 type) rettype
  # function body
  # last line is implicit return
```

### explicit return

use `ret` if you must leave early.

```slop
fn calculate(x u64) u64
  if x > 100
    ret 100                      # explicit return
  x                              # implicit return
```

### implicit return

typing `ret` at the end is unnecessary. concise code is preferred.

```slop
fn add(a u64, b u64) u64
  a + b
```

### call

```slop
result = calculate(67)
```

---

## lit functions

**built-in tools.**

### print

outputs text to stdout. groundbreaking stuff.

```slop
print "compiler said no"
print x
```

---

## recursion

yes, we have recursion. don't blow the stack.

```slop
fn factorial(n u64) u64
  if n <= 1
    1
  else
    n * factorial(n - 1)
```
