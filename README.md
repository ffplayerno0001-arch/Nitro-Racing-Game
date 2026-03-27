import pygame
import random

# 1. Initialize Pygame
pygame.init()

# 2. Setup Screen
WIDTH, HEIGHT = 400, 600
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Car Racing Game")

# Colors
GRAY = (50, 50, 50)
WHITE = (255, 255, 255)
RED = (200, 0, 0)
BLUE = (0, 0, 255)
YELLOW = (255, 255, 0)

# Game Variables
player_width = 50
player_height = 80
player_x = WIDTH // 2 - player_width // 2
player_y = HEIGHT - 100
player_speed = 5

enemy_width = 50
enemy_height = 80
enemy_x = random.randint(50, WIDTH - 100)
enemy_y = -100
enemy_speed = 7

score = 0
font = pygame.font.SysFont("Arial", 30)

clock = pygame.time.Clock()
running = True

# 3. Game Loop
while running:
    screen.fill(GRAY) # Draw Road
    
    # Check for events
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

    # 4. Movement / Logic
    keys = pygame.key.get_pressed()
    if keys[pygame.K_LEFT] and player_x > 50:
        player_x -= player_speed
    if keys[pygame.K_RIGHT] and player_x < WIDTH - 100:
        player_x += player_speed

    # Move Enemy
    enemy_y += enemy_speed
    if enemy_y > HEIGHT:
        enemy_y = -enemy_height
        enemy_x = random.randint(50, WIDTH - 100)
        score += 1
        enemy_speed += 0.2 # Make game harder

    # 5. Collision Detection
    player_rect = pygame.Rect(player_x, player_y, player_width, player_height)
    enemy_rect = pygame.Rect(enemy_x, enemy_y, enemy_width, enemy_height)

    if player_rect.colliderect(enemy_rect):
        print(f"Game Over! Your Score: {score}")
        running = False

    # 6. Drawing
    # Road lines
    pygame.draw.rect(screen, YELLOW, (WIDTH//2 - 5, 0, 10, HEIGHT))
    
    # Draw Player (Blue Car)
    pygame.draw.rect(screen, BLUE, player_rect)
    
    # Draw Enemy (Red Car)
    pygame.draw.rect(screen, RED, enemy_rect)

    # Draw Score
    score_text = font.render(f"Score: {score}", True, WHITE)
    screen.blit(score_text, (10, 10))

    pygame.display.update()
    clock.tick(60) # 60 Frames per second

pygame.quit()
