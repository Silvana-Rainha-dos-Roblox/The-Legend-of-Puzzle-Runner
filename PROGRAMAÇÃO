import math
import random
import pygame
from pygame import mixer

# Inicialização do Pygame
pygame.init()

# Configuração da tela
screen = pygame.display.set_mode((800, 600))

# Background
background = pygame.transform.scale(pygame.image.load('Background.png'), (800, 600))

# Som
mixer.music.load("MBackground.mp3")
mixer.music.play(-1)

# Título e Ícone
pygame.display.set_caption("The Legend of Puzzle: Shooter")
icon = pygame.image.load('Boneco.png')
pygame.display.set_icon(icon)

# Jogador
playerImg = pygame.transform.scale(pygame.image.load('Boneco.png'), (64, 64))
playerX = 370
playerY = 480
playerX_change = 0

# Inimigos
enemyImg = []
enemyX = []
enemyY = []
enemyX_change = []
enemyY_change = []
enemy_size = 256  # Tamanho dos inimigos
num_of_enemies = 5  # Ajustado para evitar sobreposição devido ao tamanho maior

for i in range(num_of_enemies):
    enemyImg.append(pygame.transform.scale(pygame.image.load('Zumbi.png'), (enemy_size, enemy_size)))
    enemyX.append(random.randint(0, 800 - enemy_size))
    enemyY.append(random.randint(50, 150))
    enemyX_change.append(4)
    enemyY_change.append(40)

# Tiro
bulletImg = pygame.transform.scale(pygame.image.load('Tiro.png'), (32, 32))
bulletX = 0
bulletY = 480
bulletY_change = 10
bullet_state = "ready"  # Estados possíveis: "ready" ou "fire"

# Pontuação
score_value = 0
font = pygame.font.Font('freesansbold.ttf', 32)
textX = 10
textY = 10

# Tela de fim de jogo
over_font = pygame.font.Font('freesansbold.ttf', 64)

# Estado do jogo
game_over = False


def show_score(x, y):
    score = font.render("Score : " + str(score_value), True, (255, 255, 255))
    screen.blit(score, (x, y))


def game_over_text():
    over_text = over_font.render("GAME OVER", True, (255, 255, 255))
    screen.blit(over_text, (200, 250))


def player(x, y):
    screen.blit(playerImg, (x, y))


def enemy(x, y, i):
    screen.blit(enemyImg[i], (x, y))


def fire_bullet(x, y):
    global bullet_state
    bullet_state = "fire"
    screen.blit(bulletImg, (x + 16, y + 10))


def isCollision(enemyX, enemyY, bulletX, bulletY):
    # Ajuste para centralizar a colisão no novo tamanho do inimigo
    enemy_centerX = enemyX + enemy_size // 2
    enemy_centerY = enemyY + enemy_size // 2
    bullet_centerX = bulletX + 16
    bullet_centerY = bulletY + 16
    distance = math.sqrt(math.pow(enemy_centerX - bullet_centerX, 2) + math.pow(enemy_centerY - bullet_centerY, 2))
    return distance < (enemy_size // 4)  # Raio de colisão ajustado proporcionalmente


# Loop principal do jogo
running = True
while running:
    screen.fill((0, 0, 0))
    screen.blit(background, (0, 0))

    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

        # Movimentação do jogador
        if event.type == pygame.KEYDOWN and not game_over:
            if event.key == pygame.K_LEFT:
                playerX_change = -5
            if event.key == pygame.K_RIGHT:
                playerX_change = 5
            if event.key == pygame.K_SPACE:
                if bullet_state == "ready":
                    bulletSound = mixer.Sound("MTiro.mp3")
                    bulletSound.set_volume(0.2)  # Diminui o volume do som do tiro
                    bulletSound.play()
                    bulletX = playerX
                    fire_bullet(bulletX, bulletY)

        if event.type == pygame.KEYUP and not game_over:
            if event.key in (pygame.K_LEFT, pygame.K_RIGHT):
                playerX_change = 0

    # Movimentação do jogador (somente se o jogo não acabou)
    if not game_over:
        playerX += playerX_change
        playerX = max(0, min(playerX, 800 - 64))  # Limita a posição do jogador na tela

    # Movimentação dos inimigos
    for i in range(num_of_enemies):
        # Verifica se o jogo acabou
        if enemyY[i] > playerY - 128:  # Verifica se o inimigo chegou muito perto do jogador
            game_over = True
            break

        enemyX[i] += enemyX_change[i]
        if enemyX[i] <= 0:
            enemyX_change[i] = 4
            enemyY[i] += enemyY_change[i]
        elif enemyX[i] >= 800 - enemy_size:
            enemyX_change[i] = -4
            enemyY[i] += enemyY_change[i]

        # Detecta colisão (somente se o jogo não acabou)
        if not game_over:
            collision = isCollision(enemyX[i], enemyY[i], bulletX, bulletY)
            if collision:
                explosionSound = mixer.Sound("MZumbi.mp3")
                explosionSound.play()
                bulletY = 480
                bullet_state = "ready"
                bulletX = -100  # Redefine a posição da bala para fora da tela
                score_value += 1
                enemyX[i] = random.randint(0, 800 - enemy_size)
                enemyY[i] = random.randint(50, 150)

        enemy(enemyX[i], enemyY[i], i)

    # Movimentação do tiro
    if not game_over:
        if bulletY <= 0:
            bulletY = 480
            bullet_state = "ready"
            bulletX = -100  # Redefine a posição da bala para fora da tela

        if bullet_state == "fire":
            fire_bullet(bulletX, bulletY)
            bulletY -= bulletY_change

    # Renderização do jogador e da pontuação
    player(playerX, playerY)
    show_score(textX, textY)

    # Tela de Game Over
    if game_over:
        game_over_text()

    # Atualiza a tela
    pygame.display.update()
