# memory

**stack only. predictable and fast.**

---

## no garbage collection

**gc pauses are unacceptable.**

slop v0.0.1 uses strict stack allocation. applications that need consistent low-latency cannot afford stop-the-world pauses.

**why?**
- **zero overhead** - maximum throughput
- **deterministic** - predictable execution time
- **simple** - explicit resource lifecycle

---

## memory model

### stack-allocated

current types are stack-allocated. values persist only for the function scope.

- **u64** - 8 bytes
- **bool** - 1 byte
- **str** - pointer to data segment

**behavior:**
- enter function -> alloc
- exit function -> destroy
- automatic cleanup by compiler

### copy semantics

values are copied on assignment by default in this epoch.

```slop
a = 67
b = a                            # b is a copy. a remains valid.
print a                          # valid
print b                          # valid
```

---

## future

heap allocation and manual memory management (`rekt`) are planned for future epochs.
