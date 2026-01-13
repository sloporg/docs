# references

sources and inspirations for slop concepts

---

## slop-specific terms

### bunch
**concept:** structs with methods and vibe-coded utilities  
**inspired by:** rust structs, go structs, zig structs, c structs  
**reference:** https://en.wikipedia.org/wiki/Struct_(C_programming_language)  
**innovation:** go-style receiver methods, auto `.str()`, `.json()`, `.bytes()`, `.hash()`, `.size()`, `.type()`, `.isinit()`  
**literal syntax:** `usr{id: 1, name: "chad"}` with curly braces, implicit `usr{1, "chad"}` by order

### mood
**concept:** sum types with tagged variants and explicit base type  
**inspired by:** rust enums, haskell adts, ocaml variants, swift enums  
**reference:** https://en.wikipedia.org/wiki/Algebraic_data_type  
**innovation:** explicit type declaration `mood ecode u64`, each mood restarts numbering at 0x00  
**usage:** error codes (icode/ecode/wcode), exit codes, status enums

### vibe
**concept:** interfaces/traits for polymorphism  
**inspired by:** rust traits, go interfaces, haskell typeclasses, swift protocols  
**reference:** https://en.wikipedia.org/wiki/Trait_(computer_programming)  
**innovation:** explicit `gives` keyword for vibe implementation, vibe-coded default methods  
**pattern:** use `gives vibe to type` to implement vibe methods

### leak
**concept:** public visibility modifier  
**inspired by:** rust `pub`, go capitalization convention  
**innovation:** explicit vs accidental exports, intentional public api  
**usage:** `leak fn getname() str` makes method public

### hood
**concept:** internal/package visibility (experimental)  
**inspired by:** rust `pub(crate)`, internal linkage  
**usage:** `hood fn helper() str` visible within package only  
**status:** experimental feature

### wild
**concept:** external visibility (experimental)  
**inspired by:** solidity `external` modifier  
**usage:** `wild fn api() str` external-only access  
**status:** experimental feature

### yeet
**origin:** internet slang "yeet" (to throw forcefully)  
**reference:** https://knowyourmeme.com/memes/yeet  
**concept:** fatal error termination with error code  
**inspired by:** rust `panic!()`, go `panic()`, python `sys.exit()`  
**signature:** `yeet ecode, str` - takes error code and message  
**behavior:** logs error via `loge` internally, then `os.exit(1)`

### bet
**origin:** slang "I bet" (strong assertion of confidence)  
**concept:** test assertion keyword  
**inspired by:** rust `assert!()`, go testing package, jest `expect()`  
**usage:** `bet value == expected` in test blocks  
**innovation:** casual keyword instead of formal "assert"

### rekt
**origin:** gaming slang "rekt" (destroyed/wrecked)  
**reference:** https://knowyourmeme.com/memes/rekt  
**concept:** explicit memory deallocation  
**inspired by:** c `free()`, rust `drop()`, zig deinit  
**innovation:** compiler auto-inserts before returns, enforces cleanup

### nah
**origin:** slang "nah" (negation, nothing)  
**concept:** void/nil/nothing/no-error value  
**usage:** return type void, "no error" position in tuples  
**example:** `fn dowork() (str, err)` returns `(data, nah)` for success

### chunk
**concept:** each `.sl` file is a module/compilation unit  
**inspired by:** lua chunks, file-based module systems  
**compiler enforcement:** strict section ordering within each chunk

### asm
**concept:** inline assembly blocks (target-dependent)  
**inspired by:** rust `asm!()`, c inline assembly, zig asm  
**usage:** direct assembly for syscalls, optimization  
**example:**
```slop
asm
  mov r0, #1
  svc #0
```

### slug, slap, slurp, slush
**concept:** async/concurrency og types  
**slug:** async function  
**slap:** spawn concurrent task  
**slurp:** await/join result  
**slush:** spawn + await in one line  
**inspired by:** rust async/await, go goroutines, javascript promises

---

## error handling

### info/err/warn bunches
**pattern:** separate types for severity levels  
**bunches:** `info`, `err`, `warn` (std predefined)  
**codes:** `icode`, `ecode`, `wcode` moods (each restarts at 0x00)  
**inspired by:** syslog severity levels, http status codes, posix error numbers  
**innovation:** type-checked log functions, hex numbering for geek aesthetic

### icode (info codes)
**mood:** `mood icode u64`  
**range:** 0x00-0xFF  
**common codes:** none, started, stopped, ready, complete, progress, connected  
**usage:** `logi icode.started, "server starting"`

### ecode (error codes)
**mood:** `mood ecode u64`  
**range:** 0x00-0xFF  
**categories:**
- 0x01-0x0F: not found/missing
- 0x10-0x1F: permission/auth
- 0x20-0x2F: input/validation
- 0x30-0x3F: state/conflict
- 0x40-0x4F: i/o errors
- 0x50-0x5F: network errors
- 0x60-0x6F: parse/decode errors
- 0x70-0x7F: resource errors
- 0xF0-0xFF: system/unknown

**usage:** `loge ecode.notfound, "user not found"`

### wcode (warning codes)
**mood:** `mood wcode u64`  
**range:** 0x00-0xFF  
**common codes:** none, deprecated, fallback, slowpath, retry, partial, redundant, unused  
**usage:** `logw wcode.deprecated, "use newapi instead"`

