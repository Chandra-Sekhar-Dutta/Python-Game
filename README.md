<!DOCTYPE html>
<html>
<head>
   
</head>
<body>

<h1>Python-Game</h1>

<p>Welcome to the Python-Game repository. This project demonstrates a simple game developed using Python and Pygame. The game features a monster that can move around, collect enemies for points, and avoid bombs. Below are the details and instructions for the repository.</p>

<h2>Table of Contents</h2>
<ul>
    <li><a href="#install-pygame">Install Pygame</a></li>
    <li><a href="#game-code">Game Code</a></li>
    <li><a href="#assets">Assets</a></li>
    <li><a href="#repository-link">Repository Link</a></li>
    <li><a href="#contact">Contact</a></li>
</ul>

<h2 id="install-pygame">Install Pygame</h2>
<p>To run this game on your local machine, you need to have Python and Pygame installed. Follow the steps below to install Pygame:</p>
<ol>
    <li>Ensure you have Python installed. You can download it from <a href="https://www.python.org/downloads/" target="_blank">python.org</a>.</li>
    <li>Open your command prompt (Windows) or terminal (Mac/Linux).</li>
    <li>Install Pygame by running the following command:</li>
</ol>
<pre><code>pip install pygame</code></pre>
<ol>
    <li>Once Pygame is installed, you can run the game by navigating to the directory containing the game code and executing:</li>
</ol>
<pre><code>python your_game_file.py</code></pre>

<h2 id="game-code">Game Code</h2>
<p>The main game logic is implemented in the Python file. Below is the code:</p>
<pre><code>
import pygame
import random

# Initialize Pygame
pygame.init()

# Create the screen
screen = pygame.display.set_mode((800, 600))

# Background
background = pygame.image.load('space background.png')

# Game name and game logo
pygame.display.set_caption("Space Game")
icon = pygame.image.load('ufo.png')
pygame.display.set_icon(icon)

# Player icon and its attributes
monsterImg = pygame.image.load('monster.png')
monster = [370, 480]  # monster position stored in a list
monsterX_change = 0
monsterY_change = 0
monster_last_moved = pygame.time.get_ticks()  # Initialize the last moved time

# Bomb icon
BombImg = pygame.image.load('bomb.png')
BombX = random.randint(0, 770)
BombY = 50
BombY_change = 0.4

# Enemy icon and its attributes
enemyImg = pygame.image.load('space-food.png')

# Create a list to store information about each enemy
enemies = []
num_enemies = 5

for _ in range(num_enemies):
    enemyX = random.randint(0, 770)
    enemyY = random.randint(50, 550)
    enemyX_change = 0.3  # Set an initial value for enemyX_change
    enemyY_change = 40  # Adjusted value for enemyY_change
    enemies.append([enemyX, enemyY, enemyX_change, enemyY_change])

score = 0

# Score display settings
font = pygame.font.Font('freesansbold.ttf', 32)
textX = 10
textY = 10

# Game over font settings
game_over_font = pygame.font.Font('freesansbold.ttf', 64)

game_over = False  # Initialize the game_over variable

def showScore(x, y):
    score_text = font.render("Score: " + str(score), True, (255, 255, 255))
    screen.blit(score_text, (x, y))

def showGameover():
    game_over_text = game_over_font.render("Game Over", True, (255, 0, 0))
    screen.blit(game_over_text, (200, 250))

def showMonsterDies():
    monster_dies_text = game_over_font.render("Monster Dies", True, (255, 0, 0))
    screen.blit(monster_dies_text, (200, 250))

def Monster(x, y):
    screen.blit(monsterImg, (x, y))  # blit() draws the required image on the screen

def Enemy(x, y):
    screen.blit(enemyImg, (x, y))

def BOMB(x, y):
    screen.blit(BombImg, (x, y))

