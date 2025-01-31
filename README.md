# KasparovAI - Advanced Chess AI

## Overview

KasparovAI is a sophisticated chess AI, boasting a rating of approximately 3000 ELO. This project provides a platform to play against a challenging AI opponent, implemented using the Minimax algorithm with alpha-beta pruning for optimal move selection. It utilizes HTML, CSS, and JavaScript (with jQuery for DOM manipulation) to deliver a visually interactive and engaging chess experience.

## Features

*   **Advanced AI:** Implements the Minimax algorithm with alpha-beta pruning to achieve a 3000 ELO rating.
*   **Adjustable Difficulty:** Offers multiple difficulty levels ranging from beginner to international master, allowing players of different skills to compete.
*   **Interactive Board:** Players can interact with the chessboard by dragging and dropping pieces to make their moves.
*   **Move History:** Displays a history of moves made during the game.
*   **Responsive Design:** The game is designed to adapt to different screen sizes.
*   **Social Media Links:** Provides social links to the developer's accounts.
*   **Piece Value Evaluation:** Uses a positional evaluation function that takes into account the position of the pieces and also their material value.
*   **Variant included** You can find a slightly tweaked version of the AI inside `Variants/KasparovAI-Tweak/`

## Project Structure

The project is organized as follows:

ananya-addisu-kasparov-ai/
├── README.md          - This file.
├── ai.js              - Contains the core AI logic, including the minimax algorithm.
├── board.css          - Styles the chess board elements and layout.
├── index.html         - The main HTML file for the chess game interface.
├── jquery.js          - jQuery library file.
├── rules.js           - Not used, likely a placeholder
├── validmoves.js      - Contains helper functions for validating moves and creating the board.
├── style.css          - Contains general styles for the page layout and other UI elements.
├── img/               - Contains images for the chess pieces.
│   └── chesspieces/
│       └── wikipedia/
│
└── Variants/          - Contains the modified (or tweaked) version and also a mobile version
    ├──  KasparovAI - Mobile.rar  - The mobile version in a RAR archive
    ├── KasparovAI.userpref
    ├── kaparovai.hepx
    ├── KasparovAI - Mobile/
    │   ├── index.html   - The index.html file for the mobile version
    |   └── img
    |       └── chesspieces
    |           └── wikipedia
    └── KasparovAI - Tweak/
        ├── ai.js          - AI logic for the tweak version
        ├── board.css      - board styles for the tweaked version
        ├── index.html     - index.html file of the tweaked version
        ├── jquery.js      - jquery library of the tweaked version
        ├── rules.js        - not used
        ├── style.css       - styles for the tweaked version
        ├──  validmoves.js     - validates moves for the tweaked version
        └── img/
            └── chesspieces/
                └── wikipedia/


**Key Files and Functionalities:**

*   **`ai.js`:** This file contains the `minimax` and `minimaxRoot` functions, which are the core of the AI's decision-making process. It also includes an `evaluateBoard` function, which assigns scores to board positions using piece values and positional data to determine the bot's evaluation of said position.
*   **`board.css`:** Provides styling for the chessboard squares and notation. The CSS classes here directly relate to those used by chessboard.js
*   **`index.html`:**  Sets up the layout of the game, encompassing the difficulty selector dropdown, the chess board, the move history area. Also includes the javascript files.
*   **`validmoves.js`:**  Contains the `ChessBoard` function and other functions for generating the chessboard and validates moves using the selected chess move validation system for the front end.
*   **`style.css`:** Provides the overall look and feel of the website, including colors, fonts, and responsiveness. it includes styles for the difficulty dropdown, the history card, and the social media buttons
*   **Variants/KasparovAI - Tweak/:** Contains a slightly tweaked version with a different difficulty selection and UI compared to the base variation.

## The AI Logic

The AI in KasparovAI operates using the Minimax algorithm with alpha-beta pruning. Here's a step-by-step breakdown:

1.  **Move Generation:** When it's the AI's turn, the `minimaxRoot` function is called. This function first gets all the legal moves available in the current game state using `game.ugly_moves()`.

2.  **Iterative Minimax:** The `minimaxRoot` function iterates through each of these possible moves. For each move:
    *   The move is temporarily made on a copy of the game using `game.ugly_move(newGameMove)`.
    *   The `minimax` function is then recursively called to evaluate the resulting board state.
    *   The move is undone using `game.undo()` to revert to the original state for the next move evaluation.
    *   The result returned by `minimax` is compared, and the best resultant move is selected as `bestMoveFound`.
    *   It then returns `bestMoveFound` to be moved by the AI.

