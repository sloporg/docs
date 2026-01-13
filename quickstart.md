# quickstart

your first slop program

---

## installation

```bash
# download compiler (coming soon)
curl -l https://github.com/sloporg/sloprsc/releases/latest/download/slopc-darwin-arm64 -o slopc
chmod +x slopc

# or build from source
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
  name = "world"
  print "yo $name"
```

compile and run:

```bash
slopc main.sl -o main
./main
# output: yo world
```

---

## language basics

### every slop file starts with

```slop
arch <system> <version>
```

**systems:**
- `amd64` - x86-64 targets
- `arm64` - arm64 targets
- `evm` - ethereum virtual machine
- `svm` - solana virtual machine

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
myvar = 67
user = "anon"

# invalid (compile error)
username = "anon"   # camelcase (uppercase letter)
userbro = "bro"     # underscores
```

---

## basic types

### og types

```slop
count u64 = 67          # unsigned integer
price f64 = 3.14        # float
active bool = true      # boolean
letter ch = 'a'         # character
```

### strings

```slop
name str = "anon"
msg str = "yo $name"        # interpolation
escaped str = "price: $$100"     # escape $ sign
combined str = "yo " + name   # concatenation
```

### arrays

```slop
numbers = [1, 2, 3, 4, 5]
first = numbers[0]           # index access
slice = numbers[1..3]       # [2, 3, 4]
```

### maps

```slop
ages        = {"anon": 67, "bro": 67}   # map literal
age         = ages["anon"]               # 67
hasanon     = ages.has("anon")           # true
```

### type inference

types are inferred from literals:

```slop
name        = "anon"                     # inferred: str
count       = 67                         # inferred: u64
price       = 3.14                       # inferred: f64
active      = true                       # inferred: bool

name str    = "anon"                     # explicit type annotation
```

### tuples

```slop
pair        = (67, "anon")               # tuple literal
first       = pair[0]                    # 67
second      = pair[1]                    # "anon"
anon, bro   = pair                       # destructuring
```

---

## functions

### definition

```slop
fn add(a u64, b u64) u64
  a + b        # implicit return
```

### multiple returns

```slop
fn split(name str) str, str
  # returns two strings
  "first", "second"
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

## error handling

```slop
fn readfile(path str) str
  data = fs.read(path) !
    logi "file not found: $path"
    ret ""
  data
```

---

## using standard library

```slop
arch amd64 0.0.1

use env
  io

fn main()
  args = env.args()
  
  if args.len() < 2
    print "enter name:"
    name = io.readline()
  else
    name = args[1]
  
  print "yo $name"
```

---

## next steps

1. [syntax.md](syntax.md) - complete grammar reference
2. [types.md](types.md) - type system details
3. [functions.md](functions.md) - functions and lit functions

---

## examples directory

```slop
# compile all examples
slopc examples/*.sl

# run specific example
slopc example.sl && ./example
```

---

## help

```bash
# compiler help
slopc --help

# version
slopc --version
```

welcome to slop! 🎉