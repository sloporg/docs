# changelog

slop version history

---

## [0.0.1] - 2026-01-16

### added

- complete language specification
- compiler architecture documentation
- implementation docs structure (dev/)
- all feature docs (syntax, types, functions, imports, memory, errors, concurrency, std)
- lsp specification (neovim only)
- ffi specification
- slopbind specification
- evm specification
- versioning strategy
- roadmap documentation
- slopy single source of truth

### changed

- renamed repository from `slop` to `docs`
- updated all cross-references
- established clean git history with sloopy commits
- all documentation 100% lowercase

### fixed

- removed all duplicate content
- eliminated misleading git history
- organized docs into single source of truth
- separated language specs from implementation

### experimental promoted to stable

- none this release

---

## unreleased

### version 0.1.0

focus: basic execution and parsing

status: cooking

planned features:
- lexer and parser implementation
- basic codegen for amd64/arm64
- jsonl diagnostic output
- real-time diagnostics via lsp

---

## coming soon

### version 0.2.0

focus: data core

planned features:
- all og types (u8..u256, i8..i256, f32, f64, b8..b256, bs, str, ch, bool, nah)
- collections (arrays, maps, tuples)
- bunch declarations with auto .str()
- bunch packing enforcement
- module system
- slop.toml for package mapping

---

### version 0.3.0

focus: flow core

planned features:
- error handling (! operator, e reserved var, err type, yeet)
- automatic rekt e insertion
- concurrency (slug, slap, slurp, slush, cycle)
- rekt enforcement (linear memory)
- auto .str() for all types

---

### version 0.4.0

focus: system core

planned features:
- header parsing (arch <system> <version>)
- asm blocks (inline assembly, target-dependent)
- sloppycost analysis (automatic optimization)
- time literals (10s, 100ms)
- time properties (time.iso, time.nano)

---

### version 0.5.0

focus: world standard

planned features:
- env (args, environment variables)
- io (readline, readall, lines)
- fs (read, write, lines, existence)
- os (exit)
- net (tcp sockets)
- math (pi, abs, ceil, floor, mid, mean, pow, sqrt, min, max, sort)
- tree-shaking (unused modules purged)
- c ffi basics (libclang-based, slopbind tool)
- lsp basics (neovim only - syntax highlighting, diagnostics, auto-indent)
- self-hosting begins (can compile simple slop programs in slop)

---

### version 0.6.0

focus: domain standards

planned features:
- crypto (keccak256, sha256, ed25519, secp256k1)
- codec (hex, base58, base64)
- c ffi complete (full libclang integration)
- evm (complete evm integration)
- svm (solana integration)
- data structures (stack, queue, tree, graph)
- self-hosting complete - rust compiler dropped
- lsp complete (go-to-definition, auto-complete, refactoring)

---

### version 0.7.0

focus: built-in libraries

planned features:
- http (get, post, request)
- json (parse, stringify)
- yaml (parse, stringify)
- python ffi (embedded cpython, .pyi parsing)

---

### version 0.8.0

focus: external libraries

planned features:
- module resolution (import by name)
- slop.toml dependency management
- git repos and local paths
- javascript ffi (wasm target, .d.ts parsing)

---

## version 1.0.0

focus: api frozen, production ready

criteria:
- epochs 1-8 complete
- self-hosting complete
- documentation complete
- real-world usage validates
- feels ready for production

guarantees:
- no breaking changes to core language
- experimental features can still break
- deprecation warnings for 2 releases before removal

---

## migration guide

### upgrading from 0.x to 1.0

major breaking changes will be documented with:
- clear migration instructions
- deprecation timeline
- automated upgrade tools

### experimental features

features marked experimental may:
- have breaking changes between releases
- require no recompile when graduating to stable
- syntax changes will be clearly documented

---

## contribution guidelines

for contributors:

- all commits must be sloopy (lowercase only)
- follow dense versioning model
- 100% test coverage for new features
- documentation required for all changes
- real-world usage before promotion

---

## changelog format

### example entry

```markdown
## [0.2.0] - 2026-02-15

### added
- arrays, maps, tuples
- bunch declarations
- module system

### changed
- improved error messages
- faster lexer performance

### fixed
- parser bug with nested blocks
```

---

## see also

- [versioning.md](versioning.md) - versioning strategy and dense model
