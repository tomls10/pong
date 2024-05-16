# pong
import pygame
import time
import sys

pygame.init()

WIDTH = 800
HEIGHT = 600
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption('My Game')

WHITE = (255, 255, 255)
BLACK = (0, 0, 0)
GREEN = (0, 255, 0)

radius = 10
x = int((WIDTH // 2) - 5)
y = int(radius + 5)

paddle = {"width": 200,
          "height": 20,
          "color": WHITE,
          "x": (WIDTH / 2) - 25,
          "y": (HEIGHT - 25)}

speed = 3
x_sens = y_sens = 1
pause = False
font = pygame.font.Font(None, 50)
countdown = 3
score = 0
best_score = 0
saved_balls = 0

score_font = pygame.font.Font(None, 36)

while countdown > 0:
    screen.fill(BLACK)
    text = font.render("Game start in: {}S".format(countdown), True, GREEN)
    text_rect = text.get_rect(center=(WIDTH // 2, HEIGHT // 2))
    screen.blit(text, text_rect)
    pygame.display.flip()
    time.sleep(1)
    countdown -= 1

while True:
    screen.fill(BLACK)
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            pygame.quit()
            sys.exit()

    key = pygame.key.get_pressed()

    if key[pygame.K_SPACE]:
        pause = True

    if key[pygame.K_RETURN]:
        if pause:
            pause = False
            x = (WIDTH // 2) - 5
            y = radius + 5
            score = 0
            saved_balls = 0
            speed = 3
            paddle["width"] = 200
            paddle["x"] = (WIDTH // 2) - 25

    if not pause:
        if key[pygame.K_LEFT] and paddle["x"] > 0:
            paddle["x"] -= speed

        if key[pygame.K_RIGHT] and paddle["x"] < WIDTH - paddle["width"]:
            paddle["x"] += speed

        if x <= radius or x >= WIDTH - radius:
            x_sens *= -1

        if y <= radius:
            y_sens *= -1

        if y + radius >= paddle["y"]:
            if paddle["x"] <= x <= paddle["x"] + paddle["width"]:
                y_sens *= -1
                score += 1
                saved_balls += 1
                if saved_balls % 5 == 0:
                    speed += 1
                if saved_balls % 10 == 0:
                    paddle["width"] -= 20
                    paddle["x"] += 10
            elif y < paddle["y"]:  # Vérifie si la balle est au-dessus du paddle
                if (paddle["x"] <= x + radius <= paddle["x"] + paddle["width"] or
                        paddle["x"] <= x - radius <= paddle["x"] + paddle["width"]):
                    x_sens *= -1
                    if y < paddle["y"] - radius // 2:  # Ajuste la direction horizontale uniquement si la balle est suffisamment au-dessus du paddle
                        y_sens *= -1
                    score += 1
                    saved_balls += 1
                    if saved_balls % 5 == 0:
                        speed += 1
                    if saved_balls % 10 == 0:
                        paddle["width"] -= 20
                        paddle["x"] += 10

        if y >= HEIGHT:
            pause = True

        x += x_sens * speed
        y += y_sens * speed

    pygame.draw.circle(screen, WHITE, (x, y), radius)
    pygame.draw.rect(screen, paddle["color"], (paddle["x"], paddle["y"], paddle["width"], paddle["height"]))

    score_text = score_font.render("Score: {}".format(score), True, WHITE)
    screen.blit(score_text, (10, 10))

    best_score = max(score, best_score)
    best_score_text = score_font.render("Best Score: {}".format(best_score), True, WHITE)
    screen.blit(best_score_text, (WIDTH - best_score_text.get_width() - 10, 10))

    if pause:
        end_text = font.render("Appuyez sur entré", True, GREEN)
        end_rect = end_text.get_rect(center=(WIDTH // 2, HEIGHT // 2))
        screen.blit(end_text, end_rect)
        your_score_text = font.render("Your score: {}".format(score), True, WHITE)
        your_score_rect = your_score_text.get_rect(center=(WIDTH // 2, HEIGHT // 2 + 50))
        screen.blit(your_score_text, your_score_rect)




    pygame.display.update()
    pygame.time.delay(10)
