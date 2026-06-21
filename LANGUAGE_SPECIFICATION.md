# FractoScript-13 Language Specification

## Version 1.0.0

---

## 1. Overview

**FractoScript-13 (FS-13)** is a deterministic, rule-driven symbolic transformation engine that processes sequences through multi-cycle timing rules and self-referential control mechanisms.

### Core Properties:
- **Deterministic:** Identical input always produces identical output
- **Stateful:** Maintains global state across transformations
- **Self-aware:** Rule 13 enables self-modification through line-number detection
- **Cyclic:** Transformation cycles triggered at intervals: 5, 13, 20, 21, 33, 132, 165

---

## 2. Data Model

### 2.1 Symbol System

A **symbol** is the atomic unit of FS-13. Symbols consist of:

```
Symbol ::= ASCII_CHAR | EXTENDED_SYMBOL

EXTENDED_SYMBOL ::= "§" | "◆" | "◇" | "▲" | "▼" | "●" | "○"
```

**Initial state:** Each symbol begins as ASCII character or extended symbol.

### 2.2 State Object

Each line maintains a state tuple:

```
State = (symbol, flags, metadata, rotation_count, recursion_depth)

WHERE:
  symbol          ∈ {ASCII | EXTENDED_SYMBOL}
  flags           ∈ {sealed, mutated, duplicated, collapsed, ruptured}
  metadata        = {creation_line, mutations_applied, transformations}
  rotation_count  ∈ ℤ≥0
  recursion_depth ∈ ℤ≥0
```

### 2.3 Global State

```
GlobalState = {
  cycle_count:      ∈ ℤ≥0
  lines:            List[State]
  sealed:           Bool
  rule_13_frozen:   Bool
  rupture_active:   Bool
}
```

---

## 3. Execution Model

### 3.1 Input Format

An FS-13 program is a sequence of lines:

```
INPUT ::= Line+

Line ::= SYMBOL | SYMBOL METADATA
```

**Example:**
```
A
B §rotated
C ◆sealed
```

### 3.2 Processing Pipeline

```
INPUT
  ↓
[INITIALIZE STATE]
  ↓
[CYCLE LOOP: 0 → MAX_CYCLES]
  ├─ [CHECK RULES 0-13]
  ├─ [APPLY TRANSFORMATIONS]
  ├─ [UPDATE GLOBAL STATE]
  └─ [CHECK TERMINATION]
  ↓
OUTPUT
```

### 3.3 Cycle Timing

Transformation rules fire based on **line_number mod timing_interval**:

| Rule | Trigger | Interval | Action |
|------|---------|----------|--------|
| 0 | Exact repeat detected | N/A | Soft reset + echo |
| 1 | line_num % 5 == 0 | 5-step | Expand structure |
| 2 | line_num % 13 == 0 | 13-step | Mutate symbol |
| 3 | line_num % 20 == 0 | 20-step | Duplicate block |
| 4 | line_num % 33 == 0 | 33-step | Offset duplicate |
| 8 | line_num % 21 == 0 | 21-step | Harmonic collapse |
| 13 | "13" in str(line_num) | N/A | Freeze all rules |

---

## 4. Rules Reference

### Rule 0 — Shadow of Origin (Hidden Rule)
**Trigger:** Detects exact symbol repeat across consecutive cycles

**Action:**
```
IF last_symbol == current_symbol THEN
  [1] Soft reset: Clear mutations from last cycle
  [2] Echo: Output previous symbol with ~echo flag
  [3] Advance rotation counter
```

**Example:**
```
Before: A → A (Rule 0 fires)
After:  A~echo (rotation_count++)
```

---

### Rule 1 — Structural Growth (5-step)
**Trigger:** `line_number % 5 == 0`

**Action:** Insert structure marker at end of symbol
```
Symbol → Symbol + "→"
```

**Example:**
```
Before: A B C D E
After:  A B C D E→   (line 5 triggers)
```

---

### Rule 2 — Mutation (13-step)
**Trigger:** `line_number % 13 == 0`

**Action:** Rotate symbol to next in circular sequence
```
Symbol[i] → Symbol[(i+1) % charset_length]
```

**Example:**
```
Before: A B C D E F G H I J K L M   (13 symbols)
After:  B C D E F G H I J K L M N   (line 13 triggers, A→B, etc.)
```

---

### Rule 3 — Duplication (20-step)
**Trigger:** `line_number % 20 == 0`

**Action:** Append previous block as duplicate
```
Block[n-1] → Block[n] (append copy)
```

**Example:**
```
Line 19: X Y Z
Line 20: X Y Z X Y Z   (line 20 triggers)
```

---

### Rule 4 — Offset Duplication (33-step)
**Trigger:** `line_number % 33 == 0`

**Action:** Append previous block with symbol offset
```
Block[n-1] rotated → Block[n] (append rotated copy)
```

---

### Rule 8 — Harmonic Collapse (21-step)
**Trigger:** `line_number % 21 == 0`

**Action:** Merge last 3 lines into 1
```
[Line A, Line B, Line C] → Merged(A, B, C)

Merge function: Concatenate + apply harmonic reduction
```

---

### Rule 13 — Seal of Thirteen (Master Rule)
**Trigger:** String representation of `line_number` contains digit "13"

