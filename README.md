# neon-rummikub-game
Neon Rummikub is a digital adaptation of the classic tile-based game, Rummikub.

## **Executive Summary:** 

Neon Rummikub is designed with a vibrant, neon-themed aesthetic. This single-player experience pits the user against an AI opponent, challenging them to be the first to clear their rack by forming valid sets (groups or runs) on the shared game board. The game emphasizes strategic tile placement, set manipulation, and quick decision-making. Key features include an intuitive drag-and-drop interface, dynamic score tracking, a draw pile, and a high-score system to encourage replayability. The neon visual style provides a modern and engaging backdrop to the timeless gameplay, making it accessible and enjoyable for both new and experienced Rummikub players.

## **Technical Summary:**

### **Architecture & Design**

Neon Rummikub is implemented as a client-side web application using a standard **HTML, CSS, and JavaScript** stack. The architecture follows a **Model-View-Controller (MVC)**\-like pattern, albeit implicitly, where:

* **HTML** defines the structural elements of the game (board, racks, buttons, modals).  
* **CSS** provides the "Neon" themed styling, responsive layout, and visual feedback for interactions (e.g., selected tiles, invalid moves, turn indicators).  
* **JavaScript** serves as the core "controller" and "model," managing the game state, implementing game logic, handling user input, and dynamically updating the HTML elements.

The design prioritizes a **single-page application** approach, where all game interactions occur within the same HTML document, minimizing page reloads and enhancing user experience. Responsive design principles are applied using CSS media queries to ensure playability across various device sizes.

### 

### **Core Systems**

1. **Game State Management:** Centralized let variables manage the entire game state, including:  
   * deck: Array representing the draw pile.  
   * computerTiles: Array storing the computer's hidden tiles.  
   * playerScore, computerScore: Numerical scores.  
   * isFirstMove: Boolean for the initial 30-point rule.  
   * gameActive, playerTurn: Booleans controlling game flow and turn.  
   * selectedTiles: Array of DOM elements currently selected by the player.  
   * lastValidBoardState, lastValidRackState, lastComputerRackState: Arrays storing previous game states for the "Undo" feature.  
   * highScores: Array for persistent high scores, stored in localStorage.

2. **Tile Management:**  
   * createTile(): Generates HTML div elements for tiles, applying appropriate CSS classes for color and visual representation (number or joker symbol).  
   * createAndAddTile(): Manages drawing tiles from the deck and appending them to either the player's rack or the computerTiles array.  
   * updateComputerRackDisplay(): Renders placeholder tiles for the computer's rack, hiding their values.

3. **Input Handling & UI Interaction:**  
   * **Click Events:** Used for tile selection (toggleTileSelection), button actions (handlePlay, handleDraw, handleCancel, handleUndo, handleHint, handlePass, init), and difficulty selection.  
   * **Drag & Drop (Desktop) / Touch Events (Mobile):** Implemented for intuitive tile manipulation on the player's rack and moving tiles to the board (handleDragStart, handleDragEnd, handleDrop).  
   * **Keyboard Shortcuts:** Escape to clear selection, Enter to play, Spacebar to draw, enhancing accessibility for desktop users.

4. **Game Logic & Validation:**  
   * validateMove(): The primary function for determining if a player's selected tiles form a valid Rummikub move (new set or addition to existing sets).  
   * isValidNewSet(), isValidGroup(), isValidRun(): Helper functions to validate specific set types, including robust joker handling.  
   * canAddToExistingSets(): Checks if selected tiles can extend existing sets on the board.  
   * calculatePoints(): Determines the score for played tiles.

5. **Computer AI (computerMove):**  
   * findComputerMoves(): The core AI logic, which attempts to find valid moves for the computer by:  
     * Iterating through combinations of computer's tiles to form new groups and runs.  
     * Attempting to add single tiles to existing sets on the board.  
     * Includes basic joker utilization in new sets.  
   * **Difficulty Levels:** The AI selects moves based on the chosen difficulty (easy \- random, medium \- highest score, hard \- fewest tiles played).  
   * If no valid moves are found, the computer draws a tile.

6. **UI Updates & Feedback:**  
   * updateUI(): A central function to refresh all dynamic UI elements (scores, tile counts, draw pile size, button states).  
   * showHint(): Provides contextual messages to the player, with different styling for success, error, or warning.  
   * updateTurnIndicator(): Visually indicates whose turn it is.  
   * handleInvalidMove(): Provides visual feedback for invalid plays.

