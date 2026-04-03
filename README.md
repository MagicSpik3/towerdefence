Here is a draft README tailored for an A-Level Computer Science NEA. The structure is heavily focused on maintaining a clean separation between the game’s underlying logic and its graphical representation—a crucial architectural choice that will allow both robust unit testing and a clean interface for the future AI agent to interact with. 

***

# Tower Defense AI Environment (NEA Project)

## Overview
This project is a simplified, highly deterministic Tower Defense game built specifically as a foundational environment for artificial intelligence development. Rather than focusing on complex graphical animations or physics simulations, the engine prioritizes a clean, accessible game state. The player (or eventually, the AI agent) can place his towers to optimize his defensive strategy against configurable waves of enemies. 

## Core Requirements & Mechanics

### 1. Graphical Display
* **Minimalist GUI:** The graphical interface is intentionally basic, prioritizing clarity over aesthetics. The game is represented as a 2D grid where entities are simple geometric shapes or basic sprites.
* **Decoupled Engine:** The visual representation is strictly separated from the game logic. The game can run "headless" (without graphics) to allow for rapid wave testing and future AI training.

### 2. Grid & Map Configuration
* **Center-Tile Placement:** To simplify distance calculations and spatial logic, all towers snap strictly to the exact center of a grid tile.
* **CSV Map Loading:** The environment is highly modular. A user can define his map layout using a standard `.csv` file, where specific characters represent path tiles, buildable terrain, and scenery.

### 3. Enemies (Bloons) & Waves
* **Extensible Enemy Types:** The initial release features a single basic Bloon type. The object-oriented design allows for easy inheritance to introduce new Bloon properties later.
* **Configurable Waves:** Wave data is read from configuration files (e.g., JSON or CSV), defining spawn rates, quantities, and types. 
* **Test Phases:** The wave manager supports micro-configurations, allowing a developer to spawn a single Bloon for isolated testing of targeting and state mechanics.

### 4. Towers & Combat Logic
Combat relies on instant-hit mechanics (hitscan) rather than projectile travel time, ensuring a deterministic state for AI evaluation.

* **Dart Monkey:**
    * **Range:** 2 tiles (radius).
    * **Damage/Pierce:** Destroys exactly 1 Bloon per action.
    * **Targeting:** Defaults to the Bloon furthest along the path within his range.
* **Cannon Tower:**
    * **Range:** 1 tile (radius).
    * **Damage/Pierce:** Area of Effect (AoE) attack. Destroys up to 8 Bloons simultaneously within his blast radius upon firing.

## Architecture: The AI-Ready Game State
To facilitate the eventual integration of an AI solver, the system maintains a rigidly structured, accessible **Game State**. The AI agent will interact with the game via an API-like interface:

* **State Observation:** At any tick, the engine can return a serialized snapshot of the environment, including:
    * Current money/lives.
    * Grid status (occupied/empty tiles).
    * Array of active Bloons (positions, health, distance traveled).
    * Array of active Towers (positions, cooldown status).
* **Action Execution:** The game accepts discrete inputs (e.g., `PlaceTower(type, x, y)`) and computes the resulting state. 

---

## Future Development & Fine-Tuning

Once the core logic and state engine are stable, the following features can be developed to increase the complexity of the "Territory" the AI must navigate:

### Phase 1: Expanding the Mechanics
* **Economy System:** Implement a currency system where popping Bloons grants money, constraining when and where the player can place his towers.
* **Upgrades:** Allow towers to be upgraded, changing their range, cooldown speed, or popping capacity. This forces the AI to decide between placing *more* towers or *better* towers.
* **Advanced Targeting Profiles:** Give towers configurable targeting priorities (e.g., "First", "Last", "Strongest").

### Phase 2: Engine & Data Optimization
* **Path Cache Optimization:** Instead of recalculating the path every tick, pre-compute the exact path coordinates during map load to reduce processing overhead during high-speed AI training.
* **Event Logging:** Implement a comprehensive logging system that records every state change, tower placement, and wave completion time. This data will be vital for analyzing the AI's learning curve.

### Phase 3: The AI Integration ('The Solve')
* **Heuristic Bot:** Begin with a simple rule-based algorithm (e.g., "place a Dart monkey at the first available bend") to validate the action interface.
* **Search Algorithms:** Implement tree-search algorithms (like Minimax or Monte Carlo Tree Search) utilizing the deterministic state to "look ahead" several ticks before placing a tower.
* **Fitness Evaluation:** Develop a scoring function for the AI based on surviving waves, minimizing money spent, or maximizing the distance Bloons are kept from the exit.