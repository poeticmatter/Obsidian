# GRID — Game Design Document

> **Purpose:** This document describes the current implemented rules of GRID as a reference for design discussion. It is derived from the source code and is the authoritative description of how the game behaves. Update this document whenever a mechanic changes.

---

## Overview

GRID is a single-player cyberpunk hacking game. The player breaches a corporate network by selecting and executing contiguous groups of data cells on a 5×5 grid. The network is defended by ICE nodes that punish mistakes with countermeasures. The objective is to locate and hack a SERVER node before being traced or having hardware destroyed.

---

## The Grid

The play area is a **5×5 grid of cells**. Each cell has four properties:

| Property | Values |
|---|---|
| **Color** | `ORANGE`, `SKY`, `EMERALD`, `LIME`, `FUCHSIA` |
| **Symbol** | `CUBE`, `TRIANGLE`, `NESTED`, `STAR`, `ORBIT` |
| **State** | `PRIMED`, `BROKEN`, `CORRUPTED` |
| **Virus flag** | present or absent |

- `PRIMED` cells are available for selection — this is the normal state of every cell.
- `BROKEN` cells have been consumed by an execution and cannot contribute to harvesting.
- `CORRUPTED` cells have been corrupted by a countermeasure; they display visual noise and contribute nothing when selected.
- The **Virus** flag is not a per-cell property. See Player Resources for the global Virus Counter.

The grid is generated fresh at game start with a balanced distribution of colors and symbols (exactly 5 of each across the 25 cells).

---

## Player Resources

The player has three tracked resources:

### Hardware Health
- Represents the physical integrity of the player's rig.
- Starts at **3** (max 3).
- Reduced by `HARDWARE_DAMAGE` countermeasures.
- Reaching **0** is an immediate **Game Over**.

### Trace
- Represents how closely corporate security is tracking the player.
- Maximum is **15** (default).
- Increased by `TRACE` countermeasures and the neglect penalty (see Neglect Penalty section).
- Reaching **maximum Trace** is an immediate **Game Over**.

### Virus Counter
- A global tally of accumulated viral infections.
- Incremented by `VIRUS` countermeasures.
- Displayed as a biohazard indicator on the board. Its gameplay effect is reserved for future mechanics.

---

## Execution

The core offensive action. The player **selects a contiguous group of cells** on the grid and clicks **EXECUTE PAYLOAD**.

### Selection rules
- The selection must be contiguous — all selected cells must form a connected region (orthogonal or diagonal adjacency counts for contiguity checking).
- `BROKEN` and `CORRUPTED` cells in the selection are silently skipped and contribute nothing.

### Harvesting
When a clean (virus-free) selection is executed:
- Each broken cell contributes its **colour** to the harvest tally.
- Each broken cell contributes its **symbol** to the harvest tally.

Harvested colours are applied simultaneously to all currently **ACTIVE** server nodes, advancing their layer progress and triggering countermeasures (see Network section).

---

## The Network

The player attacks a **flat pool** of nodes — there is no graph or topology. At any moment up to 3 ICE nodes are in the pool. Nodes have no parent-child relationships; the pool is self-replenishing.

### Node Types

| Type | Role |
|---|---|
| `ICE` | Defensive node. Must be cleared to advance the server search. Procedurally generated — 3 layers, each with a colour and suppression symbol. |
| `SERVER` | The target. Appears when the Server Search Progress bar fills. Hacking a SERVER wins the game. Can have global countermeasures. |

### Node Status

Each node has a **status** that controls whether it participates in gameplay:

| Status | Meaning |
|---|---|
| `INACTIVE` | Revealed but not yet accessed. Does not receive harvest, fire countermeasures, or generate neglect penalty. |
| `ACTIVE` | Fully engaged. Receives harvest progress, may fire countermeasures, and generates a neglect penalty if ignored. |
| `HACKED` | Cleared. Removed from the active pool; replaced by a new node. |

All nodes spawn as **INACTIVE**. The player must click the **ACCESS NODE** button on a node card to transition it to `ACTIVE`. This is a deliberate strategic choice — activating more nodes increases exposure to the neglect penalty.

### Node Properties

Each node has:
- **Layers** — the colour requirements the player must fill by harvesting (see per-type details below).
- **Progress** — which lanes are completed. A node is `HACKED` when all layers are filled.
- **Countermeasures** — defences that fire under specific conditions during an execution.
- **Global Countermeasures** (`SERVER` only) — defences that fire unconditionally on System Reset.

#### ICE layers

ICE nodes are **procedurally generated**. Each ICE has exactly **3 layers** and **one countermeasure**.

Each layer has a **colour** — the player must harvest at least one cell of this colour to break the layer. All three colours are distinct.

The countermeasure carries **3 required symbols** (all distinct). Whenever the ICE makes progress (any layer broken this turn), the countermeasure fires **once per required symbol that was not harvested** in that execution. Harvesting all 3 symbols fully suppresses it; harvesting none fires it 3 times.

#### SERVER layers

`SERVER` nodes use a lane-based format: each colour maps to an ordered list of numeric requirements (e.g. `ORANGE: [2, 3]` means two lanes — one needing 2 orange cells, one needing 3). Surplus harvested colour carries over to the next unfilled lane within the same colour.

### Node Activation & Replacement

When a node is **hacked**:
- It is removed from the active pool.
- If it was an ICE node: a replacement ICE (or SERVER if search bar fills) spawns as **INACTIVE** — the player must ACCESS NODE it to bring it into play.
- If it was a SERVER node: the game ends in **VICTORY**.

