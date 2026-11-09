# Kleos — Design Document

> *Kleos* (κλέος): Greek for "glory that outlives death". A single-player idle game
> set in Greek myth, with the mood of the Underworld and a spectral green palette.

This document records every decision made so far, so that nothing is silently assumed.
Items marked **(deferred)** are intentionally not decided yet.

---

## 1. Goals

1. **Learn Java** (the author starts with none; background is Symfony and Nuxt).
2. **Build a portfolio piece** a recruiter can read and run.
3. Make a game that is fun to tune, in the spirit of an idle/incremental game.

Non-goals: money, multiplayer, accounts, leaderboards (all out of scope for v1).

## 2. Working agreement

- **The author writes all the code.** Claude acts as director: it hands out one small
  milestone at a time, reviews code, and says bluntly what is wrong.
- The author has the final word on every design decision.
- Documents like this one are the exception: they may be written by Claude.

## 3. Game concept

An **idle game** where a character (not a team) progresses through zones on its own,
even while the player is away. Time is the main resource: the player can eventually
unlock everything, and the game is tuned so that every obstacle is beatable given
enough time.

### 3.1 Theme and legal stance

- Inspiration: Hades and Hades II (mood, history, lore, trivia), **not their gameplay or assets**.
- Greek mythology is public domain and is the content source (gods, places, Melinoë as
  the myth figure from the Orphic Hymns).
- **No Supergiant assets, designs or text.** Their Terms of Use (§3.5, §7) forbid
  derivative works of their characters and content without written consent. This is a
  risk assessment, not legal advice.
- Art direction: original art, **Greek black-figure/red-figure pottery** influence,
  spectral green and black palette. Placeholder shapes until the engine works.
- No dialogue system in v1. Lore appears as names, flavor text and narrated combat log,
  written in the author's own words.

### 3.2 Progression structure

- **One path in v1: the Underworld**, as a linear sequence of zones.
  Olympus (going up) is a possible later second path.
- No death and no runs. When a boss is too strong, the player **falls back and farms**
  an earlier zone until stronger, then returns.
- Two kinds of walls:
  - **Mid-bosses**: DPS checks.
  - **Zone bosses**: need preparation (see 3.4).
- A **finite campaign** ending at a final boss, followed by **prestige** (see 3.6).

### 3.3 Build system: god talent trees

- Each **god** has a talent tree. Spending points unlocks powers and spells themed
  on that god (Hades' status effects are the model: Doom, Chill, Weak, Hangover, Charm,
  chain lightning, and so on).
- **v1 scope: 3 gods** with deep trees, mechanically different from each other
  (suggested: Ares / Doom, Demeter / Chill, Zeus / chain lightning). More gods are
  added later through data only.
- The goal is to **unlock every node**. This is a completionist progression, not a
  "best build" game. Tuning checks feasibility, not optimality.
- **Decision nodes**: at certain points the tree offers a choice (for example
  *boss damage* vs *speed*). These are **swappable loadout slots**: both options are
  unlocked, only one is active at a time, and the player can switch at any moment
  (small cost or cooldown **(deferred)**).
- Items per god and duo/legendary boons are **(deferred)**.
- Counter-design (zones and bosses with resistances, weaknesses and immunities) is
  wanted but needs polish **(deferred)**.

### 3.4 "Attention" means preparation

There is **no real-time input**. A boss has phases and mechanics (for example
"needs a lot of speed in phase 2"), and the player answers them by **choosing the
loadout beforehand**. The fight itself is resolved by simulation.

Players also do: gather rewards, complete missions, claim quests.

### 3.5 Quests

Goal-based only: reach a zone, beat a boss, unlock a node, and so on, with currency or
item rewards that the player claims. Defined in data. No real-time dailies or timed
missions in v1.

### 3.6 Prestige (Chronos)

- Prestige is part of **v1** and is built **last**, after the feasibility simulator and
  the first proof of concept work.
- Theme: **Chronos**, Titan of Time ("rewind time", reset the campaign, keep permanent
  bonuses).
- What resets, what persists, and the Chronos multiplier are decided later,
  once real numbers exist **(deferred)**.
- The v1 data model must not make a reset impossible.

## 4. Simulation model

Deterministic, with **no background process**: the game state is a pure function of
saved state plus elapsed time, so offline progress is computed when the player returns.

**Hybrid event-by-event:**

- Fights are resolved **analytically**: time-to-kill comes from damage, status and phase
  formulas. The clock jumps from event to event (a kill, an affordable purchase),
  not tick by tick.
- **Casual zones and mid-bosses** that the current loadout can beat are simulated
  automatically while the player is away.
- **Boss fights while away:** the engine first predicts whether the boss is killable
  with the *current* loadout. If yes, it resolves the fight like any other event. If
  not, the offline simulation **stops there**: the player must return and change their
  loadout.
- The same prediction function must be used online and offline.
- Randomness: expected values, or a seeded generator **(deferred)**.
- Offline time cap **(deferred)**.

## 5. Data and tuning

- All content (gods, nodes, enemies, zones, bosses, quests) lives in **YAML files**,
  not in code.
- A **separate tuning file** holds only curve parameters (base value, growth, exponent).
- A **headless balance simulator** runs many full playthroughs and:
  - accepts overrides such as `--set tartarus.hp.growth=1.15` and parameter sweeps,
  - exports **CSV** for spreadsheet analysis,
  - produces a **feasibility report**: every wall is beatable within the time cap,
    even with a poor unlock order.

## 6. Pacing target

**About 3 weeks** of casual play to unlock everything. All curves are tuned against this
number and the simulator checks it. An **endgame** exists beyond that: prestige.

## 7. Technology

| Area | Choice |
|---|---|
| Language | Java 25 LTS |
| Build | Maven |
| Tests | JUnit 5, written alongside the code |
| IDE | IntelliJ IDEA |
| Data | YAML (Jackson) |
| Backend (later) | Spring Boot REST API, database **(deferred)** |
| Frontend (later) | Nuxt / Vue |
| Repo | Public on GitHub from day one, English README |

Order of work: the pure-Java engine first (no framework, no database, no UI), then the
API, then the frontend.

## 8. Open questions

- Swap cost or cooldown for decision nodes.
- Counter-design details (resistances, immunities).
- Randomness model.
- Offline cap.
- Prestige details.
- Persistence choice for the Spring phase.
- Chronos as a twist beyond prestige.
- Items, duo and legendary boons, a second path (Olympus).
