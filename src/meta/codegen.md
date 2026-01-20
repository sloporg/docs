# implementation

**bootstrap strategy using llvm.**

since we don't have a working slop compiler yet, we write the first one in rust (`sloprsc`).
it parses slop code and generates llvm ir.

---

## architecture

1. **lexer** - source text -> tokens (strict lowercase, indent/dedent)
2. **parser** - tokens -> ast (pratt parsing)
3. **codegen** - ast -> llvm ir (using `inkwell` or `llvm-sys`)
4. **link** - llvm ir -> executable (using `clang` or `llc`)

---

## llvm codegen steps

### 1. setup
initialize the llvm context, module, and builder.

```rust
let context = Context::create();
let module = context.create_module("slop_module");
let builder = context.create_builder();
```

### 2. types
map slop types to llvm types.

- `u64` -> `context.i64_type()`
- `bool` -> `context.bool_type()` (i1)
- `str` -> `context.i8_type().ptr_type(AddressSpace::Generic)` (c-string or struct)

### 3. functions
for each function in ast:
1. define function type (param types -> return type).
2. add function to module.
3. create entry basic block.
4. position builder at entry.

### 4. stack allocation
slop is **stack-only**. for every variable assignment:
1. create an `alloca` instruction in the entry block.
2. store value using `build_store`.
3. load value using `build_load` when referenced.

### 5. control flow
translate `if/else` and `while`:
1. create basic blocks: `then_bb`, `else_bb`, `merge_bb`.
2. build condition expression (i1).
3. `build_cond_br(condition, then_bb, else_bb)`.
4. populate blocks.
5. `build_br(merge_bb)` at end of branches.

---

## reference

- **llvm language reference:** [llvm.org/docs/LangRef.html](https://llvm.org/docs/LangRef.html)
- **inkwell (rust bindings):** [github.com/TheDan64/inkwell](https://github.com/TheDan64/inkwell)
