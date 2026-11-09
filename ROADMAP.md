# Kleos — Roadmap

Each milestone is small enough to finish in one sitting or two. "Done" means the
criteria below are met **and** the author can explain every line to a stranger.
No deadlines; the author sets the pace.

Rule for all milestones: **tests are written alongside the code** (JUnit 5).

---

## M0 — Setup
- Install Java 25 LTS, Maven and IntelliJ IDEA.
- Create the Maven project (`com.<you>.kleos`), run an empty test.
- Write the README (English): what Kleos is, in three lines.
- **Done when:** `mvn test` passes and the repo is pushed.

## M1 — Java fundamentals through a console prototype
- Learn: classes, records, interfaces, enums, collections, `Optional`, streams, exceptions.
- Build a throwaway console program: a hero with HP and damage fights one enemy.
- **Done when:** the fight prints a result and you can explain each class you wrote.

## M2 — The tested engine
- Stats, a first status effect (Doom), and one analytic fight: *time to kill* computed
  from formulas, not a loop.
- No framework, no files, no UI.
- **Done when:** unit tests cover normal and edge cases (zero damage, immunity).

## M3 — Content from YAML
- Load gods, nodes and enemies from YAML with Jackson.
- Keep a separate tuning file for curve parameters.
- **Done when:** changing a number in YAML changes the result, with no recompile.

## M4 — The event-driven progression simulator
- Advance the game event by event: kills, affordable purchases, zone changes.
- Implement the boss-prediction rule: stop offline progress at a boss that the current
  loadout cannot beat.
- **Done when:** simulating "3 weeks" runs in well under a second and is reproducible.

> *Optional after M4:* a throwaway page or CLI view to see the game working.

## M5 — Balance simulator and feasibility report
- Headless runs with `--set` overrides and parameter sweeps, CSV export.
- Report: time to beat every wall, including a deliberately poor unlock order.
- **Done when:** the report proves every wall is beatable under the time cap, and fails
  loudly when you break a curve.

## M6 — Prestige (Chronos)
- Design and implement the first reset layer, using real numbers from M5.
- **Done when:** the simulator covers a full cycle and the feasibility report still holds.

## M7 — Spring Boot API
- Wrap the engine in REST endpoints: state, loadout swap, claim reward, offline catch-up.
- Choose persistence now, not earlier.
- **Done when:** the API is covered by tests and the engine has no dependency on Spring.

## M8 — Nuxt frontend
- Zone view, talent tree, loadout swap, quests, rewards, offline summary.
- Placeholder art first, original Greek-pottery-style art later.
- **Done when:** the game is playable end to end from the browser.

## Later
- Counter-design (resistances, immunities), items, duo/legendary boons, Olympus path,
  Chronos twists, real art, accounts and multiplayer.
