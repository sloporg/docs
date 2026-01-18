# types

**core types.** minimal starting set.

---

## og

built-in types.

### u64

unsigned 64-bit integer. primary numeric type for v0.0.1.

```slop
x u64 = 67
y = 100                          # inferred as u64
```

**math:** `+` `-` `*` `/` `%`

### bool

truth is binary.

```slop
active bool = yea
flag = nay
```

**logic:** `&&` `||` `!`

### str

text. read-only slices.

```slop
msg str = "lowercase or die"
```

---

## type inference

compiler infers types where unambiguous.

```slop
a = 10                           # u64
b = yea                          # bool
c = "txt"                        # str
```

**explicit types** required for functions.
