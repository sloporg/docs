# syntax

slop grammar and syntax [→](references.md#syntax)

---

## file structure [→](references.md#chunk-structure)

compiler enforces this order in every `.sl` chunk:

0x01. **header:** `arch <system> <version>`
0x02. **imports:** `use modulename`
0x03. **constants:** `const name type = value`
0x04. **type aliases:** `type alias = realtype`
0x05. **mood declarations:** `mood name`
0x06. **vibe declarations:** `vibe name`
0x07. **bunch declarations:** `bunch name` (with methods inside)
0x08. **gives implementations:** `gives vibe to type` (vibe implementations for types)
0x09. **standalone functions:** `fn name()`
0x0A. **main:** `fn main()` (required last if exists)

**example:**
```slop
arch amd64 0.0.1

use net

const maxconns u64 = 100

mood status u8
  pending = 0x00
  active = 0x01

vibe logger
  fn log(msg str)

bunch usr
  id u64
  name str
  
  # methods inside bunch, use deez for instance
  leak fn isvalid() bool
    deez.id > 0

# gives implementations (after bunch)
leak gives logger to bunch usr
  fn log(msg str)
    print "$deez.name: $msg"

# standalone functions
fn helper(x u64) u64
  x * 2

fn main()
  u = usr{id: 1, name: "alice"}
  if u.isvalid()
    u.log("ready")
```

---

## indentation

- **strict 2 spaces** - no tabs, no mixed indent
- block structure defined by whitespace
- compile error if indent incorrect
- rationale: smaller files, faster transfers, low-end friendly

---

## comments [→](references.md#comments)

```slop
# single line comment
## documentation comment (used for api docs)
```

---

## literals

### numbers

```slop
42           # integer
3.14         # float
1e10         # scientific notation (10 billion)
3.14e-5      # scientific notation (0.0000314)
0xff         # hexadecimal (255)
0xdeadbeef   # hex (3735928559)
0b1010       # binary (10)

# u256 examples
0xffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff
1e77         # near max u256
```

### strings

```slop
"yo world"            # string literal
"$var interpolation"     # variable interpolation
"$$ escape $$"           # escape $ sign
"yo " + "world"       # concatenation
```

### booleans

```slop
true
false
```

### bunch literals [→](references.md#bunch)

```slop
# explicit with colons, curly braces
u = usr{id: 1, name: "chad"}

# implicit by order
u = usr{1, "chad"}

# skip fields with ,,
bunch usr
  id u64
  name str
  age u64

u = usr{1, , 30}        # id=1, name="" (default), age=30
u = usr{, "chad", }     # id=0, name="chad", age=0
```

**syntax:** curly braces with colons `{key: value}`  
**order matters** for implicit syntax  
**cannot mix** explicit and implicit

---

## operators

### math

- `+` - addition
- `-` - subtraction
- `*` - multiplication
- `/` - division
- `%` - remainder

### comparison

- `==` - equal
- `!=` - not equal
- `<` - less than
- `>` - greater than
- `<=` - less or equal
- `>=` - greater or equal

### logical

- `&&` - and
- `||` - or
- `!` - not (also used for nah checking)

### special

- `!` - error handling operator (with blocks)
- `!var` - check if var is nah or uninitialized

---

## control flow

### if/else

```slop
if condition
  # if block
else if othercondition
  # else if block
else
  # else block
```

**note:** use `else if`, not `elif`

### while

```slop
while condition
  # loop body
```


## asm blocks [→](references.md#asm)

inline assembly (target-dependent):

```slop
arch arm64 0.0.1

fn syscall(num u64, arg u64) u64
  asm
    mov x0, num
    mov x1, arg
    svc #0
  # result in x0
```

**usage:**
- direct syscalls
- performance-critical sections
- platform-specific optimizations

**target-dependent:**
- amd64: x86-64 assembly
- arm64: arm64 assembly
- evm: inline opcodes
- svm: solana instructions

---

## attributes

### #yolo [→](references.md#yolo-attribute)

override std definitions (dangerous):

```slop
# override err bunch
#yolo
bunch err
  customfield str
  msg str

# override function
#yolo
fn print(x str)
  # custom implementation
```

**scope:** per-function or per-bunch  
**warning:** breaks std assumptions


## lowercase only [→](references.md#strict-lowercase)

- strict lowercase everywhere
- enforced at lexer level

**valid:**
```slop
usr = "alice"
cnt = 42
getdata()
```

**invalid:**
```slop
userName = "alice"   # uppercase N
user_name = "alice"  # underscore
getData()            # uppercase D
```

**exception:** uppercase allowed in string literals and comments

---

## visibility modifiers

```slop
fn validate() bool        # private (default)
leak fn getname() str     # public
```

**applies to:**
- functions
- bunches
- bunch fields
- bunch methods
- vibe implementations (`gives`)
- constants
- types
- moods

**see:** [functions.md](functions.md#visibility) for details

---

## deez keyword

`deez` references the current instance in methods (equivalent to `self` in rust, `this` in other languages).

```slop
bunch usr
  id u64
  leak name str
  
  fn greet() str
    "yo, $deez.name"    # deez.field
  
  leak fn setname(n str)
    deez.name = n          # deez.field assignment
  
  fn double() usr
    usr{id: deez.id * 2, name: deez.name}  # deez in expression
```

**usage:**
- `deez.field` - access field
- `deez.method()` - call method
- `deez` - pass entire instance

**required:** must use `deez` for all instance access in methods

---

## gives syntax

`gives` implements vibe methods for a type (og types or bunches).

### basic syntax

```slop
# define vibe
vibe logger
  fn log(msg str)
  fn logerr(msg str)

# implement for bunch
gives logger to bunch usr
  fn log(msg str)
    print deez.name
  
  fn logerr(msg str)
    loge ecode.user, deez.name

# implement for og type
gives logger to u64
  fn log(msg str)
    print deez.str()
  
  fn logerr(msg str)
    loge ecode.value, deez.str()
```

### visibility with gives

```slop
# private implementation (module-scoped)
gives logger to bunch usr
  fn log(msg str)
    print deez.name

# public implementation (exported)
leak gives logger to bunch usr
  fn log(msg str)
    print deez.name
```

**rule:** all methods in a `gives` block inherit the leak status of the `gives` declaration.

### built-in vibes

all types automatically implement built-in vibes:

```slop
# numeric types (u64, i64, etc.)
vibe numeric
  fn add(other T) T      # maps to +
  fn sub(other T) T      # maps to -
  fn mul(other T) T      # maps to *
  fn div(other T) T      # maps to /

vibe comparable
  fn eq(other T) bool    # maps to ==
  fn lt(other T) bool    # maps to <

# all types
vibe showable
  fn str() str
  fn json() str

vibe hashable
  fn hash() u64
```


---

## leak consistency rules

`leak` makes declarations exportable/public. consistency rules enforce proper visibility.

### rule 0x01: leak field requires leak type

```slop
type userid u64         # private type

leak bunch usr
  leak id userid        # ❌ ERROR: leak field with private type

# fix: leak the type
leak type userid u64

leak bunch usr
  leak id userid        # ✅ OK
```

### rule 0x02: private fields can use private types

```slop
type sessiontoken str   # private type

leak bunch session
  token sessiontoken    # ✅ OK - private field with private type
  leak active bool      # ✅ OK - public field with built-in type
```

### rule 0x03: leak method requires leak parent

```slop
bunch usr
  name str
  
  leak fn greet() str   # ❌ ERROR: leak method on private bunch
    "yo"

# fix: leak the bunch
leak bunch usr
  name str
  
  leak fn greet() str   # ✅ OK
    "yo"
```

### rule 0x04: gives leak consistency

```slop
vibe logger
  fn log(msg str)

# private gives, all methods private
gives logger to bunch usr
  fn log(msg str)
    print deez.name

# public gives, all methods public
leak gives logger to bunch usr
  fn log(msg str)
    print deez.name
```

**compiler enforces:** leak annotations must be consistent throughout declaration hierarchy.

---

## naming conventions [→](conventions.md#code-conventions)

### type names

shortened but recognizable:

```slop
bunch usr        # not user
bunch cnt        # not counter
bunch pnt        # not point
```

### variable names

shortest possible (single letter from type):

```slop
u usr            # from usr
c cnt            # from cnt
p pnt            # from pnt
```

### function names

**non-verb (getters/setters):**
```slop
fn name() str           # getter
fn setname(n str)       # setter
```

**verb-based:**
```slop
fn paint()              # action
fn ispainted() bool     # check state
```

**bool checks:** prefix with `is`
```slop
fn isinit() bool
fn isvalid() bool
fn isempty() bool
```

---

## reserved keywords

- `arch`, `use`, `const`, `type`
- `mood`, `bunch`, `vibe`, `gives`
- `if`, `else`, `while`, `for`
- `true`, `false`, `nah`
- `deez`
- `test`, `bet`
- `asm`

---

## reserved names

- `err` - error bunch type
- `e` - error variable in ! blocks (not globally reserved)

**available:**
- `error` - users can use as variable name
- `er` - recommended for error variables

---

## see also

- [types.md](types.md) - type system, bunch, mood, vibe
- [functions.md](functions.md) - function syntax, methods
- [errors.md](errors.md) - error handling, ! operator
- [references.md](references.md#syntax) - syntax design rationale
