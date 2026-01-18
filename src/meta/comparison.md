# comparison

**slop vs the world.**

---

## slop vs rust

**rust:** complex borrow checker. lifetime annotations everywhere. long compile times.
**slop:** stack focused. copy semantics by default. fast compilation. simpler mental model.

---

## slop vs go

**go:** explicit error checks everywhere. garbage collector pauses.
**slop:** no gc pauses. predictable performance. panic-on-error (current epoch).

---

## slop vs python

**python:** inconsistent casing. mixed indentation. slow execution.
**slop:** strict lowercase. strict indentation. high performance.

---

## slop vs c

**c:** segfaults. buffer overflows. undefined behavior.
**slop:** defined behavior. strict structure. safer defaults.

---

## summary

| feature | slop | rust | go | python |
|---------|------|------|----|--------|
| **memory** | stack (copy) | ownership | gc | gc |
| **speed** | fast | fast | fast | slow |
| **casing** | strict | mixed | mixed | mixed |
| **indent** | strict | mixed | mixed | strict |
| **vibes** | disciplined | complex | pragmatic | flexible |
