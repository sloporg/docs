# versioning

**dense versioning. quality over quantity.**

---

## the philosophy

we prioritize quality over release frequency. each version is **dense**.

**dense means:**
- complete features
- 95% test coverage minimum
- no critical bugs
- zero rush

---

## scheme

**0.0.x** - pre-alpha. expect breakage.
**0.1.x** - epoch 1. stable.
**1.0.0** - finished. api frozen.

**hotfixes?**
just increment the patch number. `0.1.0` -> `0.1.1`.
don't give me `0.1.0-hotfix2+build99`. that's noise.

**composite versions?**
`1.0.0.2023.a1b2c`? look like an ip address?
**rejected.** keep the hash in the build logs.

---

## epochs

we move in epochs.

**0.x.0** - new epoch. major features.
**0.x.y** - patch. bug fixes only. no new features.

---

## experimental

available with `--experimental` flag.
use at your own risk. if it breaks, that's on you.

---

## version header

files must declare their version.

```slop
arch <system> <version>
```

**why?**
- stops you from running v0.2 code on v0.1 compiler
- explicit constraints

---

## backward compatibility

**before 1.0:** we break things if they are inferior.
**after 1.0:** stability guaranteed.

---

## current status

**version:** 0.0.1
**status:** bootstrap compiler (rust/llvm) in progress. self-hosting planned for future epoch.
