FractoScript‑13 (FS‑13) is a symbolic transformation engine built on:

- 13 explicit rules  
- 1 hidden rule (Rule 0)  
- Multi‑cycle timing (5, 13, 20, 21, 33, 132, 165)  
- Deterministic symbolic evolution  
- Self‑recognition and soft resets  
- Identity sealing  
- Rupture handling  

FS‑13 is designed for:

- Researchers  
- Theorists  
- Educators  
- Generative media developers  
- Computational modelers  

---

🧠 Core Concepts

FS‑13 operates on a sequence of lines:

`
L1, L2, L3, … Ln
`

Each line contains:

- A symbol  
- Metadata flags  
- A global state snapshot  

Rules fire based on:

- Line index  
- Rotation count  
- Symbolic conditions  
- Structural events  
- Recursion depth  
- Forbidden rupture states  

---

📜 Rulebook

Hidden Rule 0 — Shadow of Origin
If a line exactly matches a previous line:  
- Freeze all rules  
- Treat next line as virtual Line 1  
- Apply Rule 1 + Rule 10  
- Resume normal execution  

---

Rule 1 — Structural Growth (5‑step)
Every 5th line: apply growth transform.

Rule 1B — Rotational Multiplication (5‑rotation)
Every 5th rotation: multiply + append.

Rule 2 — Mutation (13‑step)
Every 13th line: mutate symbol.

Rule 3 — Duplication (20‑step)
Every 20th line: duplicate previous block.

Rule 4 — Offset Duplication (33‑step)
Every 33rd line: duplicate with offset.

Rule 5 — Chapter Marker (132‑step)
Every 132nd line: insert chapter marker.

Rule 6 — Offspring Generation (165‑step)
Every 165th line: spawn lineage branch.

Rule 7 — Overlap Priority
Smallest cycle wins.

Rule 8 — Harmonic Collapse (21‑step)
Merge last 3 lines into 1.

Rule 9 — Mutation–Duplication Inversion
Invert mutation before duplication.

Rule 10 — Chapter Echo
Repeat Line 1 at chapter boundaries.

Rule 11 — Recursion Overflow Reset
Reset recursion depth > 13.

Rule 12 — Forbidden Rupture
Freeze rules + output rupture marker.

Rule 13 — Seal of Thirteen
If line number contains “13”:  
- Freeze all rules  
- Output pure symbol  

---

⚙️ Execution Model

`
1. Evaluate Rule 0
2. Evaluate Rule 13
3. Evaluate Rule 12
4. Apply overlap priority (Rule 7)
5. Fire the selected rule
6. Append new line
7. Update global state
`

---

🖼️ ASCII Diagrams

System Architecture

`
+---------------------------+
|     Input Line Buffer     |
+-------------+-------------+
              |
              v
+---------------------------+
|       Rule Engine         |
|  (Rules 0–13 Evaluation)  |
+-------------+-------------+
              |
              v
+---------------------------+
|     Global State Store    |
+-------------+-------------+
              |
              v
+---------------------------+
|     Output Line Buffer    |
+---------------------------+
`

---

Rule Priority Flow

`
          +------------------+
          |   New Line n     |
          +---------+--------+
                    |
                    v
        +------------------------+
        |  Rule 0? (repeat)      |
        +-----------+------------+
                    | yes
                    v
          [Apply Rule 0 only]
                    |
                    v
                Next line
                    |
                    v
        +------------------------+
        |  Rule 13? (contains 13)|
        +-----------+------------+
                    | yes
                    v
          [Seal line, freeze all]
                    |
                    v
                Next line
                    |
                    v
        +------------------------+
        |  Rule 12? (rupture)    |
        +-----------+------------+
                    | yes
                    v
          [Output rupture marker]
                    |
                    v
                Next line
                    |
                    v
        +------------------------+
        |  Overlap Priority      |
        +-----------+------------+
                    |
                    v
        [Apply smallest-cycle rule]
`

---
