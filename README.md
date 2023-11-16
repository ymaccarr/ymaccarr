import pygame
import random

# Inicialización de Pygame
pygame.init()

# Parámetros del juego
WIDTH, HEIGHT = 300, 600
GRID_SIZE = 30
FPS = 30

# Colores
BLACK = (0, 0, 0)
WHITE = (255, 255, 255)
RED = (255, 0, 0)
CYAN = (0, 255, 255)
MAGENTA = (255, 0, 255)
YELLOW = (255, 255, 0)
GREEN = (0, 255, 0)
BLUE = (0, 0, 255)
ORANGE = (255, 165, 0)

# Definición de las piezas y sus colores
SHAPES = [
    [[1, 1, 1, 1]],  # I
    [[1, 1, 1], [1]],  # L
    [[1, 1, 1], [0, 1]],  # J
    [[1, 1], [1, 1]],  # O
    [[1, 1, 1], [1, 0]],  # S
    [[1, 1, 1], [0, 1]],  # T
    [[1, 1, 1], [0, 0, 1]]  # Z
]

SHAPES_COLORS = [CYAN, ORANGE, BLUE, YELLOW, GREEN, MAGENTA, RED]

# Clase para representar la pieza actual
class Piece:
    def __init__(self, shape, color):
        self.shape = shape
        self.color = color
        self.row = 0
        self.col = WIDTH // (2 * GRID_SIZE) - len(shape[0]) // 2

    def move_down(self):
        self.row += 1

    def move_left(self):
        self.col -= 1

    def move_right(self):
        self.col += 1

    def rotate(self):
        self.shape = list(zip(*self.shape[::-1]))

# Función para dibujar la cuadrícula
def draw_grid(screen):
    for i in range(0, WIDTH, GRID_SIZE):
        pygame.draw.line(screen, WHITE, (i, 0), (i, HEIGHT))
    for j in range(0, HEIGHT, GRID_SIZE):
        pygame.draw.line(screen, WHITE, (0, j), (WIDTH, j))

# Función para dibujar una pieza en la pantalla
def draw_piece(screen, piece):
    for i, row in enumerate(piece.shape):
        for j, cell in enumerate(row):
            if cell:
                pygame.draw.rect(screen, piece.color, (piece.col * GRID_SIZE + j * GRID_SIZE,
                                                       piece.row * GRID_SIZE + i * GRID_SIZE,
                                                       GRID_SIZE, GRID_SIZE))

# Función principal del juego
def main():
    screen = pygame.display.set_mode((WIDTH, HEIGHT))
    pygame.display.set_caption("Tetris")
    clock = pygame.time.Clock()

    # Inicialización de variables
    current_piece = Piece(random.choice(SHAPES), random.choice(SHAPES_COLORS))
    grid = [[0] * (WIDTH // GRID_SIZE) for _ in range(HEIGHT // GRID_SIZE)]

    running = True
    while running:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                running = False
            elif event.type == pygame.KEYDOWN:
                if event.key == pygame.K_LEFT:
                    current_piece.move_left()
                elif event.key == pygame.K_RIGHT:
                    current_piece.move_right()
                elif event.key == pygame.K_DOWN:
                    current_piece.move_down()
                elif event.key == pygame.K_UP:
                    current_piece.rotate()

        # Lógica del juego
        if current_piece.row + len(current_piece.shape) > len(grid):
            current_piece = Piece(random.choice(SHAPES), random.choice(SHAPES_COLORS))
        else:
            current_piece.move_down()

        # Actualización de la cuadrícula
        grid = [[0] * (WIDTH // GRID_SIZE) for _ in range(HEIGHT // GRID_SIZE)]
        for i, row in enumerate(current_piece.shape):
            for j, cell in enumerate(row):
                if cell:
                    grid[current_piece.row + i][current_piece.col + j] = 1

        # Renderización
        screen.fill(BLACK)
        draw_grid(screen)
        draw_piece(screen, current_piece)
        pygame.display.flip()

        clock.tick(FPS)

    pygame.quit()

if __name__ == "__main__":
    main()
