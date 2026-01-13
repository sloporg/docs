# imports

module import system for slop

---

## syntax

**single import:**
```slop
use env
```

**multiple imports (2-space indent):**
```slop
use env
  fs
  math
  crypto
```

equivalent to:
```slop
use env
use fs
use math
use crypto
```

## rules

- one `use` keyword starts import block
- continuation lines must be indented exactly 2 spaces
- each module on its own line
- can mix single and multi-line styles
- empty line or dedent ends block

## examples

**basic:**
```slop
arch amd64 0.0.1

use env
  fs
  io
```

**multiple blocks:**
```slop
arch amd64 0.0.1

use env
  fs
  io
use crypto
  codec
use evm.env
  evm.storage
  evm.call

fn main()
  args = env.args()
```

**mixed style:**
```slop
use env
  fs
use crypto
use http
  json
  yaml
```

## module resolution

**built-in modules:**
- no prefix needed
- directly available: `use env`, `use fs`, `use math`

**external modules:**
- defined in `slop.toml`
- imported by name: `use bigint`

**evm modules:**
- namespaced: `use evm.env`, `use evm.storage`
- must import each sub-module explicitly

**svm modules:**
- namespaced: `use svm.accountinfo`, `use svm.invoke`
- must import each sub-module explicitly

## visibility

**default:** all imports are private to chunk

**re-export:** use `leak` to make imported items public
```slop
leak use crypto  # re-export crypto module
```

## tree-shaking

**automatic:** unused imports removed at compile time

**whole-module:** if no functions from module used, entire module eliminated

**epoch 5+:** tree-shaking for all built-in modules

## import order

imports must appear in file after `arch` header and before any other declarations

**enforced order:**
1. `arch` header
2. `use` statements
3. `const` declarations
4. `type` aliases
5. `mood` declarations
6. `bunch` declarations
7. `vibe` declarations
8. `gives` implementations
9. functions
10. `main` function (last if exists)

## no relative imports

**not supported:**
```slop
use ./helper     # error
use ../utils     # error
```

**instead:** define in `slop.toml` and import by name
```toml
[dependencies]
helper = { path = "./helper" }
utils = { path = "../utils" }
```

```slop
use helper
use utils
```

## no glob imports

**not supported:**
```slop
use env.*        # error
use crypto.*    # error
```

**instead:** import explicitly
```slop
use env
use crypto
```

## lsp support

**virtual alignment (epoch 5+):**
- files store 2-space indents (canonical)
- lsp renders continuation lines at column 4
- purely visual, no file changes

**auto-indent (epoch 5+):**
- pressing enter after `use modulename` auto-indents to 2 spaces
- empty line + enter exits import block
- `:slopimports` command organizes imports

**example virtual alignment:**
```slop
# what user sees:
use env
    fs
    io

# actual file content:
use env
  fs
  io
```

## rationale

**why multi-line syntax:**
- less repetitive `use` keywords
- clear visual grouping of related modules
- smaller file size
- matches indent-based philosophy
- unambiguous start/end of import block

**why 2-space indent:**
- consistency with rest of language
- no special cases in grammar
- simpler parser implementation

**why no relative imports:**
- clearer dependency graph
- all deps in one place (slop.toml)
- easier for tools to analyze
- prevents circular dependencies

**why no glob imports:**
- explicit is better than implicit
- easier to understand what's imported
- better for tree-shaking
- prevents naming conflicts