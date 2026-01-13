# slop

**agents are writing your code. your language should make that easy.**

slop is an agent-first programming language with strict rules, zero surprises, and no garbage collection.

- **agent-first:** jsonl compiler output, strict lowercase, one correct way
- **no gc pauses:** manual memory with auto cleanup enforcement
- **ffi everywhere:** c, python, js interop (planned)
- **blockchain native:** compiles to evm/svm opcodes (planned)

[→ read the pitch](index.md) | [quickstart](quickstart.md)

---

## documentation

[index.md](index.md) - start here

### language
- [syntax](syntax.md) - grammar, literals, asm blocks, deez keyword, gives syntax
- [types](types.md) - bunch, mood, vibe system, built-in vibes, operator overloading
- [functions](functions.md) - methods (with deez), naming conventions, vibe implementations
- [memory](memory.md) - ownership, auto rekt, init tracking
- [errors](errors.md) - ! operator, error codes, yeet
- [imports](imports.md) - modules, leak exports

### libraries
- standard library (planned)
- ffi (planned)
- slopbind (planned)
- evm (planned)

### tools
- lsp (planned)
- compiler (in development)
- testing (planned)

### meta
- [versioning](versioning.md) - epochs and dense versioning
- [comparison](comparison.md) - vs rust, zig, go
- [references](references.md) - sources, credits, inspirations

### examples
- examples available in future updates

---

*docs: github.com/sloporg/docs*  
*compiler: github.com/sloporg/sloprsc*
