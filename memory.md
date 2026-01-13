# memory

memory management in slop

---

## no garbage collection

slop uses manual memory management with compiler enforcement

**rationale:**
- predictable performance (no gc pauses)
- smaller runtime (zero overhead)
- suitable for embedded/blockchain environments
- deterministic resource cleanup
- agent-friendly (no background threads)
- ownership tracking + compiler makes it safe

## memory model

### stack-allocated

**fixed-size types:**
- integers: `u8`, `u16`, `u32`, `u64`, `u128`, `u256`, `i8`, `i16`, `i32`, `i64`, `i128`, `i256`
- floats: `f32`, `f64`
- fixed buffers: `b8`, `b16`, `b32`, `b64`, `b128`, `b256`
- boolean: `bool`
- character: `ch`
- tuples: `(u64, bool)` (if all elements stack-allocated)

**behavior:**
- allocated on function entry
- deallocated on function exit
- no explicit cleanup needed
- copy semantics (value copied on assignment)

### heap-allocated

**dynamic types:**
- strings: `str`
- dynamic buffers: `bs`
- arrays: `[]t`
- maps: `{k: v}`
- bunch: custom structs
- tuples with heap-allocated elements

**behavior:**
- allocated on heap
- requires explicit `rekt` call
- move semantics (ownership transferred on assignment)
- compiler enforces cleanup

## ownership

### move semantics

**by default, values move:**
```slop
a = "yo"
b = a      # a moved to b, a invalid now
print b    # ok
print a    # error: use after move
```

**applies to:**
- strings
- arrays
- maps
- bunch instances
- any heap-allocated type

### copy semantics

**stack types copy:**
```slop
a = 42
b = a      # a copied to b, a still valid
print a    # ok
print b    # ok
```

## cleanup with rekt

**required for heap types:**
```slop
name = "alice"
rekt name  # free memory
```

**compiler enforces:**
- unused heap value → error
- use after rekt → error
- double rekt → error
- missing rekt before ret → error
- missing rekt at function end → error

**automatic in some cases:**
- error blocks: `rekt e` compiler-inserted
- function returns: compiler suggests where to add `rekt`

## automatic rekt insertion

the compiler automatically inserts `rekt` in specific scenarios to reduce boilerplate while maintaining safety

### rule 0x01: error blocks

**automatic cleanup in `!` blocks:**

```slop
data = readfile("conf.sl") !
  logi "file not found"
  ret ""
  # compiler inserts: rekt e
```

**applies to:**
- any `!` block that doesn't re-throw with `yeet`
- before each `ret` in the block
- at the end of the block if no explicit return

**equivalent manual code:**
```slop
data = readfile("conf.sl") !
  logi "file not found"
  rekt e      # manual cleanup
  ret ""
```

**when auto-rekt is skipped:**
```slop
# no auto-rekt if you yeet
data = readfile("conf.sl") !
  loge "critical error"
  yeet e      # re-throw error, no rekt inserted
```

### rule 0x02: function exit paths

**compiler inserts rekt before returns:**

```slop
fn process(input str) u64
  data = parse(input)
  
  if data.len() == 0
    ret 0
    # compiler inserts: rekt data, rekt input
  
  result = compute(data)
  rekt data
  
  result
  # compiler inserts: rekt input at function end
```

**tracking ownership:**
- compiler tracks which variables are still owned
- inserts `rekt` for all owned heap values before each exit
- order: reverse of declaration (rekt newest first)

**explicit return paths:**
```slop
fn getvalue(key str) (str, err)
  val = lookup(key) !
    ret ("", e)
    # compiler inserts: rekt key
  
  (val, nah)
  # compiler inserts: rekt key at function end
```

### rule 0x03: moved values

**no rekt if ownership transferred:**

```slop
fn createdata() str
  data = "yo"
  data           # moved to caller, no rekt needed
  # compiler does NOT insert rekt data
```

