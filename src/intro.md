# slop

**agents write your code. slop forces them to be right.**

---

## the problem

**ai agents hallucinate. traditional languages let them.**

- **too many ways to do things** - python has 4 string formats. agents pick wrong.
- **casing chaos** - `camelCase`? `snake_case`? `PascalCase`? agents make stuff up.
- **gc pauses kill performance** - your service freezes mid-request. users leave.

**agents can't handle flexibility:**
- hallucinate variable names (`userName` vs `user_name` vs `UserName`)
- can't parse compiler errors (gcc output is a nightmare)
- write code that looks right but breaks in prod

---

## slop is extremely opinionated

**one way to do everything. compiler enforces. no exceptions.**

slop doesn't give you choices. slop tells you how it is.

### no flexibility, no mistakes
- **strict lowercase only** - `username`, never `userName`. compiler rejects anything else.
- **2-space indent only** - no tabs. no 4 spaces. compiler rejects it.
- **one file structure** - arch, const, fn, main. always this order. compiler enforces.

### minimal by design
- **core types** - u64, bool, str form the foundation in v0.0.1.
- **stack-only memory** - no heap. no gc. predictable and fast.
- **explicit everything** - function signatures need types. no guessing.

---

## show me code

```slop
arch amd64 0.0.1

const max u64 = 100

fn double(x u64) u64
  x * 2

fn main()
  result = double(21)
  if result > max
    print "too big"
  else
    print "ok"
```

**what just happened:**
- lowercase enforced (`result`, not `Result`)
- 2-space indent (try tabs, compiler rejects)
- implicit return (last expression)
- no semicolons (waste of bytes)
- stack values (copied on assign)

---

## why slop is so opinionated

**because agents need guardrails, not freedom.**

traditional languages optimize for human flexibility. slop optimizes for machine correctness.

when there's only one way to write something, agents can't mess it up.

- **no style debates** - compiler decides everything
- **no syntax variations** - one correct way, period
- **no runtime surprises** - stack-only, no gc, predictable

slop doesn't trust you to make good choices. slop makes them for you.

---

## core concepts (v0.0.1)

- **chunk:** each `.sl` file is a module
- **arch header:** `arch amd64 0.0.1` - required first line, declares target and version
- **const:** compile-time constants only
- **fn:** functions with explicit type signatures
- **stack-only:** everything on the stack, copied on assignment
- **implicit return:** last expression is the return value

---

## language reference

- [quickstart](prelude/quickstart.md) - write your first program
- [syntax](basics/syntax.md) - the rules (non-negotiable)
- [types](basics/types.md) - u64, bool, str (that's all)
- [functions](basics/functions.md) - how to define functions
- [memory](core/memory.md) - stack allocation model
- [conventions](core/conventions.md) - naming (enforced by compiler)

---

## meta

- [versioning](meta/versioning.md) - dense versioning, epochs
- [comparison](meta/comparison.md) - vs rust, zig, go
- [references](meta/references.md) - sources, credits

---

**epoch 1 (v0.0.1)** - stack-only foundation

inspired by: rust (ownership), zig (simplicity), go (methods), python (indent)

---

## roadmap

**v0.0.1 (current)** - bootstrap compiler (`sloprsc`) written in rust. relies on llvm for code generation. [implementation details](meta/codegen.md).
**future** - self-hosting compiler (`slopc`) written in slop. no llvm dependency. pure slop.

