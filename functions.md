# functions

functions and lit functions in slop [→](references.md#functions)

---

## user functions

### definition

```slop
fn name(param1 type, param2 type) rettype
  # function body
  # last line is implicit return
```

### explicit return

```slop
fn calculate(x u64) u64
  if x > 100
    ret 100  # explicit return
  x  # implicit return
```

### multiple returns [→](references.md#return-types)

**single return - no parens:**
```slop
fn add(a u64, b u64) u64
  a + b

fn getname() str
  "alice"
```

**multiple returns - parens required:**
```slop
fn split(input str) (str, str)
  ("first", "second")

fn readfile(path str) (str, err)
  if !exists(path)
    ret ("", err{code: ecode.notfound, msg: "not found"})
  (content, nah)
```

**return statements match:**
```slop
ret 42              # single
ret "value"         # single
ret (a, b)          # multiple
ret (data, nah)     # multiple
```

### call

```slop
result = calculate(42)  # parens required
a, b = split("data")    # multiple return destructuring
```

---

## methods [→](references.md#methods)

### defining methods inside bunches

```slop
bunch pnt
  x u64
  y u64
  
  # private method (default)
  fn validate() bool
    deez.x > 0 && deez.y > 0
  
  # public method
  leak fn distance() f64
    sqrt(deez.x * deez.x + deez.y * deez.y)
  
  # method with parameters
  leak fn add(other pnt) pnt
    pnt{x: deez.x + other.x, y: deez.y + other.y}

# usage
p = pnt{3, 4}
if p.validate()
  dist = p.distance()
  p2 = p.add(pnt{1, 2})
```

**syntax:** methods defined inside bunch block, use `deez` for instance reference

### methods on og types via gives

og types can be extended with methods using `gives`:

```slop
# define vibe
vibe math
  fn double() u64
  fn triple() u64

# extend og type
leak gives math to u64
  fn double() u64
    deez * 2
  
  fn triple() u64
    deez * 3

# usage
n u64 = 21
doubled = n.double()  # 42
tripled = n.triple()  # 63
```

**cannot add methods directly to og types** - must use `gives` with vibe

---

## naming conventions [→](conventions.md#code-conventions)

### non-verb fields (getters/setters)

```slop
bunch usr
  id u64
  privname str
  
  # getter
  leak fn name() str
    deez.privname
  
  # setter
  leak fn setname(n str)
    deez.privname = n

# usage
u = usr{id: 1, privname: "alice"}
currentname = u.name()
u.setname("bob")
```

### verb-based methods

```slop
bunch canvas
  painted bool
  
  # action
  leak fn paint()
    deez.painted = true
  
  # check state
  leak fn ispainted() bool
    deez.painted

# usage
c = canvas{painted: false}
c.paint()
if c.ispainted()
  print "ready"
```

### bool checks - prefix with `is`

```slop
leak fn (u usr) isinit() bool
leak fn (u usr) isvalid() bool
leak fn (s str) isempty() bool
leak fn (p pnt) iszero() bool
```

**rationale:** english-friendly, clear intent

---

## visibility [→](references.md#visibility)

```slop
bunch usr
  id u64
  leak name str       # public field
  
  fn validate() bool        # private method
  leak fn getid() u64       # public method
  hood fn helper() str      # internal (experimental)
  wild fn apimethod() str   # external (experimental)
```

**default:** private  
**leak:** public  
**hood:** internal to package  
**wild:** external only  

---

## lit functions [→](references.md#lit-functions)

special built-in functions without parentheses:

### print

```slop
print "yo world"
print x             # calls x.str() in print context
print x + y         # evaluates first, then prints
```

**output:** stdout

### log functions

```slop
log "debug message"              # unstructured (discouraged)
logi icode.started, "server up"  # info (jsonl)
loge ecode.notfound, "missing"   # error (jsonl)
logw wcode.fallback, "defaults"  # warning (jsonl)
logd "debug details"             # debug (experimental)
```

**see:** [errors.md](errors.md#log-functions) for details

### sleep

```slop
sleep 5s         # sleep 5 seconds
sleep 100ms      # sleep 100 milliseconds
sleep 1e6ns      # sleep 1 millisecond (scientific notation)
```

### yeet [→](references.md#yeet)

```slop
yeet ecode.unauthorized, "access denied"
```

**signature:** `yeet ecode, str`  
**behavior:** logs via loge, then os.exit(1)

---

## closures (experimental)

```slop
# lambda without fn keyword
double = (x u64) u64
  x * 2

# with parameters
add = (a u64, b u64) u64
  a + b

# usage
result = double(21)  # 42
sum = add(10, 20)    # 30
```

---

## recursion

```slop
fn factorial(n u64) u64
  if n <= 1
    1
  else
    n * factorial(n - 1)

fn fibonacci(n u64) u64
  if n <= 1
    n
  else
    fibonacci(n - 1) + fibonacci(n - 2)
```

---

## function parameters

- max 16 params (native targets)
- max 8 return values (native targets)
- bunch fields count towards total
- compile error if exceeded

**encouragement:** use bunch to group related params

```slop
# instead of many params
fn process(id u64, name str, active bool, email str, age u64)
  # too many params

# use bunch
bunch usr
  id u64
  name str
  active bool
  email str
  age u64

fn process(u usr)
  # cleaner
```

---

## operator overloading [→](references.md#operator-overloading)

implement built-in vibe methods to enable operators:

```slop
bunch pnt
  x u64
  y u64

# implement numeric vibe for arithmetic operators
gives numeric to bunch pnt
  fn add(other pnt) pnt
    pnt{x: deez.x + other.x, y: deez.y + other.y}

# implement comparable vibe for comparison operators
gives comparable to bunch pnt
  fn eq(other pnt) bool
    deez.x == other.x && deez.y == other.y
  
  fn lt(other pnt) bool
    deez.x < other.x

# compiler maps vibe methods to operators:
p1 = pnt{3, 4}
p2 = pnt{1, 2}
p3 = p1 + p2   # → p1.add(p2) from numeric vibe
if p1 == p2    # → p1.eq(p2) from comparable vibe
  print "equal"
if p1 < p2     # → p1.lt(p2) from comparable vibe
  print "less"
```

**built-in vibe method mappings:**
- **numeric:** `add` → `+`, `sub` → `-`, `mul` → `*`, `div` → `/`, `mod` → `%`
- **comparable:** `eq` → `==`, `ne` → `!=`, `lt` → `<`, `gt` → `>`, `le` → `<=`, `ge` → `>=`
- **indexing:** `get` → `[]`

**see:** [types.md](types.md#operator-overloading) for details

---

## built-in vibe methods [→](references.md#universal-methods)

vibe-coded on all bunches via built-in vibes (lazy compilation):

```slop
bunch usr
  id u64
  name str
  
  # compiler provides via built-in vibes:
  # showable vibe:
  leak fn str() str         # string representation
  leak fn json() str        # json serialization
  
  # hashable vibe:
  leak fn hash() u64        # hash value
  
  # sizable vibe:
  leak fn size() u64        # memory size
  
  # typeable vibe:
  leak fn type() str        # type name
  
  # checkable vibe:
  leak fn isinit() bool     # init check
```

**usage:**
```slop
u = usr{id: 1, name: "alice"}
print u.str()   # "usr{id: 1, name: alice}"
print u.json()  # "{\"id\":1,\"name\":\"alice\"}"
print u.type()  # "usr"
```

**see:** [types.md](types.md#built-in-vibes) for complete list

---

## initable vibe (optional)

opt-in init hooks for bunches:

```slop
bunch usr
  id u64
  name str
  
  # implement initable vibe methods
  fn preinit()
    logi icode.started, "creating user"
  
  fn init()
    # custom init logic
    if deez.id == 0
      yeet ecode.invalid, "id required"
  
  fn postinit()
    logi icode.complete, "user $deez.id created"

# execution on construction:
u = usr{id: 1, name: "alice"}
# 0x01. preinit()
# 0x02. fields set
# 0x03. postinit()
```

**see:** [types.md](types.md#initable-vibe) for details

---

## examples

### example 0x01: method chaining

```slop
bunch builder
  data str
  
  leak fn add(s str) builder
    deez.data = deez.data + s
    deez
  
  leak fn build() str
    deez.data

# usage
result = builder{data: ""}
  .add("yo ")
  .add("world")
  .build()
```

### example 0x02: validation methods

```slop
bunch usr
  id u64
  email str
  
  fn isvalid() bool
    deez.id > 0 && deez.email.has("@")
  
  leak fn validate() err
    if deez.id == 0
      ret err{code: ecode.invalid, msg: "id required"}
    if !deez.email.has("@")
      ret err{code: ecode.invalid, msg: "invalid email"}
    nah

# usage
u = usr{id: 1, email: "user@example.com"}
er = u.validate()
if er.code != ecode.none
  loge er.code, er.msg
```

### example 0x03: operator overloading

```slop
bunch vec
  x f64
  y f64

# implement numeric vibe for operators
gives numeric to bunch vec
  fn add(other vec) vec
    vec{x: deez.x + other.x, y: deez.y + other.y}
  
  fn mul(scalar f64) vec
    vec{x: deez.x * scalar, y: deez.y * scalar}

# custom method (not from vibe)
bunch vec
  x f64
  y f64
  
  leak fn magnitude() f64
    sqrt(deez.x * deez.x + deez.y * deez.y)

# usage
v1 = vec{3.0, 4.0}
v2 = vec{1.0, 2.0}

v3 = v1 + v2          # operator (from numeric vibe)
v4 = v1 * 2.0         # operator (from numeric vibe)
mag = v3.magnitude()  # method
```

---

## see also

- [types.md](types.md) - bunch, methods, vibes
- [errors.md](errors.md) - error returns, ! operator
- [memory.md](memory.md) - auto rekt, cleanup
- [references.md](references.md#naming-conventions) - naming philosophy
