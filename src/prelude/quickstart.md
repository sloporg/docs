# quickstart

your first slop program

---

## installation

```bash
curl -l https://github.com/sloporg/sloprsc/releases/latest/download/sloprsc-darwin-arm64 -o slopc
chmod +x slopc

git clone https://github.com/sloporg/sloprsc
cd sloprsc
cargo build --release
```

---

## first program

create a file called `main.sl`:

```slop
arch amd64 0.0.1

fn main()
  print "agents write reliable code"
```

compile and run:

```bash
slopc main.sl -o main
./main
# output: agents write reliable code
```

---

## language basics

### strict 2-space indentation

```slop
fn calculate(x u64, y u64) u64
  if x > y
    ret x
  else
    y
```

### lowercase only

```slop
# valid
myvar = 42
user = "anon"

# invalid (compile error)
userName = "anon"                # uppercase letter
user_name = "anon"               # underscore not allowed
```

---

## basic types

### og

```slop
count u64 = 67                   # unsigned integer
active bool = yea                # boolean
name str = "anon"                # string literal
```

### type inference

types are inferred from literals:

```slop
name = "anon"            # inferred: str
count = 67               # inferred: u64
active = yea             # inferred: bool

name str = "anon"        # explicit type annotation
```

---

## functions

```slop
fn add(a u64, b u64) u64
  a + b                          # implicit return
```

### calling

```slop
res = add(5, 3)
print res                        # output: 8
```

---

## control flow

### if/else

```slop
fn check(age u64)
  if age >= 18
    print "adult"
  else
    print "minor"
```

### while

```slop
fn countdown(n u64)
  while n > 0
    print n
    n = n - 1
```

---

## next steps

- **0x0100** [syntax](../basics/syntax.md) - complete grammar reference
- **0x0101** [types](../basics/types.md) - type system details
- **0x0102** [functions](../basics/functions.md) - function definitions
