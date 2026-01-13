# versioning

slop versioning strategy

---

## philosophy

**dense versioning** - each release is complete, tested, and stable

we do not rush to v1.0

---

## version scheme

**0.0.x** - pre-alpha development, breaking changes expected

**0.1.x** - epoch 1 stable, patch releases

**0.2.x** - epoch 2 stable, patch releases

**0.x.0** - new epoch milestone

**1.0.0** - api frozen, production ready

---

## epochs

each epoch introduces major features and must be **dense** before moving forward

### dense means

- all planned features complete and tested
- test coverage ≥ 95% for new features
- no critical bugs remaining
- documentation complete for all features
- real-world usage validates design
- feels right - no rush, take time needed

---

## experimental features

**policy:**
- all experimental features available with `--experimental` flag
- use at your own risk in production
- may have breaking changes between releases
- graduation to stable announced in changelog when promoted

**graduation criteria:**
- feature fully tested with ≥ 95% coverage
- no known bugs for current release cycle
- feels right and ready
- announced in changelog when promoted
- included in next epoch release

---

## feature lifecycle

**inception** - develop in feat/<name> branch

**experimental** - merge to main, available with `-exp` flag

**stabilization** - promote to stable epoch when ready, announced in changelog

---

## release process

### patch releases (0.x.y)

**when:** bug fixes, minor improvements, no new features

**frequency:** as needed

**breaking changes:** never

**example:** 0.1.0 → 0.1.1 (fix parser bug)

### minor releases (0.x.0)

**when:** new epoch, major features added

**frequency:** when dense (no fixed schedule)

**breaking changes:** allowed, documented in changelog

**example:** 0.1.0 → 0.2.0 (epoch 2 data core complete)

### major release (1.0.0)

**when:** all planned features complete, production ready

**criteria:**
- epochs 1-8 complete
- self-hosting complete
- documentation complete
- community validation (if any)
- feels ready for production

**guarantees after 1.0:**
- no breaking changes to core language
- experimental features can still break
- deprecation warnings for 2 releases before removal

---

## version header

all slop files declare their version:

```slop
arch <system> <version>
```

**format:** `arch <system> <version>`

**version:** minimum compiler version required

**purpose:**
- forward compatibility checking
- feature gating (experimental)
- compilation target selection

---

## backward compatibility

**before 1.0:** breaking changes allowed between epochs

**after 1.0:** no breaking changes to core language

**experimental:** can break anytime (that's the point)

---

## changelog

all releases documented in `changelog.md`:

**format:**
```markdown
## [0.2.0] - 2026-02-15

### added
- arrays, maps, tuples
- bunch declarations
- module system
- tree-shaking

### changed
- improved error messages
- faster lexer performance

### fixed
- parser bug with nested blocks
- memory leak in string interpolation

### experimental promoted to stable
- none this release
```

---

## current status

**version:** 0.0.1

**status:** language specification complete, compiler cooking

**next milestone:** epoch 1 (v0.1.0) - basic execution and parsing complete

---

## comparison with other languages

### rust

similar dense versioning, long road to v1.0

### go

rapid to 1.0, strong compatibility guarantee

### python

long cycles, major versions break compatibility

### slop approach

rust-style density + go-style stability post-1.0

---

## architecture versioning

**native architectures:**
- `arch amd64 0.0.1` - x86-64 targets
- `arch arm64 0.0.1` - arm64 targets

**evm:**
- `arch evm fusaka` - named hardfork
- `arch evm 0.11.0` - version number

**svm:**
- `arch svm mainnet 1.18` - network + version
- `arch svm devnet 1.18` - testnet + version

**version starts at:** 0.0.1 (not 0.1.0)

**increment with:** language features, not arch updates

---

## self-hosting impact

**epoch 5:** self-hosting begins (can compile simple slop programs in slop)

**epoch 6:** self-hosting complete (rust compiler dropped)

**after epoch 6:** compiler written in slop itself, bootstrapped from previous version

**versioning stays same:** epochs continue as normal

---

## dense versioning explained

each epoch must be **dense** before moving forward

**dense means:**
- all planned features complete and tested
- test coverage ≥ 95% for new features
- no critical bugs remaining
- documentation complete for all features
- real-world usage validates design
- feels right - no rush, take the time needed