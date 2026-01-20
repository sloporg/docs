# references

**terms you need to know.**

---

## terms

### og

built-in types (`u64`, `bool`, `str`). the original gangstas.

### chunk

a single `.sl` file. compilation unit.

---

## design choices

### lowercase everything

**decision:** all lowercase.
**why:** agents struggle with ambiguity. we removed the choice.

### no garbage collection

**decision:** stack only.
**why:** speed. determinism. simplicity.
