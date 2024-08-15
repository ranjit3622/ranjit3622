import pygame
import random

# Initialize Pygame
pygame.init()

# Screen dimensions
WIDTH, HEIGHT = 800, 600
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Endless Runner")

# Colors
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)
RED = (255, 0, 0)

# Player settings
player_size = 50
player_x = 100
player_y = HEIGHT - player_size - 20
player_speed = 10

# Obstacle settings
obstacle_width = 50
obstacle_height = 50
obstacle_speed = 10
obstacle_frequency = 1500  # milliseconds

# Game settings
clock = pygame.time.Clock()
score = 0

# Load player image
player_img = pygame.Surface((player_size, player_size))
player_img.fill(RED)

# Group for obstacles
obstacles = pygame.sprite.Group()

# Define player class
class Player(pygame.sprite.Sprite):
    def __init__(self):
        super().__init__()
        self.image = player_img
        self.rect = self.image.get_rect()
        self.rect.x = player_x
        self.rect.y = player_y

    def update(self, keys):
        if keys[pygame.K_LEFT] and self.rect.left > 0:
            self.rect.x -= player_speed
        if keys[pygame.K_RIGHT] and self.rect.right < WIDTH:
            self.rect.x += player_speed

# Define obstacle class
class Obstacle(pygame.sprite.Sprite):
    def __init__(self, x, y):
        super().__init__()
        self.image = pygame.Surface((obstacle_width, obstacle_height))
        self.image.fill(BLACK)
        self.rect = self.image.get_rect()
        self.rect.x = x
        self.rect.y = y

    def update(self):
        self.rect.x -= obstacle_speed
        if self.rect.right < 0:
            self.kill()

# Create player instance
player = Player()
all_sprites = pygame.sprite.Group(player)

# Event for adding obstacles
ADD_OBSTACLE = pygame.USEREVENT + 1
pygame.time.set_timer(ADD_OBSTACLE, obstacle_frequency)

# Main game loop
running = True
while running:
    keys = pygame.key.get_pressed()

    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == ADD_OBSTACLE:
            obstacle_y = HEIGHT - obstacle_height - 20
            obstacle = Obstacle(WIDTH, obstacle_y)
            obstacles.add(obstacle)
            all_sprites.add(obstacle)

    # Update sprites
    all_sprites.update(keys)

    # Check for collisions
    if pygame.sprite.spritecollideany(player, obstacles):
        running = False

    # Draw everything
    screen.fill(WHITE)
    all_sprites.draw(screen)

    # Update display
    pygame.display.flip()

    # Maintain frame rate
    clock.tick(30)

# Quit the game
pygame.quit()
