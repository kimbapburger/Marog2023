import pygame
import random
import sys

# Initialize Pygame
pygame.init()

# Constants Variables
WIDTH, HEIGHT = 800, 600
WHITE = (255, 255, 255)
FONT = pygame.font.Font(None, 36)

# Game variables
score = 0
game_over = False

# Frog images
frog_happy = pygame.image.load('correct.png')
frog_disappoint = pygame.image.load('wrong.png')

# Main game loop
running = True
clock = pygame.time.Clock() #For Frame Rate Control

# Functions
def generate_question():
    #On easy mode, we set random numbers 1~10
    a = random.randint(1, 10)
    b = random.randint(1, 10)
    operation = random.choice(['+', '-', '*', '/'])
    question = f"{a} {operation} {b}"
    if operation == '/':
        while b == 0:
            b = random.randint(1, 10)
        answer = round(a / b, 2)  # Round up to two decimal places

    else:
        answer = eval(question)
    return f"{question} : ", answer

def display_question(question):
    info_text = FONT.render(f"(You can round up to 2 decimal places) ", True, WHITE)
    condition_text = FONT.render(f"      (Hit 100 Scores for the Froggy!)", True, WHITE)
    text = FONT.render(question, True, WHITE)
    screen.blit(info_text, (20, 60))
    screen.blit(condition_text, (20, 100))
    screen.blit(text, (20, 20))

def display_score(score):
    text = FONT.render(f"Score: {score}", True, WHITE)
    screen.blit(text, (WIDTH - 150, 20))


# Initialize Pygame screen
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption('Marog Math Quiz')

user_answer = ""
question, answer = generate_question()
display_question(question)
display_score(score)

frog_state = "happy"  # Initialize to "happy before the game loop


# Background music
pygame.mixer.init()
pygame.mixer.music.load('music.mp3')
pygame.mixer.music.set_volume(0.5)  # Adjust the volume as needed
pygame.mixer.music.play(-1)  # -1 makes it loop indefinitely

# Frog Sounds when input answer
happy_sound = pygame.mixer.Sound('happysound.mp3')
unhappy_sound = pygame.mixer.Sound('unhappysound.mp3')

happy_sound.set_volume(0.5)  # Adjust the volume as needed
unhappy_sound.set_volume(0.5)  # Adjust the volume as needed

# Cong.jpg load for displaying when the game is ended
congratulations_image = pygame.image.load('cong.jpg')
congratulations_image = pygame.transform.scale(congratulations_image, (500, 300)) # size
congratulations_x = (WIDTH - congratulations_image.get_width()) // 2 # centered
congratulations_y = (HEIGHT - congratulations_image.get_height()) // 2


congratulations_display_time = 600  # Time to display "cong.jpg" in seconds
congratulations_timer = 0


#When quit the game, running should be false, so it will stop running the loops
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_ESCAPE:
                running = False
            elif event.key == pygame.K_RETURN:
                if round(float(user_answer), 2) == round(float(answer), 2):
                    score += 1
                    frog_state = "happy"  # Change the frog state to "happy"
                    print("Correct!")
                    happy_sound.play()
                else:
                    frog_state = "disappoint"  # Change the frog state to "disappoint"
                    print("Wrong!")
                    unhappy_sound.play()
                user_answer = ""
                question, answer = generate_question()
                display_question(question)
                display_score(score)

                if score >= 100:
                    game_over = True
                    congratulations_timer = 0  # Start the timer


            elif event.key == pygame.K_BACKSPACE:
                user_answer = user_answer[:-1]

            else:
                user_answer += event.unicode

    screen.fill((0, 0, 0))
    display_score(score)
    display_question(question)
    user_input = FONT.render(user_answer, True, WHITE)
    screen.blit(user_input, (100, 20))

    if game_over:
        screen.blit(congratulations_image, (congratulations_x, congratulations_y))
        end_text = FONT.render(f"Thanks for playing! Now the Frog is Happy!", True, WHITE)
        screen.blit(end_text, (WIDTH // 5, HEIGHT // 1.2))
        congratulations_timer += 1 / 60  # Update the timer based on the frame rate
        if congratulations_timer >= congratulations_display_time:
            running = False
    else:
        if frog_state == "happy":
            screen.blit(frog_happy, (WIDTH // 4, HEIGHT // 4))
        else:
            screen.blit(frog_disappoint, (WIDTH // 4, HEIGHT // 4))

    pygame.display.flip()

pygame.quit()
