# Battleship Assignment (C++)

## Overview
Create a **console-based** Battleship game in C++ with:
1. A **Ship** base class and **five derived classes** (Carrier, Battleship, Cruiser, Submarine, Destroyer).
2. A **Board** (or **Grid**) class for managing a 10×10 layout of cells (ship placement, hits, misses).
3. A **Player** base class and two derived classes: **HumanPlayer** and **AiPlayer**.
4. Turn-by-turn gameplay until one player’s entire fleet (all five ships) is sunk.

---

## Class Details

### 1. `Ship` (Base Class)
- **Members**
  - `char* name;`
  - `int size;`
  - `int hitsTaken;`
- **Functions**
  - `Ship(const char* shipName, int shipSize);`  
    Initializes `name` and `size`.
  - `virtual ~Ship();`  
    Cleans up allocated memory for `name`.
  - `virtual void takeHit();`  
    Increments `hitsTaken` and prints `"<ShipName> got hit!"`.  
    Checks if `hitsTaken >= size`.
  - `bool isSunk() const;`  
    Returns true if `hitsTaken >= size`.

### 2. Derived Ships
Each calls the base `Ship` constructor with a **unique name** and **size**:
- **Carrier** (size = 5)  
- **Battleship** (size = 4)  
- **Cruiser** (size = 3)  
- **Submarine** (size = 3)  
- **Destroyer** (size = 2)

---

### 3. `Board`
- **Purpose**: Manages a 10×10 grid for placing ships, and marking hits/misses.
- **Members**
  - `char cells[10][10];`  
    For instance, `'~'` for empty, `'S'` for ship, `'X'` for hit, `'M'` for miss.
- **Functions**
  - `Board();`  
    Initializes `cells` to a default (e.g., `'~'` for every cell).
  - `bool isTileOccupied(int row, int col) const;`  
    Returns true if the cell indicates a ship or previously hit cell.
  - `bool inBounds(int row, int col, int shipSize, bool horizontal) const;`  
    Checks if a ship of length `shipSize` fits from `(row, col)` horizontally or vertically in the 10×10 grid.
  - `void placeShip(int row, int col, int shipSize, bool horizontal, char symbol);`  
    Fills the cells for that ship with `symbol` (e.g., `'S'`) if valid.
  - `void markHit(int row, int col);`  
    Changes `cells[row][col]` to `'X'`.
  - `void markMiss(int row, int col);`  
    Changes `cells[row][col]` to `'M'`.
  - `char getCell(int row, int col) const;`  
    Returns the character in `cells[row][col]`.

---

### 4. `Player` (Base Class)
- **Members**
  - `char* playerName;`
  - `Ship* ships[5];`
  - `Board board;`
- **Functions**
  - `Player(const char* name);`  
    Sets `playerName`, initializes the board, etc.
  - `virtual ~Player();`  
    Cleans up `playerName` and `Ship` pointers.
  - `virtual void placeAllShips();`  
    Places the five ships on the board.  
    - For each ship: find (or prompt for) `(row, col)` and orientation.  
    - Check `board.inBounds(...)` and `!board.isTileOccupied(...)` for each cell.  
    - If valid, `board.placeShip(...)`; otherwise retry.  
  - `virtual void makeMove(Player* opponent);`  
    Chooses `(row, col)` to attack on `opponent->board`.  
    - If `opponent->board.getCell(row, col)` is a ship symbol, find the corresponding `Ship`, call `takeHit()`, then `board.markHit(row, col)`.  
    - Else `board.markMiss(row, col)`.  
  - `bool allShipsSunk() const;`  
    Returns true if each `Ship` in `ships[]` is `isSunk()`.

---

### 5. `HumanPlayer` (Derived from `Player`)
- **Overrides**
  - `void placeAllShips();`  
    Prompts the user for `(row, col)` and orientation for each ship.  
    Validates with `Board` functions before placing.
  - `void makeMove(Player* opponent);`  
    Prompts the user for `(row, col)` to attack, then calls `opponent->board` methods to mark hit/miss.

---

### 6. `AiPlayer` (Derived from `Player`)
- **Overrides**
  - `void placeAllShips();`  
    Automatically (e.g., randomly) picks `(row, col)` and orientation for each ship.  
    Uses `Board` checks for valid placement; retries if invalid.
  - `void makeMove(Player* opponent);`  
    Automatically picks `(row, col)` to attack. Calls `opponent->board.getCell(...)` to determine hit or miss.
- **Helper Function**
```cpp
  int AiPlayer::getRandomCoordinate() {
      // Ensure srand(...) is called in main
      return std::rand() % 10;
  }
```
---

### 7. `Game`
- **Members**
  - `Player* player1;`
  - `Player* player2;`
- **Functions**
  - `Game(Player* p1, Player* p2);`  
    Stores pointers to two `Player` objects (e.g., Human vs AI).
  - `void setup();`  
    Calls `player1->placeAllShips();` then `player2->placeAllShips();`.
  - `void start();`  
    Alternates turns:
    1. `player1->makeMove(player2);`  
    2. Check if `player2->allShipsSunk()`.  
    3. `player2->makeMove(player1);`  
    4. Check if `player1->allShipsSunk()`.  
    Continues until one side’s ships are all sunk, then announces the winner.
  - `bool isGameOver() const;`  
    Returns true if either `player1->allShipsSunk()` or `player2->allShipsSunk()`.

---

## Steps to Complete

1. **Implement Ship Hierarchy**
   - Base `Ship` + five derived classes with correct sizes/names.
   - Each `takeHit()` prints: `"<ShipName> got hit!"`.

2. **Implement Board**
   - Use a 2D `char` array for the 10×10 grid.
   - Provide functions to check/mark cells for ship placement, hits, and misses.

3. **Implement Players**
   - `HumanPlayer` interacts via console input for ship placement and attacks.
   - `AiPlayer` uses random (or some pattern) for ship placement and attacks.
   - Validate placements (in-bounds, no overlap) and handle hits/misses.

4. **Implement Game Flow**
   - Create two `Player` objects (e.g., `HumanPlayer` + `AiPlayer`) in `main()`.
   - `setup()` for ship placements.
   - `start()` loops until a fleet is sunk.

5. **Test Thoroughly**
   - Confirm correct hits, misses, and that no ships overlap.
   - Verify the game ends only after all ships of one player are sunk.

---

## Deliverables

1. **Header & Source Files**  
   - For `Ship` hierarchy, `Board`, `Player` hierarchy, and `Game`, plus a `main.cpp`.
2. **Working Program**  
   - Must compile and run from console.  
   - Demonstrate a full game session, from placing ships to final winner.
3. **Memory Management**  
   - Ensure no memory leaks (proper `delete` of dynamically allocated resources).