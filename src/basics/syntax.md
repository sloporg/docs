# syntax

**learn the rules.**

---

## the law

compiler enforces this order. consistency is mandatory.

- **header:** `arch <system> <version>`
- **constants:** `const name type = value`
- **functions:** `fn name()`
- **main:** `fn main()` (required last if exists)

**example:**
```slop
arch amd64 0.0.1

const max u64 = 100

fn double(x u64) u64
  x * 2

fn main()
  res = double(10)
  print res
```

---

## indentation

**2 spaces. strict.**
tabs or incorrect spacing will result in compiler errors.

---

## comments

```slop
# one line only. keep it brief.
```

---

## literals

### numbers

```slop
67                               # integer (u64)
```

### strings

```slop
"no bugs in prod"                # string literal
```

### booleans

```slop
yea
nay
```

---

## operators

**math:** `+` `-` `*` `/` `%`
**compare:** `==` `!=` `<` `>` `<=` `>=`
**logic:** `&&` `||` `!`

---

## control flow

### if/else

```slop
if condition
  # do thing
else
  # do other thing
```

### while

```slop
while condition
  # loop forever
```

---

## functions

```slop
fn add(a u64, b u64) u64
  a + b
```

**implicit return:** last line returns. explicit `ret` is optional.

---

## reserved keywords

reserved for language use.

- `arch`, `const`
- `fn`, `ret`
- `if`, `else`, `while`
- `yea`, `nay`
