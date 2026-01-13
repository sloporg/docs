# comparison

slop vs other languages

---

## terminology mapping

### slop → other languages

**bunch** - struct with auto .str() method
- rust: struct
- go: struct
- typescript: interface, type
- python: class, dataclass
- c++: struct, class

**leak** - public visibility modifier
- rust: pub
- go: uppercase first letter
- typescript: export
- python: no equivalent (public by convention)
- c++: public

**mood** - sum types with variants
- rust: enum
- swift: enum
- typescript: union types
- go: no equivalent (use constants + interfaces)
- c++: enum, union
- python: dataclass with literal types

**vibe** - trait/interface definitions
- rust: trait
- go: interface
- typescript: interface, type
- haskell: typeclass, type families
- c++: abstract class, pure virtual functions
- java: interface, abstract class

**gives** - implementations
- rust: impl
- go: interface methods
- typescript: implements interface
- haskell: instance methods
- java: implements

**chunk** - a single .sl file
- rust: module
- go: package/file
- typescript: module
- haskell: module
- c++: compilation unit (.cpp, .h)

**nah** - void/nothing/empty type
- rust: unit
- go: no return
- typescript: void, undefined
- haskell: ()
- c++: void
- python: none (returns none implicitly)

**yeet** - crash/abort with message
- rust: panic!
- go: panic
- typescript: throw new Error()
- haskell: error
- c++: abort()

**! operator** - error handling
- rust: ? operator (similar usage)
- go: if err != nil { return err }
- typescript: try/catch, throw
- python: try/except raise
- haskell: error
- c++: try/catch, throw

**e** - reserved error variable
- rust: no equivalent
- go: no equivalent
- typescript: no equivalent (catch variable)
- haskell: no equivalent
- c++: no equivalent

**slug** - async task handle
- rust: future<t>
- go: chan (sync) or goroutine (async)
- typescript: promise
- javascript/typescript: promise
- python: asyncio.future
- haskellel: async mtl/async
- c++: std::future, coroutines

**slap** - spawn async task
- rust: tokio::spawn
- go: go func()
- typescript: async/await
- javascript: async/await
- python: asyncio.create_task()
- haskellel: async
- c++: std::async, coroutines

**slurp** - await result
- rust: .await
- go: <-chan (no direct await)
- typescript: await
- javascript: await
- python: await
- haskellel: await m

**slush** - spawn + await
- rust: spawn().await
- go: no direct equivalent
- typescript: no direct equivalent
- javascript: no direct equivalent
- python: asyncio.create_task().result()

**cycle** - repeat at intervals
- rust: tokio::interval loop
- go: time.ticker
- typescript: setinterval
- python: loop with time.sleep()
- haskellel: loop with thread.delay()

**sleep** - delay execution
- rust: tokio::sleep
- go: time.sleep
- typescript: no direct equivalent (setTimeout)
- python: time.sleep()
- haskellel: thread.sleep()

**rekt** - free memory
- rust: drop (automatically)
- go: no direct equivalent (gc)
- typescript: no direct equivalent (gc)
- python: del or context manager

**bunch packing** - compiler optimizes layout
- rust: repr(derive) (no auto layout)
- go: no direct equivalent (gc)
- typescript: no direct equivalent
- haskell: no direct equivalent
- c++: #pragma pack (limited)
- python: @dataclass (no control)

---

## concept comparison

### error handling

| feature | slop | rust | go | typescript | python | c++ | haskellel |
|---------|------|-----|---|----------|--------|------|-----------|
| try/catch | ? operator + e var | ? operator | panic! | if err != nil | try/catch | try/except raise | error | error: return error | return error() | try-catch |
| result type | explicit | can return `nah` | result<t> | error | result<t> | error | error | result<t> | error | result<t> | error | result<t> | panic! | no equivalent | throw | raise | error | throw | error: throw | return err |
| cleanup | rekt required | automatic drop | automatic gc | automatic gc | automatic gc | automatic gc | automatic gc | automatic gc | deinit | defer | defer | gc collects | defer | raii garbage collector |
| crash | yeet | panic! | panic! | panic! | throw new error | throw exception | os.exit(1) | sys.exit() | process.exit(1) | die | exit() | os.exit(1) | os.abort() | die() |
| reserved var | e (error scope) | no equivalent | no equivalent | no equivalent | no equivalent | catch variable | no equivalent | catch variable | error variable | error variable |

### async/await

| feature | slop | rust | go | typescript | javascript | python | haskellel |
|---------|------|-----|----------|--------------|----------------|-----------|
| spawn | slap | tokio::spawn | go func() | async | async/await | async/await | asyncio.create_task | async mtl/async |
| await | slurp | .await | <-chan (no direct) | .await | await | await | await | await | await mtl/await |
| spawn+await | slush | spawn().await | no direct equivalent | no direct equivalent | no direct equivalent | asyncio.create_task().result() | no direct equivalent | no direct equivalent |
| timer | cycle | tokio::interval | time.ticker | setinterval | settimeout | settimeout | time.sleep() | thread.sleep() | no direct equivalent |

### ownership

| feature | slop | rust | go | typescript | javascript | python | c++ | haskellel |
|---------|------|-----|----------|--------------|--------|------|-----------|
| move semantics | default | move | references only | move by default | references only | move by default |
| heap cleanup | rekt required | automatic drop | automatic gc | automatic gc | automatic gc | automatic gc | raii garbage collector | deinit | defer |
| lifetime | compiler enforces | borrow checker | no lifetime | no lifetime | no lifetime | no lifetime | borrow checker | raii lifetime |
| copy semantics | stack types only | copy everywhere | copy everywhere | copy everywhere | copy everywhere | copy everywhere | copy everywhere |

