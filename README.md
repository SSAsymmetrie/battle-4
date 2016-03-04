from random import randint

board = []
ship_locations = []

# "Magic" numbers
suggested_board_size = 5
suggested_number_of_ships = 2

def populate_board(size):
    """Generate the board of size x size, returns size"""
    if(size < 1): # A 1x1 game is trivial and a 0x0 game is unwinnable
        print "Invalid board size.  Let's try " + str(suggested_board_size) + "x" + str(suggested_board_size)
        size = suggested_board_size
    for i in range(size):
        board.append(["O"] * size)  # ["O"] for ocean, note it must be a list because strings are immutable!
    return size

def print_board(board):
    """Prints the board in human-readable form"""
    if len(board) > 0:
        for row in board:
            print " ".join(row)
    else:  # We should never see this error message.
        print "The board is empty."

def random_row(board):
    """Generates random row location for ship"""
    return randint(0, len(board) - 1)

def random_col(board):
    """Generates random row location for ship"""
    return randint(0, len(board[0]) -1)

def generate_hidden_ships(number):
    """Generates all of the hidden ships in the game"""
    for i in range(number):
        hidden_ship = [random_row(board), random_col(board)]
        ship_locations.append(hidden_ship)

def guess_valid_location(location):
    """Given a valid (on the board location), checks for hits, misses, or previous guesses"""
    if location in ship_locations:
        board[location[0]][location[1]] = "H"  # Mark the hit
        print_board(board)
        ship_locations.remove(location) # user wins when all ships are found (ship_locations is empty)
        print "HIT!"
    elif(board[location[0]][location[1]] != "O"):
        print "You already guessed that location!"
    else:
        board[location[0]][location[1]] = "X"
        print "MISS!"

def guess_location(board):
    """Asks for guesses, checks validity (on board vs not),
    if not valid, alerts user
    if valid, calls guess_valid_location to find hit or miss"""
    guess_row = int(raw_input("Guess the row: 0 to " + str(len(board)-1) + ". "))
    guess_col = int(raw_input("Guess the column: 0 to " + str(len(board[0])-1) + ". "))
    if((guess_row < 0 or guess_row > len(board)-1) or (guess_col < 0 or guess_col > len(board[0])-1)):
        print "That's not even in the ocean."
    else:
        guess_valid_location([guess_row, guess_col])
    
def determine_total_number_of_turns(board_size, num_of_ships):
    """Determines the total number of turns the player can have
can depend on size of board and number of ships, 
have not found good formula for this yet"""
    return num_of_ships + 2 

def game_play():
    """All of game play,
from choosing game size and number of battleships, to winning or losing"""
    game_over = False
    turns = 0

    print "Welcome to Battleship!"
    board_size = int(raw_input("Choose a size for the square board: "))
    board_size = populate_board(board_size) # make sure to not worry about invalid board size
    number_of_ships = int(raw_input("How many ships do you want to try to find? "))  

    if(number_of_ships < 1 or number_of_ships > board_size**2):
        print "Invalid number of ships. Let's try " + suggested_number_of_ships
        number_of_ships = suggested_number_of_ships

    generate_hidden_ships(number_of_ships)
    total_number_of_turns = determine_total_number_of_turns(board_size, number_of_ships)

    print "Here is the board.  Choose wisely! You have " + str(total_number_of_turns - turns) + " turns."
    print_board(board)
    while(not game_over):
        if (len(ship_locations) == 0):
            print "You won!" 
            game_over = True
    
        else:
            guess_location(board)
            turns += 1
            print "You have " + str(total_number_of_turns - turns) + " turns left. Here's the board for you: "
            print 
            print_board(board)
            print 

game_play()
