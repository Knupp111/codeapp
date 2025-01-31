README.md
import pygame
import sys

# Initialize pygame
pygame.init()

# Screen dimensions
SCREEN_WIDTH = 800
SCREEN_HEIGHT = 600
screen = pygame.display.set_mode((SCREEN_WIDTH, SCREEN_HEIGHT))
pygame.display.set_caption("Simple Mario Game")

# Colors
BLACK = (0, 0, 0)
WHITE = (255, 255, 255)
RED = (255, 0, 0)

# Mario character
class Mario:
    def __init__(self, x, y):
        self.x = x
        self.y = y
        self.width = 40
        self.height = 60
        self.velocity_y = 0
        self.jumping = False

    def draw(self, surface):
        pygame.draw.rect(surface, RED, (self.x, self.y, self.width, self.height))

    def jump(self):
        if not self.jumping:
            self.jumping = True
            self.velocity_y = -15

    def update(self):
        if self.jumping:
            self.velocity_y += 0.8
            self.y += self.velocity_y
            if self.y > SCREEN_HEIGHT - self.height:
                self.y = SCREEN_HEIGHT - self.height
                self.jumping = False
                self.velocity_y = 0

# Obstacle (Goomba-like)
class Goomba:
    def __init__(self, x, y):
        self.x = x
        self.y = y
        self.width = 30
        self.height = 30

    def draw(self, surface):
        pygame.draw.rect(surface, BLACK, (self.x, self.y, self.width, self.height))

    def move(self):
        self.x -= 2
        if self.x < -self.width:
            self.x = SCREEN_WIDTH

# Create Mario and Goomba
mario = Mario(50, SCREEN_HEIGHT - 60)
goomba = Goomba(SCREEN_WIDTH, SCREEN_HEIGHT - 30)

# Game loop
clock = pygame.time.Clock()
running = True

while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_SPACE:
                mario.jump()

    keys = pygame.key.get_pressed()
    if keys[pygame.K_LEFT] and mario.x > 0:
        mario.x -= 5
    if keys[pygame.K_RIGHT] and mario.x < SCREEN_WIDTH - mario.width:
        mario.x += 5

    mario.update()
    goomba.move()

    # Check for collision
    if (mario.x < goomba.x + goomba.width and
        mario.x + mario.width > goomba.x and
        mario.y < goomba.y + goomba.height and
        mario.y + mario.height > goomba.y):
        print("Game Over!")
        running = False

    # Drawing
    screen.fill(WHITE)
    mario.draw(screen)
    goomba.draw(screen)
    pygame.display.flip()

    clock.tick(30)

pygame.quit()
sys.exit()