### types

| feature | slop | rust | go | typescript | javascript | python | c++ | haskellel |
|---------|------|-----|----------|--------------|--------|------|-----------|
| generics | of t, k (exp) | <t> (exp) | type parameters | generics (exp) | type parameters | no type parameters | generics | no equivalent | generic wildcards | type parameters | no equivalent |
| sum types | mood (exp) | of t, k (exp) | enum (exp) | enum (exp) | enum variants (exp) | type unions (exp) | type aliases | type aliases | type aliases | newtype (exp) | no equivalent | type aliases (exp) | | no equivalent |
| explicit null | nah (built-in) | option (exp) | optional<t> (exp) | no equivalent | union types (exp) | no equivalent | none/null | undefined | undefined | null/nil | optional<t> (exp) | undefined or null |

---

## syntax comparison

### syntax choices

| feature | slop | rust | go | typescript | javascript | python | c++ | haskellel |
|---------|------|-----|---|----------|--------------|--------|------|-----------|
| braces | no, 2-space indent | no braces, 2-space indent | braces | braces | no braces | braces, braces, braces | braces |
| indentation | 2-space strict | 4-space (soft) | 2-space (tabs) | 2-space (soft) | 2-space (tabs) | 4-space (soft) | 2-space (tabs) | 2-space (tabs) | 2-space (soft) | 4-space (soft) | 2-space (tabs) | 4-space (soft) | 2-space (tabs) | 4-space (soft) | 2-space (tabs) | 4-space (soft) | 2-space (tabs) | 4-space (soft) | 2-space (tabs) | 4-space (sloppy) |
| semicolons | yes | yes | yes | yes | yes | yes | yes | yes | yes |
| line comments | #, ## | //, /// | #, ///, // | /// | #, /* | # | #, /* | # | #, /* | # | #, /* | /* | /* | #, doc strings | doc strings | comment (exp) | comment string (exp) |
| strings | "yo", "world" | "yo", "world" | "yo", "world" | "yo", "world" | "yo", "world" | "yo", "world" | "yo", "world" | "yo", "world" |
| variables | lowercase only | snake_case | camelcase | camelcase | snake_case | camelcase | pascalcase | camelcase | snake_case | camelcase | pascalcase | camelcase | snake_case |
| functions | fn name() def func() fn name() fn name() | function name() | def name() | function name() | func name() | def | def func name() |

---

## performance comparison

### compilation speed

| language | slop | rust | go | typescript | go | python | haskellel |
|---------|------|-----|----------|--------------|--------|------|-----------|
| compile time | n/a | fast | moderate | slow | fast | slow | moderate | n/a |
| runtime | fast | very fast | fast | very fast | moderate | moderate | slow | n/a |

### memory overhead

| language | slop | rust | go | typescript | javascript | python | c++ | haskellel |
|---------|------|-----|----------|--------------|--------|------|-----------|
| runtime size | minimal | moderate | large | moderate | large | moderate | n/a |
| gc overhead | none | none | none | none | small | large | moderate | n/a |
- rationale | manual control, predictable | no gc pauses | gc adds overhead for low-end hardware | manual control, predictable, suitable for embedded/blockchain | agent-friendly |

---

## ecosystem comparison

### package managers

| feature | slop | rust | go | typescript | javascript | python | c++ | haskellel |
|---------|------|-----|----------|--------------|--------|------|-----------|
| package format | slop.toml | cargo.toml | package.json | go.mod | package.json | n/a | n/a |
| dependency source | git repos | git repos | git repos | n/a | n/a | n/a | git repos |
| versioning | semantic | semantic | semantic | semantic | semantic | semver | semver | npm versioning | n/a | n/a |

---

## unique slop features

### what slop has that others don't

- **agent-first:** all compiler output is jsonl (machine-readable)
- **lowercase only:** strict enforcement everywhere
- **2-space strict:** no tabs, no mixed indent
- **implicit return:** last line is result
- **lit functions:** special built-ins without parens (print, log, sleep, yeet)
- **! operator:** error handling with reserved e variable
- **bunch packing:** compiler enforces optimal layout
- **mood sum types:** powerful enums with associated data
- **vibe/gives:** clean polymorphism
- **strict organization:** enforced file structure order
- **no garbage collection:** manual memory with rekt enforcement
- **sloppycost:** compile-time cost analysis
- **single source of truth:** all docs in one repo
- **neovim-only lsp:** focused development effort
- **dense versioning:** slow and steady, no rush to v1.0

### why different

not "just another language" - designed with clear purpose:
- agent-first architecture
- sloopy naming for memorability
- consistent indent-only syntax
- explicit error handling
- ownership without complexity
- performance without overhead
- blockchain-ready from epoch 1

---

## migration guides

### from rust to slop

**similarities:**
- ownership model
- error handling patterns
- async/await concepts

**differences:**
- no lifetimes to learn
- simpler error syntax
- lit functions vs macros
- different standard library

### from go to slop

**similarities:**
- async/await concepts
- explicit channel types

**differences:**
- no implicit channels (channels are explicit)
- simpler error handling
- no goroutine concept
- no defer (use rekt instead)

### from python to slop

**similarities:**
- type hints (optional types explicit)
- list comprehensions

**differences:**
- no dynamic typing (all types explicit)
- no classes/object model
- no implicit none

---

## references

slop language specification - see `syntax.md`, `types.md`, `functions.md`
slop versioning - see `versioning.md`
slop changelog - see `changelog.md`