**returned values escape function:**
- compiler tracks that value is moved to caller
- caller responsible for cleanup
- no auto-rekt inserted

**partial moves:**
```slop
fn split(text str) (str, str)
  parts = text.split(" ")
  first = parts[0]      # move from array
  second = parts[1]     # move from array
  rekt parts            # array itself must be cleaned
  
  (first, second)
  # compiler does NOT insert rekt first/second (moved to caller)
  # compiler does NOT insert rekt text (moved to parts)
```

### rule 0x04: conditional branches

**rekt inserted on all paths:**

```slop
fn conditional(flag bool) str
  data = "test"
  
  if flag
    result = process(data)
    rekt data
    ret result
    # compiler inserts: nothing (data already rekt)
  else
    ret ""
    # compiler inserts: rekt data
  
  # unreachable, no auto-rekt needed
```

**compiler validates all paths:**
- each exit path must clean up owned values
- auto-inserts where needed
- errors if manual rekt creates double-free

### rule 0x05: loop exits

**rekt before break/ret in loops:**

```slop
fn findvalue(items []str, target str) str
  for item in items
    temp = process(item)
    
    if temp == target
      rekt items
      ret temp
      # compiler inserts: rekt item (loop iterator copy)
    
    rekt temp
  
  rekt items
  ""
```

**loop iterator cleanup:**
- `for` loop creates owned copy of each element
- auto-rekt at end of each iteration
- manual rekt needed if breaking early

### when auto-rekt is NOT inserted

**scenario 0x01: value already cleaned:**
```slop
data = "test"
rekt data
# no auto-rekt at function end (already cleaned)
```

**scenario 0x02: value moved:**
```slop
data = "test"
other = data   # data moved to other
# no auto-rekt for data (moved away)
# auto-rekt for other instead
```

**scenario 0x03: value returned:**
```slop
fn create() str
  data = "test"
  data         # moved to caller
  # no auto-rekt
```

**scenario 0x04: error re-thrown:**
```slop
result = compute() !
  yeet e       # error moved via yeet
  # no auto-rekt e
```

### compiler error messages

**missing rekt (manual required):**
```
error[e302]: value not cleaned up
  --\u003e app.sl:15:3
   |
15 |   data = "yo"
   |   ^^^^ heap value never cleaned
   |
   = help: add `rekt data` before function ends
   = note: cannot auto-insert (ambiguous cleanup point)
```

**use after rekt:**
```
error[e303]: use after rekt
  --\u003e app.sl:18:3
   |
16 |   rekt data
17 |   # compiler auto-inserted rekt data here
18 |   print data
   |         ^^^^ value used after cleanup
```

**double rekt:**
```
error[e304]: double rekt
  --\u003e app.sl:20:3
   |
19 |   rekt data
20 |   # compiler auto-inserted rekt data here (conflict)
   |
   = help: remove manual `rekt data` (compiler handles it)
```

### debugging auto-rekt

**view compiler insertions:**
```bash
slopc app.sl --dumpir
```

**output shows inserted rekt:**
```
fn process(input str) u64:
  %1 = call parse(input)              # data = parse(input)
  %2 = call len(%1)                   # data.len()
  %3 = eq %2, 0                       # data.len() == 0
  br %3, label %if, label %else
  
if:
  call rekt(%1)                       # auto-inserted: rekt data
  call rekt(input)                    # auto-inserted: rekt input
  ret 0
  
else:
  %4 = call compute(%1)               # result = compute(data)
  call rekt(%1)                       # manual: rekt data
  call rekt(input)                    # auto-inserted: rekt input
  ret %4
```

### best practices

**let compiler help:**
- use auto-rekt for error blocks (always)
- use auto-rekt for simple returns (let compiler insert)
- manual rekt for complex ownership (explicit control)

**when to be explicit:**
- performance critical paths (control exact cleanup point)
- complex ownership transfers (document intent)
- long-lived values (cleanup early to free memory)