3.  **Recursive Minimax (`minimax` Function):**
    *   **Base Case:** If the recursion reaches the specified `depth` (which corresponds to the bot's difficulty), the `evaluateBoard` function is called to score the board. Note: The algorithm returns the negative of the evaluation, to make this function act as what it should (maximize the value for the bot, therefore minimize the value for the human).
    *   **Maximizing Player (AI):**
        If the `isMaximisingPlayer` argument is true, the AI seeks to maximize its score.
        *   It iterates through each possible move in the current game state using `game.ugly_moves()`.
        *   For each, it makes the move, recursively calls `minimax` to go a level deeper, then undoes the move.
        *   It continually tracks the maximum of all the returned evaluations as `bestMove`. Alpha is also used to to prune off useless branches as a side effect of tracking the max evaluation
        *   If the beta threshold is lower than alpha, then we prune, since, from here down, we are guaranteed not to find a better evaluation than what the maxminising node already has.
        *   The function returns `bestMove`, which for the maximizing layer represents the single best move that maximizes its evaluation.
    *   **Minimizing Player (Opponent):**
        If `isMaximisingPlayer` is false, it assumes that the AI's opponent (the human) will try to minimize the AI's score.
        *   Similar structure as the maximizing player, except it tracks the `min` value and moves, representing what the next player's best move is (with respect to the AI's benefit)
        *   Beta is used to prune nodes in a similar way as with the maximizing node.
        *   The function returns `bestMove`, representing the single best move that minimizes the next person's evaluation.

4. **Alpha-Beta Pruning**: During the `minimax` recursive calls, `alpha` and `beta` parameters are passed. The alpha-beta pruning logic is used to prune parts of the tree if it can be determined that they won't affect the result of the minimax, allowing the algorithm to drastically increase the speed at which it calculates the best move.

5.  **Board Evaluation (`evaluateBoard` Function):**
    *   This function assigns a numerical score to the current board position, by going through every square of the board.
    *   It uses the `getPieceValue` function to score every piece, considering both the type of piece and its position.
    *   The `pawnEvalWhite`, `knightEval`, `bishopEvalWhite`, `rookEvalWhite`, `evalQueen` and `kingEvalWhite` arrays are used to give positional advantage when scoring pieces (for example, a knight in the center being worth slightly more than on the side.) Similar evaluations are also used for black pieces to properly calculate evaluations if black is the one being evaluated.
    *   The total evaluation is returned, representing how advantageous the board state is for white.
    *   note that this is actually the negative of the evaluation that is returned, to cause `minimax` to perform correctly.

6.  **Best Move Selection:** Once `minimaxRoot` has finished it returns the move corresponding to the optimal path, which will be performed by the AI.

In essence, the AI explores the tree of possible moves, evaluating each path and selecting the one that it believes will lead to the best outcome for itself, and worst for it's opponent, within the specified search depth. The alpha-beta pruning helps in skipping the calculations that can be determined to be irrelevant, improving the calculation speed.

## Technology Stack

*   **HTML:**  Structures the webpage and game interface.
*   **CSS:** Styles the webpage and game elements for visual presentation.
*   **JavaScript:**  Implements the game logic, AI functionality, and user interactions.
*   **jQuery:** Used for DOM manipulation to create and move the pieces easier.
*   **chessboard.js:** Used for the actual rendering and piece movement logic.

## How to Run the Project

1.  Clone or download the repository.
2.  Open the `index.html` file in `/ananya-addisu-kasparov-ai/` with your web browser.
3.  Choose a difficultly level in the dropdown under the "Bot type"
4.  Start playing chess by dragging and dropping the pieces in the chessboard.

## Variants

The project includes some variants:

*   **KasparovAI - Mobile.rar:** The complete folder of the mobile version, in a Rar archive.
*  **KasparovAI - Mobile/:** The folder of the mobile version, also available in the `.rar`.
*   **KasparovAI - Tweak/:** A tweaked version of the AI, located in it's own folder. It has a simplified difficulty setting, and simplified UI.

## Dependencies
*   **jQuery**: included through `jquery.js`.

## Notes

*   The AI's strength is controlled by the search depth parameter, which can be configured via the dropdown menu.
*   The game utilizes the `chess.js` library (implicitly through `chessboard.js`) for handling the underlying chess logic.
* The `rules.js` file is unused in this code, perhaps indicating a remnant from a previous iteration.

## Author

*   **Ananya Addisu**

## Social Media
Check out my other projects at:

<div align="center">
  <a href="https://intagram.com/aniwkn">
    <img src="https://www.svgrepo.com/show/491664/instagram.svg" width="30" height="30" alt="Instagram">
  </a>
  <a href="https://twitter.com/an1nya">
     <img src="https://www.svgrepo.com/show/491681/twitter.svg" width="30" height="30" alt="Twitter">
  </a>
  <a href="https://github.com/ananya-addisu">
    <img src="https://www.svgrepo.com/show/491658/github.svg" width="30" height="30"  alt="Github">
  </a>
  <a href="https://discord.gg/">
       <img src="https://www.svgrepo.com/show/491652/discord.svg" width="30" height="30" alt="Discord">
  </a>
</div>

