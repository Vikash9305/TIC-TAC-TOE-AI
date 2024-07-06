from tkinter import *
from tkinter import messagebox

# Global variables
X = "X"
O = "O"
EMPTY = " "
AI = O
HUMAN = X

# Initialize the board
board = [EMPTY] * 9

def ButtonClick(button, index):
    global board
    if board[index] == EMPTY:
        button["text"] = HUMAN
        board[index] = HUMAN
        check_for_win()
        if EMPTY in board:
            ai_move()

def ai_move():
    global board
    best_score = -float('inf')
    best_move = None
    for i in range(9):
        if board[i] == EMPTY:
            board[i] = AI
            score = minimax(board, 0, False)
            board[i] = EMPTY
            if score > best_score:
                best_score = score
                best_move = i
    if best_move is not None:
        buttons[best_move]["text"] = AI
        board[best_move] = AI
        check_for_win()

def minimax(board, depth, is_maximizing):
    result = evaluate(board)
    if result != 0:
        return result
    if EMPTY not in board:
        return 0
    
    if is_maximizing:
        max_eval = -float('inf')
        for i in range(9):
            if board[i] == EMPTY:
                board[i] = AI
                eval = minimax(board, depth + 1, False)
                board[i] = EMPTY
                max_eval = max(max_eval, eval)
        return max_eval
    else:
        min_eval = float('inf')
        for i in range(9):
            if board[i] == EMPTY:
                board[i] = HUMAN
                eval = minimax(board, depth + 1, True)
                board[i] = EMPTY
                min_eval = min(min_eval, eval)
        return min_eval

def evaluate(board):
    lines = [(0, 1, 2), (3, 4, 5), (6, 7, 8),
             (0, 3, 6), (1, 4, 7), (2, 5, 8),
             (0, 4, 8), (2, 4, 6)]

    for line in lines:
        if board[line[0]] == board[line[1]] == board[line[2]] == AI:
            return 1
        elif board[line[0]] == board[line[1]] == board[line[2]] == HUMAN:
            return -1
    return 0

def check_for_win():
    global board
    lines = [(0, 1, 2), (3, 4, 5), (6, 7, 8),
             (0, 3, 6), (1, 4, 7), (2, 5, 8),
             (0, 4, 8), (2, 4, 6)]

    for line in lines:
        if board[line[0]] == board[line[1]] == board[line[2]] == HUMAN:
            messagebox.showinfo("Tic Tac Toe", "Player X won!")
            reset_board()
            return True
        elif board[line[0]] == board[line[1]] == board[line[2]] == AI:
            messagebox.showinfo("Tic Tac Toe", "Player O won!")
            reset_board()
            return True
    if EMPTY not in board:
        messagebox.showinfo("Tic Tac Toe", "Game Tied")
        reset_board()
        return True
    return False

def reset_board():
    global board
    for i in range(9):
        board[i] = EMPTY
        buttons[i]["text"] = ""

# Initialize the main window
main = Tk()
main.title("Tic Tac Toe")

# Initialize game variables
buttons = []
for i in range(9):
    button = Button(main, text="", font=("Arial", 60, "bold"),
                    bg="#ffb5a7", fg="white", width=3, command=lambda i=i: ButtonClick(buttons[i], i))
    button.grid(row=i // 3, column=i % 3)
    buttons.append(button)

# Start the main event loop
main.mainloop()