### ! operator
**concept:** error handling with optional fallback value  
**inspired by:** rust `?` operator, go `if err != nil` pattern  
**innovation:** can return fallback value or yeet  
**signature:** `result = riskyop() !` with optional block  
**behavior:**
- no block: auto-yeet if error
- with block: custom handling, can return fallback value
- `e` variable auto-available in block (error instance)

**example:**
```slop
data = readfile("config.txt") !
  logw wcode.fallback, "using defaults"
  "default config"
```

### exitcode mood
**concept:** standardized exit codes for `os.exit()`  
**inspired by:** posix exit codes, bsd sysexits  
**common codes:** success (0x00), generalerr (0x01), misuse (0x02)

---

## memory management

### ownership
**concept:** compile-time memory safety without garbage collection  
**inspired by:** rust ownership system, linear types  
**reference:** https://doc.rust-lang.org/book/ch04-01-what-is-ownership.html  
**innovation:** simpler than rust, enforced by compiler

### move semantics
**concept:** transfer ownership on assignment  
**applies to:** heap-allocated types (str, arrays, maps, bunches)  
**reference:** https://en.wikipedia.org/wiki/Move_semantics

### auto rekt
**concept:** compiler automatically inserts `rekt` before returns  
**inspired by:** go `defer`, rust `drop`  
**innovation:** fully automatic, no defer keyword needed  
**rationale:** prevents memory leaks, enforces cleanup

### bunch packing
**concept:** compiler-enforced optimal struct field ordering  
**inspired by:** zig struct packing suggestions  
**reference:** https://en.wikipedia.org/wiki/Data_structure_alignment  
**innovation:** slop requires it (compile error), zig only suggests

---

## type system

### operator overloading
**concept:** custom operator implementations via method names  
**pattern:** method name maps to operator symbol  
**mapping:**
- `add` → `+`
- `sub` → `-`
- `mul` → `*`
- `div` → `/`
- `mod` → `%`
- `eq` → `==`
- `ne` → `!=`
- `lt` → `<`
- `gt` → `>`
- `le` → `<=`
- `ge` → `>=`
- `get` → `[]` (indexing)

**inspired by:** rust trait operators, python dunder methods, c++ operator overloading  
**innovation:** works via bunch methods, no special trait needed

### implicit conversions
**numeric widening:** u8→u64, i32→i64, f32→f64  
**string interpolation:** auto `.str()` call in `"$var"` context  
**all safe conversions** that cannot fail

### naming conventions
**non-verb fields:** `name()` getter, `setname(val)` setter  
**verb-based:** `paint()` action, `ispainted()` check  
**bool checks:** prefix with `is` - `isinit()`, `isvalid()`, `isempty()`  
**rationale:** english-friendly, clear intent

### universal methods
**vibe-coded on all bunches:**
- `.str()` - string representation
- `.json()` - json serialization
- `.bytes()` - binary representation
- `.hash()` - hash value
- `.size()` - memory footprint
- `.type()` - type name as string
- `.isinit()` - initialization check

### initable vibe
**concept:** optional init hooks for bunches  
**methods:** `preinit()`, `init()`, `postinit()`  
**pattern:** define methods to auto-satisfy vibe  
**usage:** validation, logging, setup/teardown  
**visibility:** internal by default (not `leak`)

---

## syntax

### strict lowercase
**concept:** all identifiers must be lowercase  
**rationale:** eliminate casing ambiguity for ai agents  
**innovation:** no camelCase, PascalCase, snake_case variations

### no underscores
**concept:** underscores forbidden in identifiers (except `_` wildcard)  
**rationale:** forces short, concise names  
**pattern:** `usr` not `user_name`, `cnt` not `counter`

### chunk structure
**enforcement:** compiler requires specific section order  
**order:** header, imports, constants, type aliases, moods, bunches, vibes, functions, main  
**rationale:** consistent file structure, easier parsing for agents

### #yolo attribute
**concept:** override std definitions  
**usage:** per-function or per-bunch  
**danger:** breaks std assumptions, use carefully  
**example:**
```slop
#yolo
bunch err
  customfield str
```

---

## testing

### test keyword
**concept:** built-in test block syntax  
**file extension:** `.t.sl` for test files  
**inspired by:** rust `#[test]`, go `func TestXxx`, zig `test`

### bet assertions
**concept:** assertion keyword in tests  
**inspired by:** rust assert macros, go testing package  
**innovation:** casual slang term for assertions

---

## ffi

### c ffi
**concept:** zero-cost c interop  
**inspired by:** zig c interop, rust ffi, luajit ffi  
**reference:** https://en.wikipedia.org/wiki/Foreign_function_interface

### slopbind
**concept:** binding generator from c headers  
**inspired by:** rust bindgen, go cgo, python cffi  
**innovation:** prefers type stubs (.pyi, .d.ts), manual package management

---

## influences

### primary languages
**rust:** ownership system, error handling, traits, tooling excellence  
**zig:** simplicity, c interop, no hidden control flow, comptime  
**go:** simplicity, interfaces, methods with receivers, defer  
**python:** indentation-based syntax, readability, accessibility

### secondary influences
**c:** systems programming heritage, manual memory  
**haskell:** type system concepts, algebraic data types  
**solidity:** visibility modifiers (external), blockchain domain  

### documentation philosophy
**lowercase aesthetic:** intentional minimalism  
**casual tone:** accessible technical writing (20% casual, 80% neutral)  
**agent-first:** machine-readable everything (jsonl output)  
**hex numbering:** geek vibe (0x01, 0x02 instead of 1, 2)

---

*maintained at github.com/sloporg/docs*
