# Requirements Document

## Introduction

A browser-based recreation of the classic Chrome offline dinosaur game, built as a single HTML page using HTML5 Canvas, Tailwind CSS (via CDN), and Vanilla JavaScript. The player controls a dinosaur that runs automatically and must jump over cacti to survive. The game increases in speed over time, tracks the player's score, and allows the player to restart after a game over.

## Glossary

- **Game**: The overall Chrome Dino game application running in the browser.
- **Dino**: The player-controlled dinosaur character that runs continuously from left to right on the ground.
- **Cactus**: An obstacle sprite that spawns from the right side of the screen and moves left toward the Dino.
- **Ground**: The horizontal surface on which the Dino runs and Cacti travel.
- **Score**: A numerical value that increases over time as the player survives longer.
- **High_Score**: The highest Score achieved in the current browser session.
- **Game_Speed**: The velocity at which Cacti move across the screen (pixels per frame), which increases over time.
- **Canvas**: The HTML5 `<canvas>` element on which all game entities are rendered.
- **Collision**: The event that occurs when the Dino's bounding box overlaps with a Cactus's bounding box.
- **Jump**: The upward and downward arc motion of the Dino triggered by player input.
- **Game_Loop**: The repeating animation cycle that updates and renders all game entities on each frame.
- **Idle State**: The game state before the first input, where the game is waiting to start.
- **Playing State**: The game state where the Game_Loop is active and the player is in control.
- **Game Over State**: The game state entered after a Collision, where all movement is frozen.

---

## Requirements

### Requirement 1: Game Initialization and Display

**User Story:** As a player, I want to see the game canvas and initial state when I open the page, so that I know the game is ready to play.

#### Acceptance Criteria

1. THE Game SHALL render a Canvas element that spans the full width of the viewport and has a fixed height of 300px, styled using Tailwind CSS classes.
2. WHEN the page loads, THE Game SHALL display the Dino sprite positioned on the Ground line within the leftmost 10% of the Canvas width, with the Dino's bottom edge aligned to the Ground line.
3. WHILE the Game is in the Idle State, THE Game SHALL display a "Press Space / Tap to Start" instruction message centered on the Canvas.
4. WHEN the Game transitions out of the Idle State, THE Game SHALL remove the "Press Space / Tap to Start" instruction message from the Canvas.
5. WHILE the Game is in the Playing State, THE Game SHALL display the current Score and High_Score as text in the upper-right area of the Canvas, initialised to 0 before the first point is scored.

---

### Requirement 2: Player Input and Jump Mechanics

**User Story:** As a player, I want to press a key or tap the screen to make the dinosaur jump, so that I can avoid cacti.

#### Acceptance Criteria

1. WHEN the player presses the Space key or the ArrowUp key, THE Dino SHALL begin a Jump arc if the Dino is currently on the Ground.
2. WHEN the player taps the Canvas on a touch device, THE Dino SHALL begin a Jump arc if the Dino is currently on the Ground.
3. WHILE the Dino is airborne during a Jump, THE Dino SHALL NOT initiate a second Jump.
4. WHEN a Jump is initiated, THE Dino SHALL ascend with an initial upward velocity between 15 and 25 pixels per frame, then decelerate under a gravity constant between 0.6 and 1.2 pixels per frame squared, until the Dino returns to the Ground.
5. WHEN the Dino returns to the Ground after a Jump, THE Dino SHALL resume the running animation at Ground level.
6. THE maximum height reached by the Dino during a Jump SHALL be sufficient to clear the tallest Cactus obstacle at all valid Game_Speed values.

---

### Requirement 3: Obstacle Spawning and Movement

**User Story:** As a player, I want cacti to appear and move toward me, so that the game presents a continuous challenge.

#### Acceptance Criteria

1. WHEN the game is in the Playing State, THE Game SHALL spawn Cactus obstacles at random intervals between 500ms and 2000ms from the right edge of the Canvas.
2. WHILE the game is in the Playing State, EACH Cactus SHALL move from right to left across the Canvas at the current Game_Speed in pixels per frame.
3. WHEN a Cactus moves beyond the left edge of the Canvas, THE Game SHALL remove that Cactus from the active obstacle set.
4. WHEN a Cactus is spawned, THE Game SHALL select its type (single, double, or triple cactus cluster) with equal probability (approximately 33% each) to provide visual variety.
5. WHEN the game is in the Playing State, THE minimum time between consecutive Cactus spawns SHALL be no less than the time required for the Dino to complete a full Jump arc at the current Game_Speed, ensuring the player always has a reachable gap.

---

### Requirement 4: Difficulty Progression

**User Story:** As a player, I want the game to get faster over time, so that the challenge increases the longer I survive.

