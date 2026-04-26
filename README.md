# 🃏 Estimation Game – Scoring Rules

This document defines the complete and deterministic scoring system for the Estimation card game variant.

The rules are designed to be:

* Unambiguous for human players
* Deterministic for software implementation
* Interpretable by AI/LLM systems without assumptions

---

# 🎯 Core Definitions

* **Bid (`bid`)**: Number of tricks a player declares they will win.
* **Actual (`actual`)**: Number of tricks the player actually wins.
* **Difference (`difference`)**:

```
difference = abs(actual - bid)
```

⚠️ Difference is always absolute and never signed.

* **Round**:
  A round consists of:

1. All players placing bids
2. Playing all tricks
3. Calculating scores

* **Bola**:
  A complete game cycle consisting of **13 base rounds**

---

# 🔢 Game Structure

* Each **Bola starts with 13 base rounds**
* Rounds are played sequentially
* Special mechanics (e.g., color change) may alter order and extend total rounds

---

## Base Round Order

The 13 base rounds consist of:

* First 8 rounds: Standard rounds
* Last 5 rounds (fixed order):

  1. Suns
  2. Spade
  3. Heart
  4. Karo
  5. Trifle

---

# ✅ Win / Loss Condition

```
WIN  ⇢ actual == bid  
LOSS ⇢ actual != bid
```

---

# 🧩 Player Independence Rule

Each player is scored independently based on their own bid type:

* Call / With
* No Call
* Dash Call
* Special Call (bid ≥ 8)

Different players in the same round may use different bid types.

The only shared (global) condition is:

* Under / Over (based on total bids)

---

# 🟥 Core Scoring Modes

## 1. Call / With

* "With" is identical to "Call" and has no additional effects
* Minimum Call value is **4**

⚠️ A player **cannot Call with bid < 4**
(If bid < 4 → it must be treated as No Call)

### Win:

```
score = 13 + 10 + bid
```

### Loss:

```
score = -(10 + difference)
```

---

## 2. No Call

### Win:

```
score = 13 + bid
```

### Loss:

```
score = -difference
```

---

# 🟩 Bid = 0 Behavior

When a player bids `0`, there are two possible interpretations:

### 1. Normal Bid (No Declaration)

* Treated as **No Call with bid = 0**
* Scored using **No Call rules**

### 2. Dash Call (Explicit Declaration)

* Player explicitly declares a **Dash Call**
* Uses **Dash Call scoring rules**

⚠️ Important:

* A bid of `0` is NOT automatically a Dash Call
* Dash Call must be explicitly declared

---

# 🟦 Table Rule

```
Under ⇢ total bids < 13  
Over  ⇢ total bids > 13  
```

⚠️ Total bids must **never equal 13**

⚠️ Under / Over is a global condition and affects all players, but only modifies Dash Call scoring.

---

# 🟩 Dash Call (explicit bid = 0)

### Under:

* Win → `33`
* Loss → `-(20 + difference)`

### Over:

* Win → `23`
* Loss → `-(10 + difference)`

---

# 🟨 Special Calls (High Bids)

## Applicability

* Applies when:

```
bid ≥ 8
```

* Special Calls are treated as **Call-type bids**
* They **override normal Call scoring**
* Special Calls and Dash Call are **mutually exclusive**

---

## Win Score

```
score = bid²
```

---

## Loss Penalty

```
score = -(10 × (bid - 5))
```

---

## Reference Table

| Bid | Win | Loss |
| --- | --- | ---- |
| 8   | 64  | 25   |
| 9   | 81  | 35   |
| 10  | 100 | 45   |
| 11  | 121 | 55   |
| 12  | 144 | 65   |
| 13  | 169 | 75   |

---

# 🟣 Sa3ayda Rule

## Trigger

Occurs when:

```
No player wins the round
```

## Effect

* Current round:

  * No player receives any score
  * Round is counted (not replayed)
  * No bonuses are applied

* Next round:

```
All scores ×2
```

## Constraints

* Applies to **the immediate next round only**
* Does **not stack**
* After the next round, scoring returns to normal

---

# 🔵 Bonus Rules

* Only winner in a round → `+10`
* Only loser in a round → `-10`

## Constraints

* Bonus applies only if **exactly one player** satisfies the condition
* If multiple players win or lose → **no bonus is applied**

---

# 🔁 Color Change (Late Game Mechanic)

## Trigger

* Occurs in the last 5 rounds of the Bola
* A player makes a **Call with bid ≥ 8**

## Effect

* A new round is immediately created using the newly chosen color/order
* The original scheduled round is not played at that moment
* The original round is moved to the end of the Bola

## Result

* Total rounds become **greater than 13**
* The Bola dynamically extends
* The inserted round is played immediately
* The original round is deferred to the end

## Note

* Affects **game flow only**
* Does **not affect scoring calculations**
* Does **not affect Sa3ayda or bonus rules**

---

# ⚙️ Execution Order (Mandatory)

All scoring must follow this exact sequence:

```
1. Determine WIN / LOSS
2. If bid ≥ 8 → apply Special Call
3. Else if bid = 0 AND Dash Call declared → apply Dash Call
4. Else:
      - Apply Call / With (only if bid ≥ 4)
      - Otherwise apply No Call
5. Apply Bonus (+10 / -10)
6. Apply Sa3ayda multiplier (if active)
```

⚠️ This execution order overrides all other interpretations.

---

# ⚠️ Additional Rules & Constraints

* Each Bola starts with exactly **13 base rounds**
* Total bids across all players must never equal `13`
* Minimum Call bid is `4`
* Bid `0`:

  * Without declaration → No Call
  * With declaration → Dash Call
* Special Calls apply only when `bid ≥ 8`
* Dash Call applies only when explicitly declared
* Special Calls and Dash Call are mutually exclusive
* All scoring formulas are deterministic and must not be overridden

---

# 🧠 Design Characteristics

* Exact prediction gameplay (no partial wins)
* Increasing risk/reward with higher bids
* Table-dependent dynamics via Under / Over
* Momentum shift via Sa3ayda
* Strategic depth via Dash Call and Special Calls

---

# ✅ Status

This rule set is:

* Fully defined
* Ambiguity-free
* Implementation-ready
* Suitable for backend systems, AI agents, and competitive play
