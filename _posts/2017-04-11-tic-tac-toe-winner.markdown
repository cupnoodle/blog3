---
layout: post
title:  "Tic Tac Toe determine winner in O(1) constant time"
date:   2017-04-11 10:20:10
permalink: "tic-tac-toe-determine-winner-in-o-1-constant-time"
---



One of my friend got [this question](https://www.glassdoor.com/Interview/How-would-you-determine-if-someone-has-won-a-game-of-tic-tac-toe-on-a-board-of-any-size-QTN_1104.htm) for a recent interview, I thought it was interesting so I gave it a shot.
<br>  
The question is given that an arbitrary square board with size N (NxN), the player wins if there is a complete line of X in the board. How do you check if the player has won the Tic Tac Toe game in constant time?  
<br>
By definition, the winning condition for Tic Tac Toe is having a full row, full column or full diagonal of your symbol placed.  
<br>  
We can use two arrays to keep track of the number of X (or O) placed in a given row / column and two variable to keep track of the number of X (or O) placed in diagonal / inverse diagonal, since there is only one possible diagonal (top left to bottom right) and inverse diagonal (top right to bottom left) for Tic Tac Toe.

eg : 
**row[2] = 4** means third row has 4 X placed.  
**col[0] = 2** means first column has 2 X placed.  
**diagCount = 3** means there is 3 X placed in diagonal (the line from top left to bottom right)  
**inverseDiagCount = 1** means there is 1 X placed in inverse diagonal (the line from top right to bottom left)  
<br>  
If one of these variable is equal to board size, then the player has won.  We increment these variable respectively after player has finished his/her turn.  

eg: For a board with size **3**, if row[0] (first row) has 3 X placed, then the player has won.
<br>  
We can write a function **bool hasGameEnded()** to check for all these variable if they are equal to board size. This function will run every time after player has finished his/her turn. The function will return true if one of the variable is equal to board size, otherwise false. But this function will need O(n) time complexity to run as we need to check **n** rows and **n** columns plus two diagonal (**2n + 2**).  
<br>  
We can further reduce the time complexity by passing the coordinate of the latest move of player into the function, **bool hasGameEnded(lastPlacedX, lastPlacedY)**. We then only need to check for the row and column belonging to the last position placed by the player.  

eg:  The player last move was (0,2)  then the program only needs to check the first column and third row plus diagonal and inverse-diagonal to see if the value (count) is equal to board size. 
**This reduces 2n + 2 checks to 4 checks**.
<br>  
There is no need to check all rows/columns as checking the row/column of the last placed position is suffice since the game will end if the last placed position form a complete line.  
<br>  
I have written a ruby script to demonstrate this question. Note the `game_has_ended?` method.


<script src="https://gist.github.com/cupnoodle/bbf651dd1d9fbea398df635e62ad2f36.js"></script>