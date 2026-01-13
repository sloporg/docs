# errors

error handling in slop [→](references.md#error-handling)

---

## error system overview

slop uses a type-safe error handling system with severity levels and error codes.

**components:**
- **bunches:** `info`, `err`, `warn` (std predefined)
- **moods:** `icode`, `ecode`, `wcode` (hex codes starting at 0x00)
- **log functions:** `logi`, `loge`, `logw`, `log` (type-checked)
- **! operator:** error handling with optional fallback
- **yeet:** fatal error termination

---

## error codes

### icode (info codes) [→](references.md#icode)

```slop
mood icode u64
  none = 0x00
  started = 0x01
  stopped = 0x02
  ready = 0x03
  complete = 0x04
  progress = 0x05
  connected = 0x06
  disconnected = 0x07
```

### ecode (error codes) [→](references.md#ecode)

```slop
mood ecode u64
  none = 0x00
  
  # 0x01-0x0F: not found/missing
  notfound = 0x01
  noent = 0x02
  nofile = 0x03
  nodir = 0x04
  
  # 0x10-0x1F: permission/auth
  unauthorized = 0x10
  forbidden = 0x11
  noperm = 0x12
  
  # 0x20-0x2F: input/validation
  invalid = 0x20
  badreq = 0x21
  badtype = 0x22
  badformat = 0x23
  
  # 0x30-0x3F: state/conflict
  conflict = 0x30
  exists = 0x31
  locked = 0x32
  
  # 0x40-0x4F: i/o
  ioerr = 0x40
  readerr = 0x41
  writeerr = 0x42
  
  # 0x50-0x5F: network
  timeout = 0x50
  unreachable = 0x51
  refused = 0x52
  
  # 0x60-0x6F: parse/decode
  parseerr = 0x60
  decodeerr = 0x61
  encodeerr = 0x62
  
  # 0x70-0x7F: resource
  nomem = 0x70
  nospace = 0x71
  toobig = 0x72
  
  # 0xF0-0xFF: system/unknown
  syserr = 0xf0
  unknown = 0xff
```

### wcode (warning codes) [→](references.md#wcode)

```slop
mood wcode u64
  none = 0x00
  deprecated = 0x01
  fallback = 0x02
  slowpath = 0x03
  retry = 0x04
  partial = 0x05
  truncated = 0x06
  redundant = 0x07
  unused = 0x08
  experimental = 0x09
```

---

## error bunches

### info bunch

```slop
bunch info
  code icode
  msg str
```

### err bunch [→](references.md#infoerr-bunches)

```slop
bunch err
  code ecode
  msg str
```

### warn bunch

```slop
bunch warn
  code wcode
  msg str
```

---

## log functions

### logi - info logging

```slop
logi icode.started, "server starting on port 6969"
logi icode.complete, "processing finished"
```

**signature:** `logi icode, str`  
**takes:** info code and message  
**output:** jsonl to stdout (controlled by -v flag)

### loge - error logging [→](references.md#log-functions)

```slop
loge ecode.notfound, "user not found"
loge ecode.timeout, "request timed out after 30s"
```

**signature:** `loge ecode, str`  
**takes:** error code and message  
**output:** jsonl to stderr (always shown)

### logw - warning logging

```slop
logw wcode.deprecated, "use newapi instead"
logw wcode.fallback, "using default config"
```

**signature:** `logw wcode, str`  
**takes:** warning code and message  
**output:** jsonl to stderr (controlled by -vv flag)

### log - unstructured logging

```slop
log "debug: processing item 42"
```

**signature:** `log str`  
**takes:** plain message (no code)  
**discouraged:** use logi/loge/logw for structured logging

---

## yeet function [→](references.md#yeet)

fatal error termination with error code.

```slop
yeet ecode.unauthorized, "access denied"
```

**signature:** `yeet ecode, str`  
**behavior:**
0x01. calls `loge` with error code and message  
0x02. calls `os.exit(1)` to terminate process

**use cases:**
- unrecoverable errors
- critical failures
- security violations

---

## ! operator [→](references.md#-operator)

error handling operator with optional fallback value.

### default behavior (auto-yeet)

```slop
data = readfile("config.txt") !
# if error: yeet ecode automatically
print data
```

### custom fallback

```slop
data = readfile("config.txt") !
  logw wcode.fallback, "using default config"
  "port: 6969\nhost: localhost"

print data  # guaranteed to have value
```

### error variable

inside ! block, `e` variable contains the error:

```slop
data = readfile("config.txt") !
  loge e.code, "failed: $e.msg"
  yeet ecode.ioerr, "cannot continue"
```

**note:** cannot use `ret` in ! block, only expressions or yeet

---

## function error returns

functions return errors as part of multiple return values.

### declaring error returns

```slop
fn readfile(path str) (str, err)
  if !exists(path)
    ret ("", err{code: ecode.notfound, msg: "file not found"})
  
  content = fs.read(path)
  (content, nah)  # nah = no error
```

**pattern:** `(result, err)` where `err` is error bunch  
**no error:** return `nah` in error position

### calling with ! operator

```slop
fn process()
  # auto-yeet on error
  content = readfile("data.txt") !
  
  # custom handling
  content = readfile("data.txt") !
    logw wcode.fallback, "file missing, using empty"
    ""
  
  parse(content)
```

---

## error handling patterns

### pattern 0x01: early return with error check

```slop
fn validate(usr usr) err
  if usr.name == ""
    ret err{code: ecode.invalid, msg: "name required"}
  
  if usr.id == 0
    ret err{code: ecode.invalid, msg: "id required"}
  
  nah  # no error
```

### pattern 0x02: propagate with !

```slop
fn loadconfig() (config, err)
  raw = readfile("config.txt") !
  data = parse(raw) !
  (data, nah)
```

### pattern 0x03: fallback chain

```slop
fn getconfig() str
  content = readfile("config.txt") !
    logw wcode.fallback, "trying default location"
    readfile("/etc/app/config.txt") !
      logw wcode.fallback, "using hardcoded defaults"
      "port: 4200"
  
  content
```

### pattern 0x04: manual error handling

```slop
fn process()
  data, er = readfile("input.txt")
  if er.code != ecode.none
    loge er.code, "failed to read: $er.msg"
    ret
  
  print data
```

**discouraged:** use ! operator instead for cleaner code

---

## exitcode mood

standardized exit codes for `os.exit()`.

```slop
mood exitcode u64
  success = 0x00
  generalerr = 0x01
  misuse = 0x02
  noinput = 0x42
  unavailable = 0x45
  # ... more codes
```

**usage:**
```slop
fn main()
  if condition
    os.exit(exitcode.success)
  else
    os.exit(exitcode.generalerr)
```

---

## automatic cleanup

compiler automatically inserts `rekt` before all returns, including error paths.

```slop
fn process() err
  data = allocate()
  
  if problem
    ret err{code: ecode.ioerr, msg: "failed"}
    # compiler inserts: rekt data
  
  nah
  # compiler inserts: rekt data
```

**see:** [memory.md](memory.md#auto-rekt) for details

---

## reserved names

- `e` - error instance in ! blocks (not globally reserved)
- `err` - error bunch type (reserved)
- `error` - available for user variables

**recommended:** use `er` for error variables outside ! blocks

---

## examples

### example 0x01: http server error handling

```slop
arch amd64 0.0.1

use net
  http

fn handlereq(req http.request) (http.response, err)
  if req.method != "get"
    ret (http.response, err{code: ecode.badreq, msg: "only get allowed"})
  
  data = readfile("index.html") !
    ret (http.response{status: 0x194, body: "not found"}, nah)
  
  (http.response{status: 0xc8, body: data}, nah)

fn main()
  server = net.listen("0.0.0.0", 6969) !
  
  for req in server.accept()
    res, er = handlereq(req)
    if er.code != ecode.none
      loge er.code, "request failed: $er.msg"
    req.send(res)
```

### example 0x02: config loading with fallbacks

```slop
arch amd64 0.0.1

use fs
  env

fn loadconfig() str
  # try environment variable
  path = env.get("config")
  if path != ""
    content = readfile(path) !
      logw wcode.fallback, "env config missing"
    if content != ""
      ret content
  
  # try local file
  content = readfile("./config.txt") !
    logw wcode.fallback, "local config missing"
  if content != ""
    ret content
  
  # try system location
  content = readfile("/etc/app/config.txt") !
    logw wcode.fallback, "system config missing, using defaults"
    ret "port: 6969\nhost: 0.0.0.0"
  
  content
```

### example 0x03: validation with early returns

```slop
arch amd64 0.0.1

bunch usr
  id u64
  name str
  email str

fn validate(u usr) err
  if u.id == 0
    ret err{code: ecode.invalid, msg: "id cannot be zero"}
  
  if u.name == ""
    ret err{code: ecode.invalid, msg: "name required"}
  
  if !u.email.has("@")
    ret err{code: ecode.invalid, msg: "invalid email format"}
  
  nah

fn createusr(u usr) err
  er = validate(u)
  if er.code != ecode.none
    ret er
  
  db.save(u) !
  
  logi icode.complete, "user created: $u.id"
  nah
```

---

## see also

- [functions.md](functions.md) - return types, multiple returns
- [memory.md](memory.md) - auto rekt, cleanup
- [references.md](references.md#error-handling) - error system design
