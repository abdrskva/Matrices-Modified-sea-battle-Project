# Matrices-Modified-sea-battle-Project
import os

def clear_screen():
    os.system('cls' if os.name == 'nt' else 'clear')

def print_board(board):
    print("   A B C D E F G")
    for i, row in enumerate(board):
        print(i + 1, end=" |")
        for cell in row:
            print(cell, end=" ")
        print()

def place_ship(board, ship_size):
    while True:
        orientation = "horizontal" if input("Place ship of size {} horizontally? (y/n): ".format(ship_size)).lower() == 'y' else "vertical"
        coordinates = input("Enter starting coordinates for the ship (e.g., A3): ").upper()

        try:
            x, y = ord(coordinates[0]) - 65, int(coordinates[1]) - 1
            if orientation == "horizontal" and all(board[y][x+i] == " " for i in range(ship_size)):
                for i in range(ship_size):
                    board[y][x+i] = "S"
                break
            elif orientation == "vertical" and all(board[y+i][x] == " " for i in range(ship_size)):
                for i in range(ship_size):
                    board[y+i][x] = "S"
                break
            else:
                print("Invalid placement. Try again.")
        except (ValueError, IndexError):
            print("Invalid coordinates. Try again.")

def take_shot(board):
    while True:
        coordinates = input("Enter coordinates to shoot (e.g., A3): ").upper()

        try:
            x, y = ord(coordinates[0]) - 65, int(coordinates[1]) - 1
            if 0 <= x < 7 and 0 <= y < 7 and board[y][x] not in ["H", "M"]:
                return x, y
            else:
                print("Invalid coordinates. Try again.")
        except (ValueError, IndexError):
            print("Invalid coordinates. Try again.")

def update_board(board, x, y):
    if board[y][x] == "S":
        board[y][x] = "H"
        return True
    else:
        board[y][x] = "M"
        return False

def check_ship_sunk(board, x, y):
    ship_size = 1
    if board[y][x] == "H":
        for i in range(1, 4):
            if x + i < 7 and board[y][x+i] == "H":
                ship_size += 1
            else:
                break
        for i in range(1, 4):
            if x - i >= 0 and board[y][x-i] == "H":
                ship_size += 1
            else:
                break
    else:
        for i in range(1, 4):
            if y + i < 7 and board[y+i][x] == "H":
                ship_size += 1
            else:
                break
        for i in range(1, 4):
            if y - i >= 0 and board[y-i][x] == "H":
                ship_size += 1
            else:
                break

    if ship_size == 3:
        print("You sunk a ship!")

def is_game_over(board):
    return all(cell != "S" for row in board for cell in row)

def main():
    print("Welcome to Battleship!")

    player_name = input("Enter your name: ")
    print("Hello, {}!".format(player_name))

    while True:
        board = [[" "]*7 for _ in range(7)]

        # Place ships on the board
        place_ship(board, 3)
        place_ship(board, 2)
        place_ship(board, 2)
        place_ship(board, 1)
        place_ship(board, 1)
        place_ship(board, 1)
        place_ship(board, 1)

        shots = 0

        while not is_game_over(board):
            clear_screen()
            print_board(board)
            x, y = take_shot(board)
            hit = update_board(board, x, y)
            if hit:
                check_ship_sunk(board, x, y)
            shots += 1

        clear_screen()
        print_board(board)
        print("Congratulations, {}! You've sunk all the ships in {} shots.".format(player_name, shots))

        play_again = input("Do you want to play again? (y/n): ").lower()
        if play_again != 'y':
            break

if __name__ == "__main__":
    main()
