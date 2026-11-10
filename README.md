# 🎮 Pokémon Ga-Ole Console Edition

> A turn-based, console-based simulation of the *Pokémon Ga-Ole* arcade game, written in plain Java.

---

## 📌 Project Overview
This project brings the strategic mechanics of the *Pokémon Ga-Ole* arcade game to the command line. Players build a team, enter turn-based battles featuring speed-ordered turns and quick-time dodge events, catch defeated Pokémon using shuffled Poké Balls, and compete for top spots on a persistent leaderboard.

---

## 🚀 Running the Game

* **Requirements:** JDK 17+
* **Execution:** Run commands from the project root directory, as data files are read directly from `src/data/`.

```bash
# Compile all source files into an 'out' directory
javac -d out src/*.java

# Run the application
java -cp out Main
```

---

## 🕹️ How It Plays

1. **New Game / Load Game:** Start fresh with three classic starters (*Bulbasaur, Charmander, Squirtle*) or load an existing player save from disk.
2. **Spawn & Encounter:** Launch a battle to spawn three hidden wild Pokémon "in the grass." Pick one blind to join your team for free.
3. **Turn-Based Combat:** The remaining two wild Pokémon challenge you. Choose two of your team members, and execute moves ordered by speed.
4. **Quick-Time Dodge Events:** When a wild Pokémon attacks, you have **5 seconds** to input a dodge direction (`l` or `r`). Timed-out prompts count as failed dodges.
5. **Blind Catch Phase:** Defeat the opponents and throw a Poké Ball at the survivors. Balls are shuffled beforehand so your choice is entirely blind.
6. **Scoring & Leaderboard:** Score is calculated as `10000 − 75 × turns` (floored at 500), plus `1000 points` per catch. Top 5 scores are saved to the persistent leaderboard.

---

## 🏗️ Architecture & Component Design

```text
Main ──► Game ──┬──► Player ──► Pokemon ◄── Fire / Water / Grass /
                │                            Electric / Flying / Ground
                ├──► Battle ──► Pokeball ◄── Normal / Great / Ultra / Master
                ├──► Leaderboard
                └──► DataManager ──► src/data/*.txt
```

* **`Main` & `Game`:** Entry point and central game loop handling menus, state flow, and user navigation.
* **`Battle`:** Encapsulates a single fight instance, managing turn orders, damage calculation, QTE dodging mechanics, catches, and score computation.
* **`Pokemon` & Subtypes:** Base class handling stats, with specialized element subclasses (`FirePokemon`, `WaterPokemon`, etc.) defining unique type matchups.
* **`Pokeball` & Subtypes:** Manages catch formulas and rates across `Normal`, `Great`, `Ultra`, and `Master` balls.
* **`Player` & `Leaderboard`:** Manages active player state, inventory collections, and top-score tracking.
* **`DataManager`:** Sole handler for all file input/output operations against `src/data/*.txt`.

---

## 💡 Key Design Decisions

* **Inheritance for Types:** Element subclasses extend `Pokemon` and define specific matchups. Base attacks query an effectiveness multiplier, avoiding complex type-checking `if` chains.
* **Copy, Don't Share:** `pokemon.txt` is loaded once into a master roster. Every active Pokémon in play is a cloned instance, ensuring combat damage never mutates template data.
* **Wild as State, Not Class:** A Pokémon is tracked as wild via an object field rather than a separate class, allowing smooth transitions into ownership mid-game.
* **Data-Driven Poké Balls:** The core catch formula lives once in `Pokeball`, with individual balls supplying name attributes and catch rates (e.g., a rate of `1.0` makes the Master Ball a guaranteed catch organically).
* **Separation of Concerns:** `Battle` operates independently of UI and file systems, keeping game rules modular.
* **Reference-Based Saves:** Save files store collection numbers rather than heavy object duplicates, keeping saves compact and allowing global rebalancing via `pokemon.txt`.
* **Timed Input Handling:** `Scanner.nextLine()` blocks indefinitely; the dodge prompt executes reads on an `ExecutorService` and awaits input using `Future.get(5, SECONDS)`.

---

## 📁 Project Layout

```text
src/
├── Main.java
├── Game.java
├── Battle.java
├── Pokemon.java 
├── FirePokemon.java
├── WaterPokemon.java
├── GrassPokemon.java
├── ElectricPokemon.java
├── FlyingPokemon.java
├── GroundPokemon.java
├── Pokeball.java 
├── NormalBall.java
├── GreatBall.java
├── UltraBall.java
├── MasterBall.java
├── Player.java
├── Leaderboard.java
├── DataManager.java
└── data/
    ├── pokemon.txt       # Master roster (40 Pokémon)
    ├── players.txt       # Saved player profiles
    └── leaderboard.txt   # Top 5 score records
```