**Action:** Freeze all other rules until reset
```
IF "13" in str(line_number) THEN
  rule_13_frozen = TRUE
  [All other rules disabled until next major cycle]
```

**Example:**
```
Line 13:  Rules frozen
Line 113: Rules frozen
Line 130: Rules frozen
Line 213: Rules frozen
```

---

## 5. Syntax & Semantics

### 5.1 Program Syntax

```
Program     ::= Statement+
Statement   ::= Symbol (Metadata)?
Symbol      ::= CHAR | EXTENDED_UNICODE
Metadata    ::= "@" Flags
Flags       ::= Flag ("," Flag)*
Flag        ::= "sealed" | "mutable" | "locked"
```

### 5.2 Example Program

```
# FractoScript-13 Example: Transformation Sequence
A
B
C
D
E @sealed
F
G
H
I
J
K
L
M @mutable
N
O
P
Q
R
S
T
U
```

### 5.3 Comments

Lines starting with `#` are comments:
```
# This is a comment
A  # Inline comments not supported
```

---

## 6. Output Format

### 6.1 Standard Output

```
[CYCLE 0]
State: A B C D E F G H I J K L M N O P Q R S T U
Transformations: none

[CYCLE 1]
State: A B C D E→ F G H I J K L M N O P Q R S T U
Transformations: Rule 1 (line 5)

[CYCLE 2]
State: B C D E→ F G H I J K L M N→ O P Q R S T U
Transformations: Rule 2 (line 13)
```

### 6.2 Verbose Output

```
[CYCLE 0, LINE 5]
  Rule 1 Triggered (Structural Growth)
  Before: E
  After:  E→
  Flags:  [growth]

[CYCLE 0, LINE 13]
  Rule 2 Triggered (Mutation)
  Before: M
  After:  N
  Flags:  [mutated]
```

---

## 7. Execution Parameters

### 7.1 Configuration

```yaml
max_cycles: 100          # Maximum transformation cycles
max_depth: 50            # Maximum recursion depth
symbol_set: "A-Z0-9"     # Available symbols
enable_rule_0: true      # Enable shadow detection
enable_rule_13: true     # Enable sealing
harmony_threshold: 3     # Lines to collapse in Rule 8
```

### 7.2 Termination Conditions

An FS-13 program terminates when:

1. **Max cycles reached:** `cycle_count >= max_cycles`
2. **All rules sealed:** `rule_13_frozen && all_other_rules_frozen`
3. **Fixed point:** State doesn't change across cycle
4. **Rupture handled:** Forbidden state encountered and resolved

---

## 8. Error Handling

### 8.1 Rupture States

A **rupture** occurs when conflicting rules trigger simultaneously:

```
IF Rule_X && Rule_Y fire at same line AND incompatible THEN
  [1] Log rupture event
  [2] Apply rupture_priority_order
  [3] Mark line with ~ruptured flag
```

### 8.2 Forbidden States

Certain state combinations are forbidden:

```
FORBIDDEN:
  - symbol cannot be both "sealed" AND "mutated"
  - recursion_depth > max_depth
  - cyclic duplication (self-reference)
```

---

## 9. Semantics & Examples

### 9.1 Simple Transformation

**Input (5 lines):**
```
A
B
C
D
E
```

**Cycle 0:**
- Line 5: Rule 1 triggers → E becomes E→

**Output:**
```
A
B
C
D
E→
```

---

### 9.2 Complex Transformation (20 lines)

**Input:**
```
A B C D E F G H I J K L M N O P Q R S T
```

**Cycles 0-2:**
```
Cycle 0, Line 5:  Rule 1 (E→E→)
Cycle 0, Line 13: Rule 2 (M→N)
Cycle 1, Line 20: Rule 3 (duplicate block)
Result: A B C D E→ F G H I J K L N O P Q R S T→ A B C D E→ F G H I J K L N O P Q R S T→
```

---

## 10. Implementation Guidelines

### 10.1 Recommended Architecture

```
fs13_interpreter.py
├── lexer.py          # Tokenize input
├── parser.py         # Build AST
├── state_manager.py  # Manage execution state
├── rules.py          # Rule definitions
├── executor.py       # Main execution loop
└── output.py         # Format output
```

### 10.2 Key Interfaces

```python
class FSExecutor:
    def __init__(self, max_cycles=100, config=None)
    def parse(self, program_text: str) -> List[State]
    def execute(self) -> ExecutionResult
    def get_state(self, cycle: int) -> GlobalState
    def render_output(self, format: str) -> str
```

---

## 11. Reserved Keywords

```
@sealed      # Mark as unchangeable
@mutable     # Mark as mutable
@locked      # Prevent duplication
#            # Comment
```

---

## 12. Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.0.0 | 2026-06-12 | Initial specification |

---

## 13. Future Extensions

- [ ] Recursion support (nested programs)
- [ ] Function definitions (reusable blocks)
- [ ] Conditional rules (IF/THEN syntax)
- [ ] Symbol arithmetic (addition, modulo operations)
- [ ] Network mode (distributed transformation)

---

**Language Author:** Octoous72  
**Repository:** https://github.com/Octoous72/FractoScript-13  
**Status:** Specification Complete, Implementation In Progress