### Server Search Progress

The **Server Search Bar** tracks how close the player is to locating the hidden SERVER.

- Each ICE node hacked advances the bar by **1** (constant `ICE_SEARCH_REWARD`).
- The bar has a maximum of **5** (`maxServerSearchProgress`).
- When the bar fills (progress ≥ maximum), a **SERVER** node spawns as the replacement (INACTIVE) and the bar resets to 0.
- Until the bar fills, hacked ICE nodes are replaced with new procedurally generated ICE nodes.

This means the player must clear a minimum of **5 ICE nodes** before a SERVER appears. The SERVER then occupies one of the pool slots alongside any remaining ICE.

### Layer Filling

When an execution resolves, harvested colours are applied to all currently **ACTIVE** nodes simultaneously.

- **ICE:** each layer is broken if at least one cell of its colour was harvested this turn. All three layers must be broken for the ICE to be `HACKED`.
- **SERVER:** colour progress fills lanes sequentially. Surplus harvested colour carries over to the next unfilled lane within the same colour. Colour harvested beyond a node's requirements is wasted.

A node becomes `HACKED` when all its layers are fully complete.

---

## Active Node Neglect Penalty

After every execution, each `ACTIVE` node that received **no layer progress** this turn adds **1 TRACE** to the player.

This creates a meaningful tension around the ACCESS NODE decision: activating a node you cannot meaningfully progress this turn costs you trace. Players should only activate nodes they are ready to attack.

---

## Countermeasures

Countermeasures are defensive penalties on nodes. There are two kinds.

### Local Countermeasures (triggered during execution)

Each node has one or more countermeasures, each with a `type` and `value`. Activation rules differ by node type.

**ICE (symbol-count suppression):**

Each ICE has exactly one countermeasure with 3 required symbols. Whenever an execution breaks at least one ICE layer (makes progress), the countermeasure fires **once per required symbol that was not harvested** in that execution — up to 3 times if none are harvested, 0 times if all 3 are present.

**SERVER (symbol-tally suppression):**

Each countermeasure carries a `requiredSymbols[]` list. It fires if, and only if, **both** of the following are true:
1. The execution **advanced the node's progress** (at least one lane ticked forward).
2. The player harvested **fewer** of one or more `requiredSymbols` than required.

If `requiredSymbols` is empty, the countermeasure never fires via this path.

### Global Countermeasures (triggered on System Reset)

Attached exclusively to `SERVER` nodes. They have the same `type`/`value` structure as local countermeasures but **no `requiredSymbols`** — they always fire when System Reset is confirmed. They fire across the entire node dictionary, not just active nodes, but **not on nodes that have already been hacked**.

### Countermeasure Types

| Type | Effect |
|---|---|
| `TRACE` | Adds `value` to the player's Trace. |
| `HARDWARE_DAMAGE` | Subtracts `value` from Hardware Health. |
| `NET_DAMAGE` | **Currently bypassed** — no effect. Reserved for a future hand/discard mechanic. |
| `VIRUS` | Adds `value` to the player's global Virus Counter. |
| `CORRUPT` | Corrupts `value` random `PRIMED` cells, setting their state to `CORRUPTED`. Corrupted cells cannot contribute to harvesting. |
| `NOISE` | Adds a new lane requirement to a random active node, increasing its hacking cost. |

---

## System Reset

System Reset is implemented in the engine and will wipe the grid and fire all active-node countermeasures + global SERVER countermeasures. It does not currently have a UI trigger — this mechanic is in progress.

---

## Win & Loss Conditions

### Victory
Hack a **SERVER** node. The game transitions to `VICTORY`.

### Game Over — any of the following:
| Condition | Trigger |
|---|---|
| **Hardware destroyed** | Hardware Health reaches 0 |
| **Fully traced** | Trace reaches its maximum (15) |

---

## Game Flow (Phases)

```
MENU
  └─► PLAYING          Player selects cells and executes them
        │               Server nodes make progress; countermeasures fire;
        │               neglect penalty applied; win/loss evaluated
        └─► VICTORY          SERVER hacked
        └─► GAME_OVER        Any loss condition met
```

Between executions the player can click **ACCESS NODE** on any INACTIVE node to activate it, choosing when to expose themselves to the neglect penalty.

---

## Design Notes & Open Questions

The following are areas with intentional design space or known loose ends as of this document version.

- **NET_DAMAGE** is architecturally present but its effect is bypassed with a console warning. A future hand/discard mechanic would give it meaning.
- **System Reset** is implemented in the engine (`SYSTEM_RESET` + `RESOLVE_SYSTEM_RESET` actions) but has no UI trigger yet. When wired up, it will wipe the grid and fire all active-node countermeasures and global SERVER countermeasures.
- **ICE suppression** is symbol-count based: the countermeasure fires once per missing required symbol whenever the ICE makes progress. Players can partially suppress by harvesting some but not all required symbols.
- **Server Search fill rate:** Currently a flat 1 per ICE cleared (`ICE_SEARCH_REWARD`). This could be tuned per-node via difficulty or a dedicated reward field.
- **Pool size:** The active pool starts with 3 ICE nodes. This is a constant in `initializeGameHandler.ts` (`INITIAL_ICE_COUNT`).
- **Replacement nodes spawn INACTIVE.** The player must choose when to ACCESS NODE a replacement. This creates a pacing mechanic — delaying activation means fewer nodes generating neglect trace, but also less server progress.
