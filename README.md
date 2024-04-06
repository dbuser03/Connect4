# Connect4 Class

This class represents the game of Connect 4. It includes methods for initializing the game, getting the initial state, getting the next state, checking for a win, and more.

### Methods

`__init__`
Initializes a new instance of the Connect4 class. Sets the row count to 6, column count to 7, action size to the column count, and the number of pieces in a row needed to win to 4.

`__repr__`
Returns a string representation of the class.

`get_initial_state`
Returns the initial state of the game as a 2D numpy array of zeros with dimensions row_count x column_count.

`get_next_state`
Given the current state, an action, and a player, it returns the next state of the game.

`get_valid_moves`
Returns a 1D numpy array representing valid moves in the current state.

`check_win`
Checks if the last action resulted in a win.

`get_value_and_terminated`
Returns the value of the current state and a boolean indicating whether the game has ended.

`get_opponent`
Returns the opponent of the given player.

`get_opponent_value`
Returns the opposite of the given value.

`change_perspective`
Changes the perspective of the game state to the given player.

`get_encoded_state`
Returns the encoded state of the game.

### Usage

```python
game = Connect4()
initial_state = game.get_initial_state()
next_state = game.get_next_state(initial_state, action=3, player=1)
valid_moves = game.get_valid_moves(next_state)
win = game.check_win(next_state, action=3)
value, terminated = game.get_value_and_terminated(next_state, action=3)
opponent = game.get_opponent(player=1)
opponent_value = game.get_opponent_value(value=1)
changed_perspective = game.change_perspective(next_state, player=1)
encoded_state = game.get_encoded_state(next_state)
```

---

# Node Class

This class represents a node in a Monte Carlo Tree Search (MCTS) for the game of Connect 4. Each node represents a game state and contains methods for expanding the tree, simulating games, and backpropagating results.

### Methods

`__init__`
Initializes a new instance of the Node class. Sets the game, arguments, state, parent node, and action taken to reach this node.

`is_fully_expanded`
Checks if the node is fully expanded, i.e., all possible moves from this state have been explored.

`select`
Selects the child node with the highest Upper Confidence Bound (UCB).

`get_ucb`
Calculates the UCB of a child node.

`expand`
Expands the tree by creating a new child node for an unexplored move.

`simulate`
Simulates a game from the current state to the end of the game using random moves.

`backpropagate`
Backpropagates the result of a simulation up the tree, updating the value sum and visit count of each node along the path.

### Usage

```python
node = Node(game, args, state)
is_fully_expanded = node.is_fully_expanded()
best_child = node.select()
ucb = node.get_ucb(best_child)
new_child = node.expand()
value = node.simulate()
node.backpropagate(value)
```

---

# MCTS Class

This class represents a Monte Carlo Tree Search (MCTS) for the game of Connect 4. It includes methods for initializing the search and performing the search.

### Methods

 `__init__`
Initializes a new instance of the MCTS class. Sets the game and arguments.

`search`
Performs the MCTS. It starts by creating a root node for the given state. Then, it repeatedly selects the most promising node, expands the tree if the node is not terminal, simulates a game if the node is not terminal, and backpropagates the result. This process continues for a fixed amount of time (10 seconds). Finally, it returns the probabilities of each action being selected.

### Usage

```python
mcts = MCTS(game, args)
action_probs = mcts.search(state)
```

---

# Connect 4 Game Simulation

This script simulates a game of Connect 4 between a human player and an AI player. The AI player uses a Monte Carlo Tree Search (MCTS) for decision making.

### Functions

`print_board`
Prints the current state of the game board.

### Main Script

The script starts by initializing the game and the MCTS. It then enters a loop where the human player and the AI player take turns playing the game. The human player is asked to input their move, while the AI player uses the MCTS to determine its move. The game continues until a player wins or the game is a draw.

### Usage

```python
print_board(state)
game = Connect4()
player = 1
args = {'C': 1.41}
mcts = MCTS(game, args)
state = game.get_initial_state()
```

The game loop:

```python
while True:
    if player == 1:
        valid_moves = game.get_valid_moves(state)
        print ('valid moves:', [i for i in range (game.action_size) if valid_moves[i] == 1])
        action = int (input (f"{player}: "))
        if valid_moves[action] == 0:
            print ('action not valid')
            continue
    else:
        neutral_state = game.change_perspective(state, player)
        mcts_probs = mcts.search(neutral_state)
        action = np.argmax(mcts_probs)
        print (f"AI: {action}")
    state = game.get_next_state(state, action, player)
    time.sleep(2)
    clear_output(wait=True)
    value, is_terminal = game.get_value_and_terminated(state, action)
    print_board(state)
    if is_terminal:
        print_board(state)
        if value == 1:
            print (player, "won")
        else:
            print ("draw")
        break
    player = game.get_opponent(player)
```