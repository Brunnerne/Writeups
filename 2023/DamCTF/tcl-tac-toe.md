# tcl-tac-toe (web)

We have a game of tic-tac-toe against a computer player where we can send a previous board, new board, and a signature.
This is then verified, so previous board is signed and verified against the signature, and the previous and new board are compared to ensure the move is okay. Then it checks for a potential winner and otherwise, the computer takes its turn, and so on.

When validating the move, it checks that exactly one field was changed, and that no previous X or O was overwritten.
But when making a move itself, the computer checks whether a field contains exactly the character -.
This means we can instead place another character - like ? - and this prevents the computer from placing there, but it isn't seen as an X and thus cheats its placement algorithm.

In practice, we place a ? in the middle first:
```js
prev_board = Array.from(gameBoard);
gameBoard[4] = '?';
post_board_update((new_board, new_signature, message) => {
    gameBoard = new_board;
    signature = new_signature;
    for (let i = 0; i < squares.length; i++) {
        squares[i].textContent = gameBoard[i];
    }
    if (message != "") {
        setTimeout(()=>{
            alert(message);
            if (message.startsWith("Impossible") || message.startsWith("Haha") || message.startsWith("Cat")) {
                location.reload();
            }
        }, 250);
    }
});
```

Then we just play the game until needing only the middle square and then we convert this to an X (which is allowed), thus winning:
```js
prev_board = Array.from(gameBoard);
gameBoard[4] = 'X';
post_board_update((new_board, new_signature, message) => {
    gameBoard = new_board;
    signature = new_signature;
    for (let i = 0; i < squares.length; i++) {
        squares[i].textContent = gameBoard[i];
    }
    if (message != "") {
        setTimeout(()=>{
            alert(message);
            if (message.startsWith("Impossible") || message.startsWith("Haha") || message.startsWith("Cat")) {
                location.reload();
            }
        }, 250);
    }
});
```
