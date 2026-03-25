# connect-4-game-using-python
import pygame
import numpy as np
import sys

pygame.init()

# Constants
CYAN = (0, 255, 255)
BLACK = (0, 0, 0)
PINK = (255, 192, 203)
PURPLE = (128, 0, 128)
WHITE = (255, 255, 255)

# game dimensions
ROW_COUNT = 6
COLUMN_COUNT = 7
SQUARESIZE = 100
RADIUS = int(SQUARESIZE / 2 - 5)  # radius of the piece circles

# screen dimensions
width = COLUMN_COUNT * SQUARESIZE
height = (ROW_COUNT + 1) * SQUARESIZE
size = (width, height)
screen = pygame.display.set_mode(size)

# font
myfont = pygame.font.SysFont("monospace", 75)
font_title = pygame.font.SysFont("monospace", 50)


def create_board():
    """Create an empty new game board."""
    return np.zeros((ROW_COUNT, COLUMN_COUNT))


def drop_piece(board, row, col, piece):
    """Drop a piece into the board at the specified row and column."""
    board[row][col] = piece


def is_valid_location(board, col):
    """Check if the column is a valid location to drop a piece."""
    for r in range(ROW_COUNT):
        if board[r][col] == 0:
            return True
    return False


def get_next_open_row(board, col):
    """find the next open row in the specified column."""
    for r in range(ROW_COUNT - 1, -1, -1):
        if board[r][col] == 0:
            return r


def print_board(board):
    """Print the game board to the console."""
    print(board)


def winning_move(board, piece):

    # check horizontal locations for win
    for c in range(COLUMN_COUNT - 3):
        for r in range(ROW_COUNT):
            if board[r][c] == piece and board[r][c + 1] == piece and board[r][c + 2] == piece and board[r][c + 3] == piece:
                return True

    # check vertical locations for win
    for c in range(COLUMN_COUNT):
        for r in range(ROW_COUNT - 3):
            if board[r][c] == piece and board[r + 1][c] == piece and board[r + 2][c] == piece and board[r + 3][c] == piece:
                return True

    # check positively sloped diagonals
    for c in range(COLUMN_COUNT - 3):
        for r in range(ROW_COUNT - 3):
            if board[r][c] == piece and board[r + 1][c + 1] == piece and board[r + 2][c + 2] == piece and board[r + 3][c + 3] == piece:
                return True

    # check negatively sloped diagonals
    for c in range(COLUMN_COUNT - 3):
        for r in range(3, ROW_COUNT):
            if board[r][c] == piece and board[r - 1][c + 1] == piece and board[r - 2][c + 2] == piece and board[r - 3][c + 3] == piece:
                return True

    return False

def draw_board(board, title=None):
    # draw the board and pieces
    for c in range(COLUMN_COUNT):
        for r in range(ROW_COUNT):
            # row 0 at top, row 5 at bottom
            y_rect = r * SQUARESIZE + SQUARESIZE
            pygame.draw.rect(screen, CYAN, (c * SQUARESIZE, y_rect, SQUARESIZE, SQUARESIZE))

            y = r * SQUARESIZE + SQUARESIZE + SQUARESIZE / 2

            if board[r][c] == 0:
                pygame.draw.circle(screen, BLACK, (int(c * SQUARESIZE + SQUARESIZE / 2), int(y)), RADIUS)

            elif board[r][c] == 1:
                pygame.draw.circle(screen, PURPLE, (int(c * SQUARESIZE + SQUARESIZE / 2), int(y)), RADIUS)

            elif board[r][c] == 2:
                pygame.draw.circle(screen, PINK, (int(c * SQUARESIZE + SQUARESIZE / 2), int(y)), RADIUS)

    #redraw title after drawing board
    if title:
        screen.blit(title, (width // 2 - title.get_width() // 2, 10))

    pygame.display.update()


def main():
    """main game loop."""
    board = create_board()
    game_over = False
    turn = 0

    # Draw title
    title = font_title.render("Connect 4", 1, WHITE)
    
    screen.fill(BLACK)
    draw_board(board, title)

    while not game_over:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                sys.exit()

            if event.type == pygame.MOUSEMOTION:
                pygame.draw.rect(screen, BLACK, (0, 0, width, SQUARESIZE))
                screen.blit(title, (width // 2 - title.get_width() // 2, 10))
                
                posx = event.pos[0]
                if turn == 0:
                    pygame.draw.circle(
                        screen, PURPLE, (posx, int(SQUARESIZE / 2)), RADIUS)
                else:
                    pygame.draw.circle(
                        screen, PINK, (posx, int(SQUARESIZE / 2)), RADIUS)
            pygame.display.update()

            if event.type == pygame.MOUSEBUTTONDOWN:
                pygame.draw.rect(screen, BLACK, (0, 0, width, SQUARESIZE))
                # Redraw title
                screen.blit(title, (width // 2 - title.get_width() // 2, 10))

                posx = event.pos[0]
                col = int(posx / SQUARESIZE)

                if 0 <= col < COLUMN_COUNT and is_valid_location(board, col):
                    row = get_next_open_row(board, col)

                    if turn == 0:
                        drop_piece(board, row, col, 1)

                        if winning_move(board, 1):
                            label = myfont.render("Player 1 wins!!", 1, PURPLE)
                            screen.blit(label, (40, 10))
                            game_over = True

                    else:
                        drop_piece(board, row, col, 2)

                        if winning_move(board, 2):
                            label = myfont.render("Player 2 wins!!", 1, PINK)
                            screen.blit(label, (40, 10))
                            game_over = True

                    print_board(board)
                    draw_board(board, title)

                    turn += 1
                    turn = turn % 2

                    if game_over:
                        pygame.time.wait(3000)


if __name__ == "__main__":
    main()
