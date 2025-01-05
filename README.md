# Battleship Assignment (C++)

## Overview
Create a **console-based** Battleship game in C++ with:
1. A **Ship** base class and **five derived classes** (Carrier, Battleship, Cruiser, Submarine, Destroyer).
2. A **Grid** class for managing a 10×10 layout of cells (ship placement, hits, misses).
3. A **Player** base class and two derived classes: **HumanPlayer** and **AiPlayer**.
4. Turn-by-turn gameplay until one player’s entire fleet (all five ships) is sunk.
5. **Display** each player’s board **after every move** to show updated hits and misses.

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

### 3. `Grid`
- **Purpose**: Manages a 10×10 layout of cells for placing ships and marking hits or misses.
- **Members**  
  - `char cells[10][10];`  
    For instance, `'~'` for empty, `'S'` for ship, `'X'` for hit, `'M'` for miss.
- **Functions**  
  - `Grid();`  
    Initializes `cells` (e.g., set all to `'~'`).
  - `bool isTileOccupied(int row, int col) const;`  
    Returns true if the cell indicates a ship or a previously hit cell.
  - `bool inBounds(int row, int col, int shipSize, bool horizontal) const;`  
    Checks if placing a ship of length `shipSize` at `(row, col)` horizontally/vertically stays within the 10×10 grid.
  - `void placeShip(int row, int col, int shipSize, bool horizontal, char symbol);`  
    Marks each relevant cell with `symbol` (e.g., `'S'`) if valid.
  - `void markHit(int row, int col);`  
    Marks `cells[row][col]` as `'X'`.
  - `void markMiss(int row, int col);`  
    Marks `cells[row][col]` as `'M'`.
  - `char getCell(int row, int col) const;`  
    Returns `cells[row][col]`.
  - **Optional**: A function such as `printGrid()` to display the current state of `cells`.

---

### 4. `Player` (Base Class)
- **Members**  
  - `char* playerName;`  
  - `Ship* ships[5];`  *(five pointers to the different derived Ships)*  
  - `Grid grid;`       *(a 10×10 grid)*
- **Functions**  
  - `Player(const char* name);`  
    Initializes `playerName` and constructs `grid`.  
  - `virtual ~Player();`  
    Cleans up `playerName` and `Ship` pointers.  
  - `virtual void placeAllShips();`  
    Must place the five ships onto `grid`.  
    - For each ship, choose `(row, col)` and orientation.  
    - Check `grid.inBounds(...)` and `grid.isTileOccupied(...)`.  
    - If valid, use `grid.placeShip(...)`; otherwise retry.  
  - `virtual void makeMove(Player* opponent);`  
    Chooses `(row, col)` to attack on `opponent->grid`.  
    - If `opponent->grid.getCell(...)` is a ship, find the correct `Ship`, call `takeHit()`, then `opponent->grid.markHit(...)`.  
    - Otherwise `opponent->grid.markMiss(...)`.  
    - **After** the attack, **print** both players’ grids so you can see updated hits/misses.
  - `bool allShipsSunk() const;`  
    Returns true if all five ships are sunk.
  - **Optional**: `void displayMyGrid()` or `void displayGrid()` to print the grid to the console.

---

### 5. `HumanPlayer` (Derived from `Player`)
- **Overrides**  
  - `void placeAllShips();`  
    - **Prompts user** for `(row, col)` and orientation for each of the five ships.  
    - Uses `grid.inBounds(...)` and `grid.isTileOccupied(...)` to validate.  
    - Calls `grid.placeShip(...)` if valid.  
  - `void makeMove(Player* opponent);`  
    - **Prompts user** for `(row, col)` to attack.  
    - Checks `opponent->grid.getCell(...)` to see if it’s a ship.  
    - Calls the relevant `Ship`’s `takeHit()`, marks hits or misses, and **prints** the grids.

---

### 6. `AiPlayer` (Derived from `Player`)
- **Overrides**  
  - `void placeAllShips();`  
    - **Automatically** chooses `(row, col)` and orientation (random or pattern).  
    - Uses `grid.inBounds(...)` and `grid.isTileOccupied(...)` to ensure validity.  
    - Calls `grid.placeShip(...)` if valid; otherwise tries again.  
  - `void makeMove(Player* opponent);`  
    - **Automatically** picks `(row, col)` (random or pattern).  
    - Checks `opponent->grid.getCell(...)`.  
    - Calls the correct `Ship`’s `takeHit()`, marks a hit/miss, **prints** both grids afterward.
- **Helper**  
```cpp
  int AiPlayer::getRandomCoordinate() {
      // Ensure srand(...) is called in main
      return std::rand() % 10;
  }
```
*(Remember to call `std::srand(std::time(nullptr))` once in `main`.)*

---

### 7. `Game`
- **Members**  
  - `Player* player1;`  
  - `Player* player2;`
- **Functions**  
  - `Game(Player* p1, Player* p2);`  
    Assigns two player objects (e.g., `HumanPlayer` vs. `AiPlayer`).  
  - `void setup();`  
    Calls `player1->placeAllShips();`, then `player2->placeAllShips();`.  
  - `void start();`  
    - Loop until one side is fully sunk:  
      1. `player1->makeMove(player2);`  
      2. Check `player2->allShipsSunk()`.  
      3. `player2->makeMove(player1);`  
      4. Check `player1->allShipsSunk()`.  
    - Announce the winner when a fleet is sunk.
  - `bool isGameOver() const;`  
    Returns true if `player1->allShipsSunk()` or `player2->allShipsSunk()`.

---

## Steps to Complete

1. **Implement Ship Hierarchy**  
   - Base `Ship` + five derived classes (Carrier, Battleship, Cruiser, Submarine, Destroyer).
   - `takeHit()` prints `"<ShipName> got hit!"` and increments `hitsTaken`.

2. **Implement Grid**  
   - 10×10 `cells`.  
   - `isTileOccupied`, `inBounds`, `placeShip`, `markHit`, `markMiss`, etc.

3. **Implement Player Classes**  
   - `HumanPlayer` for user input on ship placement and attacks.  
   - `AiPlayer` automates those steps.  
   - Validate placement and record hits/misses.  
   - **Always** print both players’ grids after each move.

4. **Implement Game**  
   - Create two `Player` objects (e.g., `HumanPlayer`, `AiPlayer`) in `main()`.  
   - `setup()` places ships.  
   - `start()` loops until a fleet is sunk, printing results along the way.

5. **Test Thoroughly**  
   - Check that ships cannot overlap or exceed grid boundaries.  
   - Verify hits, misses, and correct sinking behavior.  
   - Confirm final winner is announced correctly.

---

## Deliverables

1. **Header & Source Files**  
   - For `Ship` (base + derived), `Grid`, `Player` (base + derived), and `Game`, plus a `main.cpp`.
2. **Working Program**  
   - Must compile and run, showing a complete session.  
   - Print boards after each attack to track progress.
3. **Memory Management**  
   - Properly `delete` dynamic allocations (names, ship pointers, etc.) to avoid leaks.

**End of Assignment**