#### Acceptance Criteria

1. WHILE the game is in the Playing State, THE Game SHALL increase Game_Speed at a constant acceleration rate of 0.001 pixels per frame per frame until Game_Speed reaches the defined maximum.
2. THE Game SHALL start with an initial Game_Speed of 5 pixels per frame and a maximum Game_Speed of 15 pixels per frame.
3. WHILE the game is in the Playing State, THE Game SHALL reduce the maximum Cactus spawn interval as Game_Speed increases, such that the interval at maximum Game_Speed is no greater than 50% of the interval at initial Game_Speed, with a hard floor of 500ms.

---

### Requirement 5: Score Tracking

**User Story:** As a player, I want to see my score increase as I play, so that I can measure my performance.

#### Acceptance Criteria

1. WHILE the game is in the Playing State, THE Game SHALL increment the Score at a rate of 1 point per second of survival.
2. THE Game SHALL display the Score as a zero-padded five-digit number (e.g., 00042) in the upper-right area of the Canvas.
3. WHEN the current Score exceeds the High_Score, THE Game SHALL update the High_Score to equal the current Score.
4. WHILE the browser session is active, THE High_Score SHALL persist across game restarts and SHALL be initialised to 0 at the start of a new session when no prior score exists.
5. WHEN the Score reaches 99999, THE Game SHALL cap the Score at 99999 and SHALL NOT increment it further, preserving the five-digit display format.

---

### Requirement 6: Collision Detection and Game Over

**User Story:** As a player, I want the game to end when my dinosaur hits a cactus, so that the game has meaningful stakes.

#### Acceptance Criteria

1. WHEN a Collision is detected between the Dino and a Cactus, THE Game SHALL transition to the Game Over state within the same game loop frame in which the Collision is detected.
2. WHEN the Game Over state is entered, THE Game SHALL stop the Game_Loop and freeze movement of the Dino, all active Cactus obstacles, and the Ground scroll.
3. WHEN the Game Over state is entered, THE Game SHALL display a "GAME OVER" message centered on the Canvas.
4. WHEN the Game Over state is entered, THE Game SHALL display a "Press Space / Tap to Restart" prompt centered below the "GAME OVER" message on the Canvas.
5. THE Game SHALL use axis-aligned bounding box (AABB) collision detection with a 10% inset on each side of both the Dino and Cactus bounding boxes to avoid false positives from sprite edges.

---

### Requirement 7: Game Restart

**User Story:** As a player, I want to restart the game after a game over, so that I can try to beat my high score.

#### Acceptance Criteria

1. WHEN the player presses the Space key or the ArrowUp key during the Game Over state, THE Game SHALL reset the Dino to its Ground position with no active Jump arc, clear all active Cactus obstacles, reset Game_Speed to its initial value, and resume the Game_Loop.
2. WHEN the player taps the Canvas during the Game Over state on a touch device, THE Game SHALL reset the Dino to its Ground position with no active Jump arc, clear all active Cactus obstacles, reset Game_Speed to its initial value, and resume the Game_Loop.
3. WHEN the game is restarted, THE Game SHALL reset the Score to zero and reset the Cactus spawn interval to its initial value corresponding to the initial Game_Speed.
4. WHEN the game is restarted, THE High_Score SHALL be preserved from the previous round and SHALL NOT be reset.
5. WHEN the game is restarted, THE Game SHALL confirm that the active Cactus obstacle array is empty before the first new Cactus is spawned.

---

### Requirement 8: Visual Rendering and Animation

**User Story:** As a player, I want smooth, visually clear animations, so that the game feels responsive and enjoyable.

#### Acceptance Criteria

1. THE Game SHALL render all entities using the HTML5 Canvas 2D API, targeting 60 frames per second with a per-frame budget of 16.7ms, driven by `requestAnimationFrame`.
2. WHILE the Dino is on the Ground in the Playing State, THE Dino SHALL display an alternating leg animation that cycles between two leg positions at a 200ms interval.
3. WHILE the Dino is airborne, THE Dino SHALL display a fixed jumping pose with no leg animation.
4. THE Ground SHALL scroll continuously from right to left at the current Game_Speed to create the illusion of forward motion.
5. THE Game SHALL render all sprites using geometric shapes drawn via the Canvas 2D API, requiring no external image assets.
6. IF the user's system or browser applies a dark color scheme, THEN THE page background surrounding the Canvas SHALL use a Tailwind CSS dark theme class (e.g., `bg-gray-900` with light-colored text).
7. IF the user's system or browser applies a light color scheme, THEN THE page background surrounding the Canvas SHALL use a Tailwind CSS light theme class (e.g., `bg-white` or `bg-gray-100` with dark-colored text).