def collision(enemy, monster, bomb):
    global score, monster_last_moved, game_over  # Declare score, monster_last_moved, and game_over as global
    # Check for collision between monster and enemy (food)
    distance = ((monster[0] - enemy[0])**2 + (monster[1] - enemy[1])**2)**0.5
    if distance < 20:  # You can adjust this value based on your images and desired collision area
        enemy[0] = random.randint(0, 770)
        enemy[1] = random.randint(150, 550)
        score += 1

    # Check for collision between monster and bomb
    bomb_distance = ((monster[0] - bomb[0])**2 + (monster[1] - bomb[1])**2)**0.5
    if bomb_distance < 30:  # You can adjust this value based on your images and desired collision area
        showMonsterDies()  # Display "Monster Dies" message
        pygame.display.update()
        pygame.time.delay(1000)  # Pause for 1 second
        monster_last_moved = pygame.time.get_ticks()  # Reset the timer
        monster[0] = random.randint(0, 736)
        monster[1] = random.randint(0, 536)
        score = 0

    # Check if the monster has been stationary for 5 seconds
    current_time = pygame.time.get_ticks()
    if current_time - monster_last_moved >= 5000:
        monster[0] = random.randint(0, 736)
        monster[1] = random.randint(0, 536)
        monster_last_moved = current_time

# Set up the main loop to keep the game running (game loop)
while not game_over:
    screen.fill((0, 0, 0))
    
    # Background image persists here
    screen.blit(background, (0, 0))

    # Check for events
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            game_over = True  # Exit the loop when the window is closed
        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_LEFT:
                monsterX_change = -0.5
                monster_last_moved = pygame.time.get_ticks()  # Reset the timer on key press
            if event.key == pygame.K_RIGHT:
                monsterX_change = 0.5
                monster_last_moved = pygame.time.get_ticks()
            if event.key == pygame.K_UP:
                monsterY_change = -0.5
                monster_last_moved = pygame.time.get_ticks()
            if event.key == pygame.K_DOWN:
                monsterY_change = 0.5
                monster_last_moved = pygame.time.get_ticks()
        if event.type == pygame.KEYUP:
            if event.key == pygame.K_LEFT or event.key == pygame.K_RIGHT:
                monsterX_change = 0
            if event.key == pygame.K_UP or event.key == pygame.K_DOWN:
                monsterY_change = 0

    monster[0] += monsterX_change
    monster[1] += monsterY_change
    
    # Boundaries for the player
    if monster[0] <= 0:
        monster[0] = 0
    if monster[0] >= 736:
        monster[0] = 736
    if monster[1] <= 0:
        monster[1] = 0
    if monster[1] >= 536:
        monster[1] = 536

    # Update and render the bomb
    BombY += BombY_change
    if BombY > 600:  # If bomb goes below the screen, reset its position
        BombY = 50
        BombX = random.randint(0, 770)

    BOMB(BombX, BombY)

    # Update and render the enemies
    for enemy in enemies:
        enemy[0] += enemy[2]  # Update the enemy position (you can adjust the speed)
        if enemy[0] <= 0 or enemy[0] >= 770:
            enemy[2] = -enemy[2]  # Reverse the direction if the enemy hits the boundaries
            enemy[1] += enemy[3]
        if enemy[1] >= 550:
            enemy[1] = 24

        collision(enemy, monster, (BombX, BombY))
        Enemy(enemy[0], enemy[1])

    Monster(monster[0], monster[1])
    showScore(textX, textY)  # Display the score on the screen
    pygame.display.update()

# Game over screen
showGameover()
pygame.display.update()
pygame.time.delay(500)  # Pause for 2 seconds
pygame.quit()
</code></pre>

<h2 id="assets">Assets</h2>
<p>The game uses the following assets:</p>
<ul>
    <li>bomb.png</li>
    <li>monster.png</li>
    <li>space background.png</li>
    <li>space-food.png</li>
    <li>ufo.png</li>
</ul>

<h2 id="repository-link">Repository Link</h2>
<p>You can find the repository at: <a href="https://github.com/Chandra-Sekhar-Dutta/Python-Game/tree/main" target="_blank">https://github.com/Chandra-Sekhar-Dutta/Python-Game/tree/main</a></p>

<h2 id="contact">Contact</h2>
<p>If you have any questions or feedback, feel free to contact me at: <a href="mailto:chandrasekhardutta3@gmail.com">chandrasekhardutta3@gmail.com</a></p>

</body>
</html>