**example - early cleanup:**
```slop
fn process(data str)
  parsed = parse(data)
  rekt data              # explicit: free early (done with input)
  
  result = compute(parsed)
  rekt parsed            # explicit: free early (done with parsed)
  
  finalize(result)
  rekt result            # could rely on auto-rekt here
```

## bunch packing

**compiler optimizes bunch layout:**

```slop
bunch usr
  id u64       # 8 bytes
  active bool  # 1 byte
  name str     # 24 bytes (ptr + len + cap)
```

**packed layout:**
- slot 0: `id` (u64) + `active` (bool) = 9 bytes → padded to 8-byte boundary = 16 bytes
- slot 1: `name` (str) = 24 bytes

**total:** 40 bytes

**compiler errors if non-optimal:**
```
error[e301]: bunch not optimally packed
  --> app.sl:10:1
   |
10 | bunch usr
   | ^^^^^^^^^ suggest reordering fields
   |
   = help: reorder to minimize padding
   = suggestion:
     bunch usr
       id u64
       active bool
       name str
```

**you must manually reorder** - compiler won't auto-fix

## parameter limits

**native (amd64/arm64):**
- max 16 params
- max 8 return values

**evm:**
- max 12 params
- max 6 return values

**svm:**
- max 12 params
- max 6 return values

**rationale:**
- matches calling conventions (registers + stack)
- prevents stack-too-deep errors (evm)
- bunch fields count as single param

**if exceeded:**
```
error[e101]: too many parameters
  --> app.sl:5:1
   |
5 | fn helper(a u64, b u64, c u64, ..., z u64)
   |           ^^^^^^^^^^^^^^^^^^^^^^^^^ 17 parameters (max 16)
   |
   = help: use bunch to group related parameters
```

## bunch memory layout

**by-name access:**
```slop
usr = usr{id: 1, name: "alice"}
print usr.id      # access by name
```

**by-slot access:**
```slop
print usr[0]      # access slot 0 (id + active packed)
print usr[1]      # access slot 1 (name)
```

**slot index:**
- groups fields by packing
- fields in same slot share memory alignment
- useful for low-level optimizations

## sloppycost

**memory cost analysis:**

compiler calculates memory footprint at compile time:

```slop
fn process(items []u64)
  # compiler analyzes:
  # - stack usage
  # - heap allocations
  # - bunch sizes
  # - total memory footprint
```

**reported in cu (compute units):**
- abstract unit (not bytes or gas)
- useful for optimization
- available with `--dumpir` flag

## zero-copy operations

**slicing creates views (no copy):**
```slop
data = "yo world"
part = data[0..5]    # "hello" (view, not copy)
rekt part            # free view
rekt data            # free original
```

**both must be cleaned up** - compiler tracks both

## comparison with other languages

**rust:**
- slop: similar ownership model
- slop: simpler (no lifetimes in core)
- slop: explicit rekt vs drop trait

**c:**
- slop: compiler enforces cleanup (c doesn't)
- slop: move semantics by default (c copies)
- slop: no undefined behavior (c has)

**go/java/python:**
- slop: no gc (they have gc)
- slop: manual control (they're automatic)
- slop: predictable (they have gc pauses)

**zig:**
- slop: similar manual management
- slop: move semantics (zig has allocators)
- slop: bunch packing enforced (zig suggests)

## evm specifics

**storage vs memory:**
- heap types use evm memory (not storage)
- storage requires explicit `evm.storage.store()`
- storage is persistent, memory is temporary

**gas cost:**
- heap allocation costs gas
- compiler optimizes to minimize allocations
- `sloppycost` reports gas estimates

## future: ownership annotations

**possible syntax (experimental):**
```slop
fn process(data ref str)  # borrow (no ownership)
  print data
  # no rekt needed

fn consume(data str)      # take ownership
  print data
  # rekt data required

fn update(data mref str)  # mutable reference
  data = "new"
  # caller sees change
```

**status:** under research for epoch 6+