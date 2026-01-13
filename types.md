# types

slop type system [→](references.md#type-system)

---

## og types

### integers

```slop
u8    u16    u32    u64    u128    u256    # unsigned
i8    i16    i32    i64    i128    i256    # signed
```

**examples:**
```slop
x u64 = 0xff                    # hex: 255
y u256 = 0xffffffffffffffff     # max u64 value in u256
z i64 = -42
```

### floats

```slop
f32    f64
```

**examples:**
```slop
pi f64 = 3.14159
avogadro f64 = 6.022e23         # scientific notation
tiny f32 = 1.5e-10
```

### buffers

```slop
b8     b16    b32    b64    b128    b256    # fixed size
bs     # dynamic buffer
```

### text

```slop
str    # string
ch     # character
```

### logic

```slop
bool   # boolean
```

### special

```slop
nah    # void/nothing/empty
```

**usage:** [→](references.md#nah)
- return type for void functions
- "no error" position in error tuples
- cannot be returned for typed values

---

## collections

### arrays

```slop
[]u64           # array of u64
items = [1, 2, 3]  # array literal
items[0]         # index access
items[0..2]       # slice
```

**methods:**
- `.size()` - length
- `.has(val)` - contains check
- `.json()` - json serialization

### maps [→](references.md#json)

```slop
{str:u64}         # map string to u64
data = {"a": 1, "b": 2}  # map literal (json syntax)
data["a"]         # key access
data.has("a")      # membership test
```

**methods:**
- `.size()` - number of keys
- `.has(key)` - key existence
- `.json()` - json serialization

### tuples

```slop
(u64, str)        # tuple type
pair = (42, "yo") # tuple literal
first, second = pair   # destructuring
```

**multiple returns:**
```slop
fn split(s str) (str, str)
  ("first", "second")

a, b = split("data")
```

---

## bunch (structs) [→](references.md#bunch)

### declaration

```slop
bunch usr
  id u64
  name str
  active bool
```

### construction [→](references.md#bunch-literals)

```slop
# explicit with colons, curly braces
u = usr{id: 1, name: "alice", active: true}

# implicit by order
u = usr{1, "alice", true}

# skip fields with ,,
bunch pnt
  x u64
  y u64
  z u64

p = pnt{1, , 3}        # x=1, y=0 (default), z=3
```

### field access

```slop
print u.id       # by name
print u.name
print u[0]      # by slot (packed layout)
```

### field visibility

```slop
bunch usr
  id u64              # private (default)
  leak name str       # public
```

**see:** [visibility](#visibility) below

---

## methods [→](references.md#methods-on-og-types)

### defining methods (inside bunch with deez)

```slop
bunch pnt
  x u64
  y u64
  
  # private method
  fn validate() bool
    deez.x > 0 && deez.y > 0
  
  # public method
  leak fn distance() f64
    sqrt(deez.x * deez.x + deez.y * deez.y)
  
  # operator overloading via numeric vibe
  leak fn add(other pnt) pnt
    pnt{x: deez.x + other.x, y: deez.y + other.y}

# usage
p1 = pnt{3, 4}
p2 = pnt{1, 2}

if p1.validate()
  dist = p1.distance()
  p3 = p1.add(p2)     # explicit
  p3 = p1 + p2        # operator (compiler maps add → +)
```

**syntax:** methods defined inside bunch block, use `deez` for instance reference

### methods on og types (via gives)

og types can be extended with custom methods using `gives`:

```slop
# define custom vibe
vibe nuts
  fn nuts() u64
  fn double() u64

# extend og type with vibe
leak gives nuts to u64
  fn nuts() u64
    deez * 0x45
  
  fn double() u64
    deez * 2

# usage
x u64 = 42
result = x.nuts()     # 2772 (42 * 0x45)
doubled = x.double()  # 84
```

**cannot extend og types directly** - must use `gives` with a vibe

---

## built-in vibes

all types automatically implement built-in vibes. compiler generates implementations lazily (only compiled if used).

### showable vibe

```slop
vibe showable
  fn str() str      # string representation
  fn json() str     # json serialization
```

**vibe-coded for bunches:**
```slop
bunch usr
  id u64
  name str

# compiler vibe-codes:
u = usr{id: 1, name: "alice"}
print u.str()   # "usr{id: 1, name: alice}"
u.json()        # "{\"id\":1,\"name\":\"alice\"}"
```

**vibe-coded for og types:**
```slop
x u64 = 42
x.str()     # "42"
x.json()    # "42"
```

### hashable vibe

```slop
vibe hashable
  fn hash() u64     # hash value for collections
```

**vibe-coded for all types:**
```slop
u = usr{id: 1, name: "alice"}
h = u.hash()  # combined hash of fields

x u64 = 42
h = x.hash()  # hash of value
```

### sizable vibe

```slop
vibe sizable
  fn size() u64     # memory footprint in bytes
```

**vibe-coded for all types:**
```slop
u = usr{id: 1, name: "alice"}
s = u.size()  # memory size of bunch

x u64 = 42
s = x.size()  # 8 (bytes)
```

### typeable vibe

```slop
vibe typeable
  fn type() str     # type name as string
```

**vibe-coded for all types:**
```slop
u = usr{id: 1, name: "alice"}
t = u.type()  # "usr"

x u64 = 42
t = x.type()  # "u64"
```

### checkable vibe

```slop
vibe checkable
  fn isinit() bool  # initialization check
```

**vibe-coded for all types:**
```slop
u usr
if !u.isinit()
  print "uninitialized"

u = usr{id: 1, name: "alice"}
if u.isinit()
  print "initialized"
```

### numeric vibe (numeric types only)

```slop
vibe numeric
  fn add(other T) T      # maps to +
  fn sub(other T) T      # maps to -
  fn mul(other T) T      # maps to *
  fn div(other T) T      # maps to /
  fn mod(other T) T      # maps to %
```

**vibe-coded for u64, i64, u32, i32, f64, f32, etc.**

### comparable vibe

```slop
vibe comparable
  fn eq(other T) bool    # maps to ==
  fn ne(other T) bool    # maps to !=
  fn lt(other T) bool    # maps to <
  fn gt(other T) bool    # maps to >
  fn le(other T) bool    # maps to <=
  fn ge(other T) bool    # maps to >=
```

**vibe-coded for all types** (bunches compare field-by-field)

### bitwise vibe (integer types only)

```slop
vibe bitwise
  fn and(other T) T      # maps to &
  fn or(other T) T       # maps to |
  fn xor(other T) T      # maps to ^
  fn shl(n u64) T        # maps to <<
  fn shr(n u64) T        # maps to >>
```

**vibe-coded for integer types (u64, i64, u32, etc.)**

### overriding vibe-coded methods

you can override any vibe-coded method:

```slop
bunch usr
  id u64
  name str
  password str
  
  # override vibe-coded str() to hide password
  leak fn str() str
    "usr{id: $deez.id, name: $deez.name}"
  
  # override vibe-coded json() to hide password
  leak fn json() str
    "{\"id\":$deez.id,\"name\":\"$deez.name\"}"

u = usr{id: 1, name: "alice", password: "secret"}
print u.str()   # "usr{id: 1, name: alice}" (password hidden)
```

### lazy compilation

built-in vibe methods are only compiled if actually used:

```slop
bunch usr
  id u64
  name str

u = usr{id: 1, name: "alice"}
print u.str()   # .str() is compiled
# .json(), .hash(), etc. NOT compiled (unused)
```

**benefit:** smaller binary size, faster compilation

---

## visibility [→](references.md#visibility)

### levels

```slop
fn validate() bool        # private (default)
leak fn getname() str     # public
```

**private (default):**
- visible only within same bunch/module
- cannot be called from outside

**leak (public):**
- visible everywhere
- part of public api

- visible within package
- not visible to external users

- external-only access
- cannot be called internally (like solidity external)

---

## operator overloading [→](references.md#operator-overloading)

operators map to built-in vibe methods. implement the vibe to enable operators.

### arithmetic operators (numeric vibe)

```slop
bunch pnt
  x u64
  y u64

# implement numeric vibe to enable +, -, *, /, %
gives numeric to bunch pnt
  fn add(other pnt) pnt
    pnt{x: deez.x + other.x, y: deez.y + other.y}
  
  fn sub(other pnt) pnt
    pnt{x: deez.x - other.x, y: deez.y - other.y}
  
  fn mul(scalar u64) pnt
    pnt{x: deez.x * scalar, y: deez.y * scalar}

# compiler maps:
p1 = pnt{3, 4}
p2 = pnt{1, 2}
p3 = p1 + p2    # → p1.add(p2)
p4 = p1 - p2    # → p1.sub(p2)
p5 = p1 * 5     # → p1.mul(5)
```

### comparison operators (comparable vibe)

```slop
bunch pnt
  x u64
  y u64

# implement comparable vibe to enable ==, !=, <, >, <=, >=
gives comparable to bunch pnt
  fn eq(other pnt) bool
    deez.x == other.x && deez.y == other.y
  
  fn lt(other pnt) bool
    deez.x < other.x && deez.y < other.y

# compiler maps:
p1 = pnt{3, 4}
p2 = pnt{1, 2}
if p1 == p2     # → p1.eq(p2)
  print "equal"
if p1 < p2      # → p1.lt(p2)
  print "less"
```

### indexing operator

```slop
bunch matrix
  data [][]u64
  
  leak fn get(i u64) []u64
    deez.data[i]
  
  leak fn set(i u64, row []u64)
    deez.data[i] = row

# compiler maps:
m = matrix{data: [[1, 2], [3, 4]]}
row = m[0]      # → m.get(0)
```

**built-in vibe method mappings:**
- **numeric:** `add` → `+`, `sub` → `-`, `mul` → `*`, `div` → `/`, `mod` → `%`
- **comparable:** `eq` → `==`, `ne` → `!=`, `lt` → `<`, `gt` → `>`, `le` → `<=`, `ge` → `>=`
- **bitwise:** `and` → `&`, `or` → `|`, `xor` → `^`, `shl` → `<<`, `shr` → `>>`
- **indexing:** `get` → `[]`

---

## type aliases

```slop
type userid = u64
type callback = fn(u64) bool
type errhandler = fn(err) nah
```

---

## mood (sum types) [→](references.md#mood)

### declaration with explicit type

```slop
mood status u8
  pending = 0x00
  running = 0x01
  complete = 0x02

mood ecode u64
  none = 0x00
  notfound = 0x01
  unauthorized = 0x02
```

**type required:** `mood name basetype`

### usage

```slop
s = status.pending

if s == status.running
  print "processing"

# error codes
e = ecode.notfound
loge e, "user not found"
```

**see:** [errors.md](errors.md#error-codes) for error code moods

---

## vibe (interfaces/traits) [→](references.md#vibe)

vibes define method contracts. types implement vibes through method definitions or explicit `gives`.

### declaration

```slop
vibe stringer
  fn str() str

vibe logger
  fn log(msg str)
  fn logerr(msg str)

vibe comparable
  fn eq(other T) bool
  fn lt(other T) bool
```

**methods have no body** - abstract signatures only

### implementation via gives

```slop
vibe logger
  fn log(msg str)
  fn logerr(msg str)

bunch usr
  id u64
  leak name str

# implement logger vibe using gives
leak gives logger to bunch usr
  fn log(msg str)
    print "$deez.name: $msg"
  
  fn logerr(msg str)
    loge ecode.user, "$deez.name: $msg"

# use as logger vibe
fn process(l logger)
  l.log("processing")
  l.logerr("error occurred")

u = usr{id: 1, name: "alice"}
process(u)  # usr satisfies logger vibe
```

### implementation with methods (auto-satisfaction)

```slop
vibe stringer
  fn str() str

bunch pnt
  x u64
  y u64
  
  # implement stringer vibe method directly in bunch
  leak fn str() str
    "($deez.x, $deez.y)"

# pnt automatically satisfies stringer vibe (all methods present)
fn display(s stringer)
  print s.str()

p = pnt{3, 4}
display(p)  # works - pnt has str() method
```

**auto-satisfaction:** if bunch has all methods of a vibe, it automatically satisfies that vibe

### extending og types with gives

```slop
vibe nuts
  fn nuts() u64

# extend u64 og type
leak gives nuts to u64
  fn nuts() u64
    deez * 0x45

# now u64 satisfies nuts vibe
fn process(n nuts)
  print n.nuts()

x u64 = 42
process(x)  # works - u64 implements nuts
```

### partial implementation

bunches can have methods without satisfying full vibe:

```slop
vibe logger
  fn log(msg str)
  fn logerr(msg str)

bunch usr
  name str
  
  # only implements log (not logerr)
  fn log(msg str)
    print deez.name

# usr has log() method (can call directly)
u = usr{name: "alice"}
u.log("yo")  # ✅ works

# but usr does NOT satisfy logger vibe
fn process(l logger)
  l.log("test")
  l.logerr("error")

process(u)  # ❌ ERROR: usr missing logerr method
```

**compile-time check:** vibe satisfaction verified when passing to functions expecting vibe type

### gives visibility

```slop
vibe logger
  fn log(msg str)

# private gives (module-scoped)
gives logger to bunch usr
  fn log(msg str)
    print deez.name

# public gives (exported)
leak gives logger to bunch config
  fn log(msg str)
    print "config log"
```

**see:** [syntax.md](syntax.md#leak-consistency-rules) for leak rules

---

## advanced vibe patterns

comprehensive patterns for complex vibe usage scenarios

### pattern 0x01: multiple vibe implementation

**single type satisfies multiple vibes:**

```slop
vibe stringer
  fn str() str

vibe jsonable
  fn json() str

vibe hashable
  fn hash() u64

bunch usr
  id u64
  name str
  
  # implement all three vibes
  leak fn str() str
    "usr{$deez.id, $deez.name}"
  
  leak fn json() str
    "{\"id\": $deez.id, \"name\": \"$deez.name\"}"
  
  leak fn hash() u64
    deez.id ^ hash_str(deez.name)

# usr satisfies all three vibes
fn display(s stringer)
  print s.str()

fn serialize(j jsonable) str
  j.json()

fn index(h hashable) u64
  h.hash()

u = usr{id: 1, name: "alice"}
display(u)      # works
serialize(u)    # works
index(u)        # works
```

### pattern 0x02: vibe composition

**vibes requiring other vibes:**

```slop
vibe showable
  fn str() str

vibe serializable
  fn json() str

# composite vibe requiring both
vibe loggable
  fn str() str      # from showable
  fn json() str     # from serializable
  fn logentry() str # additional method

bunch config
  port u64
  host str
  
  leak fn str() str
    "$deez.host:$deez.port"
  
  leak fn json() str
    "{\"port\": $deez.port, \"host\": \"$deez.host\"}"
  
  leak fn logentry() str
    "config[$deez.str()]: $deez.json()"

# config satisfies loggable (has all three methods)
fn log_item(item loggable)
  logi item.logentry()

c = config{port: 6969, host: "localhost"}
log_item(c)
```

### pattern 0x03: extending og types with multiple vibes

**add multiple capabilities to og types:**

```slop
vibe validator
  fn validate() bool

vibe sanitizer
  fn sanitize() str

# extend str with both vibes
leak gives validator to str
  fn validate() bool
    deez.len() \u003e 0 \u0026\u0026 !deez.contains("\\0")

leak gives sanitizer to str
  fn sanitize() str
    deez.replace("\u003c", "\u0026lt;").replace("\u003e", "\u0026gt;")

# use both vibes
fn process_input(input str) str
  if !input.validate()
    yeet err{code: ecode.invalid, msg: "invalid input"}
  
  input.sanitize()

text = "\u003cscript\u003ealert('xss')\u003c/script\u003e"
safe = process_input(text)  # validates then sanitizes
```

### pattern 0x04: vibe with generic-like behavior

**vibe methods with type parameters (experimental):**

```slop
vibe container
  fn get(index u64) any
  fn set(index u64, value any)
  fn len() u64

bunch intarray
  data []u64
  
  leak fn get(index u64) any
    deez.data[index]
  
  leak fn set(index u64, value any)
    # runtime type check
    if typeof(value) != "u64"
      yeet err{code: ecode.type, msg: "expected u64"}
    deez.data[index] = value as u64
  
  leak fn len() u64
    deez.data.len()

# use as container
fn fill(c container, count u64)
  for i in 0..count
    c.set(i, i * 2)
```

### pattern 0x05: operator overloading via vibes

**complete numeric type with all operators:**

```slop
vibe numeric
  fn add(other T) T
  fn sub(other T) T
  fn mul(other T) T
  fn div(other T) T
  fn mod(other T) T

vibe comparable
  fn eq(other T) bool
  fn lt(other T) bool

bunch fixed
  value u64  # fixed-point with 6 decimal places
  
  leak fn add(other fixed) fixed
    fixed{value: deez.value + other.value}
  
  leak fn sub(other fixed) fixed
    fixed{value: deez.value - other.value}
  
  leak fn mul(other fixed) fixed
    # fixed-point multiplication
    result = (deez.value * other.value) / 1000000
    fixed{value: result}
  
  leak fn div(other fixed) fixed
    # fixed-point division
    result = (deez.value * 1000000) / other.value
    fixed{value: result}
  
  leak fn mod(other fixed) fixed
    fixed{value: deez.value % other.value}
  
  leak fn eq(other fixed) bool
    deez.value == other.value
  
  leak fn lt(other fixed) bool
    deez.value \u003c other.value

# use with operators
a = fixed{value: 1500000}  # 1.5
b = fixed{value: 2000000}  # 2.0

c = a + b    # calls a.add(b) → 3.5
d = b - a    # calls b.sub(a) → 0.5
e = a * b    # calls a.mul(b) → 3.0
f = b / a    # calls b.div(a) → 1.333...

if a \u003c b   # calls a.lt(b)
  print "a is less"
```

### pattern 0x06: indexing operator

**custom get/set via vibes:**

```slop
vibe indexed
  fn get(key str) any
  fn set(key str, value any)

bunch registry
  data {}str: str
  
  leak fn get(key str) any
    if deez.data.has(key)
      deez.data[key]
    else
      nah
  
  leak fn set(key str, value any)
    if typeof(value) != "str"
      yeet err{code: ecode.type, msg: "expected str"}
    deez.data[key] = value as str

# use with [] operator (experimental)
r = registry{data: {}}
r["config"] = "value"         # calls r.set("config", "value")
val = r["config"]             # calls r.get("config")
```

### pattern 0x07: method override in gives

**override vibe-coded methods:**

```slop
# all types vibe-code .str() method
bunch usr
  id u64
  name str

# default .str() would be: "usr{id: 1, name: \"alice\"}"

# override with custom implementation
leak gives showable to bunch usr
  fn str() str
    "user #$deez.id: $deez.name"

u = usr{id: 1, name: "alice"}
print u.str()  # "user #1: alice" (custom, not default)
```

### pattern 0x08: vibe-based polymorphism

**process different types through same vibe:**

```slop
vibe processor
  fn process() str

bunch textfile
  path str
  
  leak fn process() str
    content = fs.read(deez.path)
    content.uppercase()

bunch jsonfile
  path str
  
  leak fn process() str
    data = fs.read(deez.path)
    parsed = json.parse(data)
    json.stringify(parsed, indent: 2)

bunch csvfile
  path str
  
  leak fn process() str
    rows = fs.readlines(deez.path)
    rows.join("|")

# generic processing function
fn batch_process(items []processor) []str
  results = []
  for item in items
    results.append(item.process())
  results

# use with mixed types
files []processor = [
  textfile{path: "readme.txt"},
  jsonfile{path: "config.json"},
  csvfile{path: "data.csv"}
]

outputs = batch_process(files)
```

### pattern 0x09: vibe constraints in functions

**require multiple vibes:**

```slop
vibe stringer
  fn str() str

vibe hashable
  fn hash() u64

# function requiring both vibes
fn cache_key of t (item t) (str, u64)
  # t must satisfy both stringer and hashable
  (item.str(), item.hash())

bunch usr
  id u64
  name str
  
  leak fn str() str
    deez.name
  
  leak fn hash() u64
    deez.id

u = usr{id: 1, name: "alice"}
key, hash = cache_key(u)  # works - usr has both methods
```

### pattern 0x10: partial vibe implementation with compiler errors

**demonstrate compiler vibe checking:**

```slop
vibe serializable
  fn json() str
  fn xml() str
  fn yaml() str

bunch config
  port u64
  
  # only implement json
  leak fn json() str
    "{\"port\": $deez.port}"

# try to use as serializable
fn save(s serializable)
  fs.write("data.json", s.json())
  fs.write("data.xml", s.xml())
  fs.write("data.yaml", s.yaml())

c = config{port: 6969}
save(c)  # ❌ compiler error
```

**error message:**
```
error[e701]: type does not satisfy vibe
  --\u003e app.sl:185:6
   |
185 |   save(c)
   |        ^ config does not implement serializable
   |
   = note: config is missing methods:
     - xml() str
     - yaml() str
   = help: add missing methods to bunch config or use gives
```

**fix with gives:**
```slop
leak gives serializable to bunch config
  fn json() str
    "{\"port\": $deez.port}"
  
  fn xml() str
    "\u003cconfig\u003e\u003cport\u003e$deez.port\u003c/port\u003e\u003c/config\u003e"
  
  fn yaml() str
    "port: $deez.port"

c = config{port: 6969}
save(c)  # ✅ works now
```

### pattern 0x11: auto-satisfaction vs explicit gives

**when to use each approach:**

**auto-satisfaction (methods in bunch):**
```slop
vibe stringer
  fn str() str

bunch usr
  name str
  
  # method in bunch - auto-satisfies stringer
  leak fn str() str
    deez.name

# good for:
# - primary capabilities of the type
# - methods fundamental to type's purpose
# - when bunch and vibe are defined together
```

**explicit gives (outside bunch):**
```slop
vibe logger
  fn log(msg str)

bunch usr
  name str

# gives outside bunch - explicit satisfaction
leak gives logger to bunch usr
  fn log(msg str)
    logi "user: $deez.name"

# good for:
# - extending types after definition
# - optional capabilities
# - separating concerns
# - when vibe and bunch are in different modules
```

### pattern 0x12: vibe-based builder pattern

**fluent interfaces with vibes:**

```slop
vibe builder of t
  fn build() t

bunch querybuilder
  table str
  where str
  limit u64
  
  leak fn settable(t str) querybuilder
    deez.table = t
    deez
  
  leak fn setwhere(w str) querybuilder
    deez.where = w
    deez
  
  leak fn setlimit(l u64) querybuilder
    deez.limit = l
    deez
  
  leak fn build() str
    "SELECT * FROM $deez.table WHERE $deez.where LIMIT $deez.limit"

# fluent usage
query = querybuilder{table: "", where: "", limit: 0}
  .settable("users")
  .setwhere("active = true")
  .setlimit(10)
  .build()
```

### pattern 0x13: vibe inheritance simulation

**vibes with overlapping methods:**

```slop
vibe reader
  fn read() str

vibe writer
  fn write(data str)

vibe readwriter
  fn read() str    # from reader
  fn write(data str)  # from writer

bunch filehandle
  path str
  
  leak fn read() str
    fs.read(deez.path)
  
  leak fn write(data str)
    fs.write(deez.path, data)

# filehandle satisfies all three vibes
fn read_only(r reader)
  r.read()

fn write_only(w writer)
  w.write("data")

fn read_write(rw readwriter)
  content = rw.read()
  rw.write(content + "\\nappended")

f = filehandle{path: "test.txt"}
read_only(f)    # works
write_only(f)   # works
read_write(f)   # works
```

### pattern 0x14: compile-time vibe validation

**compiler enforces vibe contracts:**

```slop
vibe validator
  fn validate() (bool, str)

# function expects validator
fn check_input(v validator) bool
  valid, msg = v.validate()
  if !valid
    logw msg
  valid

bunch email
  addr str
  
  leak fn validate() (bool, str)
    if !deez.addr.contains("@")
      (false, "invalid email: missing @")
    else
      (true, "")

bunch phone
  number str
  
  # missing validate method intentionally

e = email{addr: "test@example.test"}
check_input(e)  # ✅ works

p = phone{number: "555-1234"}
check_input(p)  # ❌ compiler error: phone doesn't implement validator
```

### pattern 0x15: lazy vibe satisfaction

**compiler only checks vibes when needed:**

```slop
vibe stringer
  fn str() str

bunch usr
  name str
  
  # has str() but never declare as stringer

# can call method directly
u = usr{name: "alice"}
u.str()  # ✅ works (method exists)

# vibe only checked when passing to function
fn display(s stringer)
  print s.str()

display(u)  # compiler checks: does usr satisfy stringer? yes → ✅
```

**compile-time checking happens:**
- when passing value to function expecting vibe type
- when assigning to variable with vibe type
- when storing in collection of vibe type

**not checked:**
- when calling method directly on concrete type
- when concrete type used throughout

---

```slop
vibe initable
  fn preinit()
  fn init()
  fn postinit()

bunch usr
  id u64
  name str
  
  # opt-in to initable
  fn preinit()
    logi icode.started, "creating user"
  
  fn init()
    # custom init logic
  
  fn postinit()
    logi icode.complete, "user created: $deez.id"

# execution flow:
u = usr{id: 1, name: "chad"}
# 0x01. preinit() called
# 0x02. fields initialized
# 0x03. postinit() called
```

---



## see also

- [syntax.md](syntax.md) - bunch literal syntax
- [functions.md](functions.md) - method syntax, naming
- [memory.md](memory.md) - bunch packing, initialization
- [references.md](references.md#type-system) - type system design