7. **Persistence:**  
   * localStorage: Used to store high scores persistently across browser sessions, managed by loadHighScores(), updateHighScoresDisplay(), checkHighScore(), and saveScore().

### **Challenges & Solutions**

1. **Draw Condition Detection:**  
   * **Challenge:** The original implementation failed to detect a draw when both players had full racks and no valid moves, even if the deck wasn't empty.  
   * **Solution:** Introduced a checkStalledGame() function that explicitly verifies if both players can make *any* valid move and if their racks are full (or the deck is empty). This function is called at critical points (handlePlay, handleDraw, computerMove, handlePass) to immediately end the game in a draw if the conditions are met.

2. **Complex Rummikub Rules (e.g., First Move, Joker Logic):**  
   * **Challenge:** Accurately implementing the "first move must be 30+ points" rule and flexible joker usage within sets.  
   * **Solution:** isFirstMove flag and score checks were integrated into handlePlay and findPossibleMoves. Joker handling in isValidGroup and isValidRun was refined to correctly account for their flexible nature, allowing them to substitute for missing tiles.

3. **AI Complexity:**  
   * **Challenge:** Developing a computer opponent that can make reasonably intelligent moves without being overly complex or resource-intensive.  
   * **Solution:** findComputerMoves was designed to prioritize common Rummikub strategies (forming new sets, adding to existing ones) and includes a basic approach to joker integration. Difficulty levels provide a simple scaling mechanism.

4. **Responsive Design:**  
   * **Challenge:** Ensuring the game layout and tile sizes adapt well to different screen dimensions.  
   * **Solution:** Extensive use of flexible CSS units, flexbox for layout, and @media queries to adjust tile sizes and control element visibility/arrangement on smaller screens.

### **Optimizations**

* **DOM Manipulation:** While direct DOM manipulation is used, efforts are made to batch updates where possible (e.g., updateUI()) to minimize reflows.  
* **Event Delegation:** Using event listeners on parent elements (like board and rack) for drag-and-drop events rather than on individual tiles improves performance for a large number of elements.  
* **setTimeout for AI:** Introducing setTimeout before the computer's move provides a better user experience by giving the player time to process their turn and observe the UI changes.

### **Future Enhancements**

1. **Advanced AI:**  
   * Implement more sophisticated AI strategies, such as:  
     * Splitting and rearranging existing sets on the board.  
     * Replacing jokers with actual tiles from hand.  
     * Considering future moves or blocking the player.  
     * More nuanced scoring for AI move selection.  
2. **Multiplayer Functionality:**  
   * Integrate a backend (e.g., Firebase Firestore) to enable real-time multiplayer games.  
3. **Visual Enhancements:**  
   * More elaborate animations for tile movement and set formation.  
   * Custom fonts and improved visual effects for scores and messages.  
   * Sound effects for game actions (playing tiles, drawing, winning/losing).  
4. **Improved Undo System:**  
   * Allow undoing multiple steps, potentially with a visual history.  
5. **Rule Variations:**  
   * Option to enable/disable specific Rummikub house rules.  
6. **Tutorial/Onboarding:**  
   * An interactive tutorial for new players to learn the rules and interface.

### **Conclusion**

Neon Rummikub is a well-structured and engaging web-based game that successfully captures the essence of the classic Rummikub experience. Its client-side architecture, combined with thoughtful game logic and a visually appealing design, provides a solid foundation. The recent enhancements, particularly the robust draw condition detection, significantly improve game fairness and completeness. While functional and enjoyable, there's ample room for further development in AI sophistication and advanced Rummikub mechanics to elevate the gameplay experience.

# **Modifications:**

### ** Three improvements have been made on 7th July 2025:
1. *Duplicate Tile Detection*: Rejects sequences with duplicate numbers (5, 6, 6). Enhanced joker logic ensures they accurately fill gaps and manage mixed colours and all-joker scenarios for better tile counting and sequence validation.
2. *Improve Draw Detection*: Tracks consecutive passes, allowing a maximum of two. Ensures the game correctly identifies draw conditions when players are unable to make valid moves.
3. *High-Score Detection*: The game updates the high-score board if it ends suddenly in a draw or if the player starts a new game.
