# conventions

**how to write valid slop.**

---

## naming

### variables
lowercase only. snake_case permitted.
- `count` (preferred)
- `user_name` (accepted)
- `userName` (compiler error)
- `UserName` (compiler error)

### functions
lowercase. avoid underscores if possible.
- `calculate` (preferred)
- `readfile` (better than `read_file`)

### files
lowercase. always `.sl`.
- `main.sl`
- `math.sl`

---

## no caps allowed

**zero capitals.**
- identifiers: `username` not `userName`
- acronyms: `httpserver` not `HTTPServer`
- constants: `max_size` not `MAX_SIZE`

**why?**
removes ambiguity and enforces consistency across the ecosystem.

---

## formatting

### indent
- **2 spaces** strictly.
- tabs or mixed spacing will trigger compiler errors.

### spacing
- space around operators: `x + y`
- space after comma: `fn(a, b)`

---

## strings

**double quotes only.**
- `"hello"` (valid)
- `'hello'` (invalid)

---

## comments

**single line only.**
- `#` starts a comment
- space after hash required: `# good`
- vertical alignment recommended for block comments

```slop
x = 10                           # aligned
y = 20                           # clean
```

---

## og

**built-in types.**

- integers: `u64`
- logic: `bool`
- text: `str`

---

## file structure

**order is enforced.**

- **header:** `arch <system> <version>`
- **constants:** `const name type = value`
- **functions:** `fn name()`
- **main:** `fn main()` (must be last)
