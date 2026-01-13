# slop

**agents are writing your code. your language should make that easy.**

---

## the problem

**ai agents are replacing developers. but they're stuck with human languages.**

- **too many ways to do things** - python has 4 string formats. agents guess wrong.
- **syntax variations everywhere** - camelCase? snake_case? PascalCase? agents hallucinate.
- **garbage collected pauses** - your agent-written service freezes for 50ms. users bounce.
- **error handling chaos** - try/catch, if err != nil, result<t,e>. every language different.

**the real pain:**
- agents hallucinate casing (`userName` vs `user_name` vs `UserName`)
- compiler errors aren't machine-readable (good luck parsing gcc output)
- manual memory breaks in production
- business logic buried in boilerplate

---

## enter slop

**do the minimum to achieve business logic. compiler handles the rest.**

### agent-first design
- **jsonl compiler output** - every error, warning, fix is structured json. agents parse perfectly.
- **strict lowercase** - no camelCase vs snake_case. everything lowercase. zero hallucinations.
- **one correct way** - `bunch` for structs, `mood` for enums. no alternatives.
- **operator overloading** - `pnt + pnt` just works. define via methods. minimal syntax.

### memory that just works
- **auto cleanup** - compiler inserts `rekt` before returns. like go defer but automatic.
- **ownership tracking** - compiler knows what you own. prevents use-after-free.
- **explicit init check** - `.isinit()` method. no confusion with empty values.

### built for reality
- **ffi everywhere** - call c, python, js. manual package control (you pick versions).
- **blockchain native** - compiles to evm/svm opcodes. no solidity needed.
- **inline asm** - drop to assembly when needed. target-dependent.

---

## show me code

```slop
arch amd64 0.0.1

bunch usr
  id u64
  name str
  
  leak fn isvalid() bool
    deez.name != ""

fn getusr(id u64) (usr, err)
  if id == 0
    ret (usr, err{code: ecode.invalid, msg: "invalid id"})
  (usr{id: id, name: "anon"}, nah)

fn main()
  u = getusr(1) !
    logw wcode.fallback, "using guest"
    usr{id: 0, name: "guest"}
  
  if u.isvalid()
    print u.name
  # rekt u inserted automatically
```

**what just happened:**
- `bunch usr` with method - methods inside bunch with `deez`
- `(usr, err)` - multiple returns with parens
- `!` operator - custom fallback on error
- `nah` - no error marker
- `leak fn` - public method
- auto `rekt` - cleanup before exit
- strict lowercase, 2-space indent - agent can't mess it up

---

## why slop exists

**for agents writing production code.**

traditional languages were designed for humans reading code. slop is designed for machines writing code and humans reviewing it.

- **one correct way** - agents don't hallucinate variations
- **minimum syntax** - operator overload via methods, auto cleanup, explicit types
- **strict structure** - file sections enforced by compiler (chunk = file)
- **machine-readable errors** - jsonl output with fix diffs

**for systems that need control.**

blockchain contracts, embedded devices, real-time services - if you need predictable behavior and zero gc pauses, slop delivers.

---

## core concepts

- **chunk:** each `.sl` file is a module
- **bunch:** struct with auto `.str()`, `.json()` methods (via built-in vibes)
- **mood:** sum types with variants (explicit base type)
- **vibe:** trait/interface - define contracts, implement via `gives` or auto-satisfied
- **gives:** implement vibe methods for types (og types or bunches)
- **deez:** instance reference in methods (like `self` in rust)
- **leak:** public visibility modifier
- **! operator:** error handling with optional fallback
- **yeet:** fatal error (takes ecode, logs, exits)
- **rekt:** memory deallocation (compiler-inserted before returns)
- **nah:** void/nothing/no-error

see [references](references.md) for complete concept guide

---

## get started

- [quickstart](quickstart.md) - write your first program
- [syntax](syntax.md) - language rules
- [showcases](showcases.md) - real examples

**or dive deep:**
- [types](types.md) - bunch, mood, vibe system
- [functions](functions.md) - methods, naming conventions
- [errors](errors.md) - ! operator, error codes, yeet
- [memory](memory.md) - ownership, auto rekt, init tracking
- [ffi](ffi.md) - calling c/python/js
- [evm](evm.md) - blockchain contracts

---

## language reference

### core features
- [syntax](syntax.md) - grammar, indentation, literals, asm blocks
- [types](types.md) - og types, collections, bunch, mood, vibe
- [functions](functions.md) - user functions, methods, lit functions
- [imports](imports.md) - module system, leak exports
- [memory](memory.md) - ownership, move semantics, auto rekt
- [errors](errors.md) - ! operator, error codes, yeet
- [concurrency](concurrency.md) - slug, slap, slurp, slush

### libraries
- [std](std.md) - standard library modules
- [ffi](ffi.md) - foreign function interface
- [slopbind](slopbind.md) - binding generator
- [evm](evm.md) - blockchain integration

### tools
- [lsp](lsp.md) - language server (neovim only)
- [compiler](compiler.md) - compiler overview, jsonl output
- [testing](testing.md) - test framework, bet assertions

---

## development

- [versioning](versioning.md) - dense versioning, epochs
- [roadmap](roadmap.md) - 8-epoch timeline
- [changelog](changelog.md) - version history
- [comparison](comparison.md) - vs rust, zig, go
- [references](references.md) - sources, credits, inspirations

---

## examples

- [showcases](showcases.md) - real-world programs
- [benchmarks](benchmarks.md) - performance tests

---

## principles

- **lowercase everywhere** - identifiers, keywords, files (no underscores)
- **2-space indent strict** - compiler enforces
- **explicit memory** - rekt required (compiler-inserted), compiler tracks
- **agent-readable** - jsonl errors with fix diffs
- **one way** - no syntax variations, clear path

---

## status

**epoch 1 (v0.0.1)** - current target  
compiler in development: [github.com/sloporg/sloprsc](https://github.com/sloporg/sloprsc)

see [roadmap](roadmap.md) for 8-epoch plan

---

## inspired by

rust (ownership), zig (simplicity), go (methods), python (indent)  
see [references](references.md) for full credits and sources

---

*docs: github.com/sloporg/docs*  
*compiler: github.com/sloporg/sloprsc*
