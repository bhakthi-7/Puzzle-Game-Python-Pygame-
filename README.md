import pygame
import random
import time

# Initialize Pygame
pygame.init()

# Constants
WIDTH, HEIGHT = 800, 600
GRID_SIZE = 4
CELL_SIZE = WIDTH // GRID_SIZE
WHITE = (255, 255, 255)
GRAY = (200, 200, 200)
BLACK = (0, 0, 0)
GREEN = (0, 255, 0)

# Set up the screen
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Sliding Puzzle Game")

# Load font
font = pygame.font.Font(None, 36)

def create_puzzle():
    numbers = list(range(1, GRID_SIZE * GRID_SIZE))
    random.shuffle(numbers)
    numbers.append(0)  # 0 represents the empty cell
    grid = [numbers[i:i + GRID_SIZE] for i in range(0, GRID_SIZE * GRID_SIZE, GRID_SIZE)]
    return grid

def find_empty(grid):
    for r, row in enumerate(grid):
        for c, cell in enumerate(row):
            if cell == 0:
                return r, c

def is_valid_move(grid, row, col, move_row, move_col):
    return 0 <= move_row < GRID_SIZE and 0 <= move_col < GRID_SIZE

def make_move(grid, row, col, move_row, move_col):
    grid[row][col], grid[move_row][move_col] = grid[move_row][move_col], grid[row][col]

def is_solved(grid):
    expected = list(range(1, GRID_SIZE * GRID_SIZE))
    expected.append(0)
    actual = [cell for row in grid for cell in row]
    return actual == expected

def draw_grid(grid):
    for r, row in enumerate(grid):
        for c, cell in enumerate(row):
            x, y = c * CELL_SIZE, r * CELL_SIZE
            pygame.draw.rect(screen, GRAY, (x, y, CELL_SIZE, CELL_SIZE), 0)
            pygame.draw.rect(screen, BLACK, (x, y, CELL_SIZE, CELL_SIZE), 1)
            if cell != 0:
                text = font.render(str(cell), True, BLACK)
                text_rect = text.get_rect(center=(x + CELL_SIZE // 2, y + CELL_SIZE // 2))
                screen.blit(text, text_rect)

def main():
    grid = create_puzzle()
    moves = 0
    start_time = time.time()

    running = True
    while running:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                running = False
            if event.type == pygame.MOUSEBUTTONDOWN:
                mouse_x, mouse_y = pygame.mouse.get_pos()
                col = mouse_x // CELL_SIZE
                row = mouse_y // CELL_SIZE
                empty_row, empty_col = find_empty(grid)

                if (abs(row - empty_row) + abs(col - empty_col)) == 1:
                    make_move(grid, empty_row, empty_col, row, col)
                    moves += 1

        screen.fill(WHITE)
        draw_grid(grid)

        if is_solved(grid):
            elapsed_time = time.time() - start_time
            text = font.render(f"Solved! Moves: {moves}, Time: {elapsed_time:.2f}s", True, GREEN)
            text_rect = text.get_rect(center=(WIDTH // 2, HEIGHT - 50))
            screen.blit(text, text_rect)
            pygame.display.flip()
            pygame.time.wait(3000) #3 second delay
            running = False

        pygame.display.flip()

    pygame.quit()

if __name__ == "__main__":
    main()
