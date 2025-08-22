import pygame
import sys
import random
import math
from pygame import mixer

# Инициализация pygame
pygame.init()
mixer.init()

# Размеры окна
SCREEN_WIDTH = 1024
SCREEN_HEIGHT = 768
screen = pygame.display.set_mode((SCREEN_WIDTH, SCREEN_HEIGHT))
pygame.display.set_caption("🐾 Мультяшные питомцы - Забота и веселье! 🎮")

# Цвета
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)
BACKGROUND = (240, 248, 255)  # Нежно-голубой фон

# Шрифты
try:
    font_title = pygame.font.Font(None, 48)
    font_large = pygame.font.Font(None, 32)
    font_medium = pygame.font.Font(None, 24)
    font_small = pygame.font.Font(None, 18)
except:
    font_title = pygame.font.SysFont('arial', 48)
    font_large = pygame.font.SysFont('arial', 32)
    font_medium = pygame.font.SysFont('arial', 24)
    font_small = pygame.font.SysFont('arial', 18)

class MuzzleAnimation:
    """Класс для мультяшной мордочки с эмоциями"""
    def __init__(self, pet_type):
        self.pet_type = pet_type
        self.emotion = "happy"
        self.animation_frame = 0
        self.blink_timer = 0
        self.blinking = False
        
    def update(self):
        self.animation_frame += 0.1
        self.blink_timer += 1
        
        if self.blink_timer > 120 and not self.blinking:
            if random.random() < 0.1:
                self.blinking = True
                self.blink_timer = 0
        elif self.blinking and self.blink_timer > 5:
            self.blinking = False

    def draw(self, surface, x, y, size=1.0):
        if self.pet_type == "cat":
            base_color = (255, 150, 100)
            ear_color = (255, 120, 80)
        elif self.pet_type == "dog":
            base_color = (200, 150, 100)
            ear_color = (180, 130, 90)
        else:
            base_color = (100, 200, 255)
            ear_color = base_color

        head_radius = int(40 * size)
        pygame.draw.circle(surface, base_color, (x, y), head_radius)
        
        if self.pet_type in ["cat", "dog"]:
            pygame.draw.polygon(surface, ear_color, [
                (x - 25*size, y - 30*size),
                (x - 15*size, y - 50*size),
                (x - 5*size, y - 35*size)
            ])
            pygame.draw.polygon(surface, ear_color, [
                (x + 25*size, y - 30*size),
                (x + 15*size, y - 50*size),
                (x + 5*size, y - 35*size)
            ])

        eye_y = y - 10*size
        left_eye_x = x - 15*size
        right_eye_x = x + 15*size
        
        if self.blinking:
            pygame.draw.line(surface, BLACK, (left_eye_x-8*size, eye_y), (left_eye_x+8*size, eye_y), 2)
            pygame.draw.line(surface, BLACK, (right_eye_x-8*size, eye_y), (right_eye_x+8*size, eye_y), 2)
        else:
            pygame.draw.circle(surface, WHITE, (left_eye_x, eye_y), int(10*size))
            pygame.draw.circle(surface, WHITE, (right_eye_x, eye_y), int(10*size))
            
            pupil_offset = math.sin(self.animation_frame) * 3*size
            pygame.draw.circle(surface, BLACK, (left_eye_x + pupil_offset, eye_y), int(5*size))
            pygame.draw.circle(surface, BLACK, (right_eye_x + pupil_offset, eye_y), int(5*size))

        mouth_y = y + 15*size
        if self.emotion == "happy":
            pygame.draw.arc(surface, BLACK, (x-15*size, mouth_y-5*size, 30*size, 20*size), 
                          math.radians(0), math.radians(180), 2)
        elif self.emotion == "sad":
            pygame.draw.arc(surface, BLACK, (x-15*size, mouth_y+5*size, 30*size, 20*size), 
                          math.radians(180), math.radians(360), 2)
        elif self.emotion == "eating":
            chew_offset = math.sin(self.animation_frame * 3) * 2*size
            pygame.draw.ellipse(surface, (255, 200, 100), 
                             (x-8*size, mouth_y + chew_offset, 16*size, 8*size))
        elif self.emotion == "playing":
            pygame.draw.circle(surface, BLACK, (x, mouth_y), int(5*size))

class CartoonPet:
    """Мультяшный питомец с продвинутой анимацией"""
    def __init__(self, name, pet_type, x, y):
        self.name = name
        self.pet_type = pet_type
        self.x = x
        self.y = y
        self.hunger = 5
        self.mood = 5
        self.tank_cleanliness = 5 if pet_type == "fish" else 0
        self.muzzle = MuzzleAnimation(pet_type)
        self.animation_frame = 0
        self.bounce_offset = 0
        
    def update(self):
        self.animation_frame += 0.1
        self.bounce_offset = math.sin(self.animation_frame) * 5
        self.muzzle.update()
        
        if self.hunger > 7 or self.mood < 3:
            self.muzzle.emotion = "sad"
        else:
            self.muzzle.emotion = "happy"

    def draw(self, surface):
        y_pos = self.y + self.bounce_offset
        
        if self.pet_type == "cat":
            self.draw_cat(surface, y_pos)
        elif self.pet_type == "dog":
            self.draw_dog(surface, y_pos)
        else:
            self.draw_fish(surface, y_pos)
        
        self.muzzle.draw(surface, self.x, y_pos - 20)
        self.draw_status(surface, y_pos)

    def draw_cat(self, surface, y_pos):
        body_color = (255, 150, 100)
        pygame.draw.ellipse(surface, body_color, (self.x - 40, y_pos, 80, 40))
        
        tail_wiggle = math.sin(self.animation_frame * 2) * 15
        pygame.draw.arc(surface, body_color, (self.x - 70, y_pos - 20, 40, 60),
                      math.radians(180), math.radians(180 + tail_wiggle), 10)

    def draw_dog(self, surface, y_pos):
        body_color = (200, 150, 100)
        pygame.draw.ellipse(surface, body_color, (self.x - 45, y_pos, 90, 45))
        
        tail_wag = math.sin(self.animation_frame * 3) * 20
        points = [
            (self.x - 50, y_pos + 20),
            (self.x - 70, y_pos + 10 + tail_wag),
            (self.x - 60, y_pos - 10)
        ]
        pygame.draw.polygon(surface, body_color, points)

    def draw_fish(self, surface, y_pos):
        body_color = (100, 200, 255)
        fish_wiggle = math.sin(self.animation_frame * 4) * 3
        
        body_points = [
            (self.x - 30 + fish_wiggle, y_pos),
            (self.x + 30 + fish_wiggle, y_pos),
            (self.x + 20 + fish_wiggle, y_pos + 15),
            (self.x - 20 + fish_wiggle, y_pos + 15)
        ]
        pygame.draw.polygon(surface, body_color, body_points)
        
        pygame.draw.polygon(surface, body_color, [
            (self.x + fish_wiggle, y_pos),
            (self.x + 15 + fish_wiggle, y_pos - 20),
            (self.x + 30 + fish_wiggle, y_pos)
        ])

    def draw_status(self, surface, y_pos):
        name_text = font_medium.render(self.name, True, BLACK)
        surface.blit(name_text, (self.x - name_text.get_width()//2, y_pos - 60))
        
        bar_width = 60
        pygame.draw.rect(surface, (200, 200, 200), (self.x - bar_width//2, y_pos + 50, bar_width, 8))
        pygame.draw.rect(surface, (255, 100, 100), (self.x - bar_width//2, y_pos + 50, bar_width * (self.hunger/10), 8))
        
        pygame.draw.rect(surface, (200, 200, 200), (self.x - bar_width//2, y_pos + 62, bar_width, 8))
        pygame.draw.rect(surface, (255, 255, 100), (self.x - bar_width//2, y_pos + 62, bar_width * (self.mood/10), 8))
        
        if self.pet_type == "fish":
            pygame.draw.rect(surface, (200, 200, 200), (self.x - bar_width//2, y_pos + 74, bar_width, 8))
            pygame.draw.rect(surface, (100, 200, 255), (self.x - bar_width//2, y_pos + 74, bar_width * (self.tank_cleanliness/10), 8))

    def feed(self):
        if self.hunger > 0:
            self.hunger = max(0, self.hunger - 2)
            self.muzzle.emotion = "eating"
            return f"{self.name} с удовольствием поел(а)!"
        return f"{self.name} уже не голоден(а)!"

    def play(self):
        if self.mood < 10:
            self.mood = min(10, self.mood + 2)
            self.hunger = min(10, self.hunger + 1)
            self.muzzle.emotion = "playing"
            return f"Вы поиграли с {self.name}!"
        return f"{self.name} уже счастлив(а)!"

    def clean_tank(self):
        if self.pet_type == "fish":
            if self.tank_cleanliness < 10:
                self.tank_cleanliness = 10
                self.mood = min(10, self.mood + 1)
                return "Аквариум теперь чист!"
            return "Аквариум уже чист!"
        return "Только для рыбок!"

    def pass_time(self):
        self.hunger = min(10, self.hunger + 0.05)
        self.mood = max(0, self.mood - 0.03)
        if self.pet_type == "fish":
            self.tank_cleanliness = max(0, self.tank_cleanliness - 0.02)

class BubbleEffect:
    """Эффект пузырьков для сообщений"""
    def __init__(self):
        self.bubbles = []
        
    def add_bubble(self, x, y, text, color=(255, 255, 200)):
        self.bubbles.append({
            'x': x, 'y': y,
            'text': text,
            'color': color,
            'life': 90,
            'offset': random.uniform(0, 6.28)
        })
        
    def update(self):
        for bubble in self.bubbles[:]:
            bubble['life'] -= 1
            bubble['y'] -= 1.5
            if bubble['life'] <= 0:
                self.bubbles.remove(bubble)
                
    def draw(self, surface):
        for bubble in self.bubbles:
            alpha = min(255, bubble['life'] * 3)
            
            # Пузырек
            s = pygame.Surface((120, 50), pygame.SRCALPHA)
            pygame.draw.ellipse(s, (*bubble['color'], alpha), (0, 0, 120, 50))
            pygame.draw.ellipse(s, (0, 0, 0, alpha), (0, 0, 120, 50), 2)
            
            # Текст
            text_surf = font_small.render(bubble['text'], True, (0, 0, 0, alpha))
            text_rect = text_surf.get_rect(center=(60, 25))
            s.blit(text_surf, text_rect)
            
            wobble = math.sin(pygame.time.get_ticks() * 0.01 + bubble['offset']) * 3
            surface.blit(s, (bubble['x'] - 60 + wobble, bubble['y'] - 25))

class CartoonButton:
    """Мультяшная кнопка с цветами"""
    def __init__(self, x, y, width, height, text, color=(255, 200, 100), hover_color=(255, 180, 80)):
        self.rect = pygame.Rect(x, y, width, height)
        self.text = text
        self.color = color
        self.hover_color = hover_color
        self.is_hovered = False
        self.bounce = 0
        
    def update(self):
        if self.is_hovered:
            self.bounce = math.sin(pygame.time.get_ticks() * 0.01) * 3
        else:
            self.bounce = 0
            
    def draw(self, surface):
        button_rect = self.rect.copy()
        button_rect.y += self.bounce
        
        color = self.hover_color if self.is_hovered else self.color
        pygame.draw.rect(surface, color, button_rect, border_radius=12)
        pygame.draw.rect(surface, BLACK, button_rect, 3, border_radius=12)
        
        text_surf = font_medium.render(self.text, True, BLACK)
        text_rect = text_surf.get_rect(center=button_rect.center)
        surface.blit(text_surf, text_rect)
        
    def check_hover(self, pos):
        self.is_hovered = self.rect.collidepoint(pos)
        return self.is_hovered
        
    def is_clicked(self, pos, event):
        if event.type == pygame.MOUSEBUTTONDOWN and event.button == 1:
            return self.rect.collidepoint(pos)
        return False

class CartoonGame:
    """Основной класс игры"""
    def __init__(self):
        self.pets = []
        self.selected_pet = None
        self.bubbles = BubbleEffect()
        self.message = "Добро пожаловать! Заведите питомца."
        self.message_timer = 0
        
        # Цветные кнопки
        self.buttons = [
            CartoonButton(50, 650, 180, 60, "🐱 Кошка", (255, 200, 150)),
            CartoonButton(250, 650, 180, 60, "🐶 Собака", (200, 200, 255)),
            CartoonButton(450, 650, 180, 60, "🐠 Рыбка", (150, 250, 255)),
            CartoonButton(650, 650, 150, 50, "🍗 Кормить", (255, 150, 150)),
            CartoonButton(810, 650, 150, 50, "🎮 Играть", (150, 255, 150)),
            CartoonButton(650, 710, 150, 50, "✨ Чистить", (150, 200, 255)),
            CartoonButton(810, 710, 150, 50, "📊 Статус", (255, 255, 150))
        ]
        
        self.background = self.create_background()

    def create_background(self):
        bg = pygame.Surface((SCREEN_WIDTH, SCREEN_HEIGHT))
        bg.fill(BACKGROUND)
        
        pygame.draw.circle(bg, (255, 255, 100), (900, 100), 50)
        for i in range(12):
            angle = math.radians(i * 30)
            end_x = 900 + math.cos(angle) * 70
            end_y = 100 + math.sin(angle) * 70
            pygame.draw.line(bg, (255, 255, 100), (900, 100), (end_x, end_y), 3)
        
        for x, y in [(200, 100), (400, 150), (600, 80)]:
            for i in range(3):
                pygame.draw.circle(bg, WHITE, (x + i*30, y), 25)
        
        for x in range(0, SCREEN_WIDTH, 20):
            grass_height = random.randint(10, 30)
            pygame.draw.line(bg, (100, 200, 100), (x, SCREEN_HEIGHT), (x, SCREEN_HEIGHT - grass_height), 2)
        
        return bg

    def show_message(self, text):
        self.message = text
        self.message_timer = 180

    def add_pet(self, pet_type):
        x = random.randint(100, SCREEN_WIDTH - 200)
        y = random.randint(100, 400)
        name = f"{pet_type.capitalize()}{len(self.pets) + 1}"
        
        if pet_type == "cat":
            new_pet = CartoonPet(name, "cat", x, y)
        elif pet_type == "dog":
            new_pet = CartoonPet(name, "dog", x, y)
        else:
            new_pet = CartoonPet(name, "fish", x, y)
            
        self.pets.append(new_pet)
        self.selected_pet = new_pet
        self.show_message(f"Вы завели {name}! 🎉")
        self.bubbles.add_bubble(x, y - 80, "Привет! 🐾", (255, 220, 150))

    def handle_events(self):
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                return False
                
            mouse_pos = pygame.mouse.get_pos()
            
            for button in self.buttons:
                button.check_hover(mouse_pos)
                
                if button.is_clicked(mouse_pos, event):
                    if button.text == "🐱 Кошка":
                        self.add_pet("cat")
                    elif button.text == "🐶 Собака":
                        self.add_pet("dog")
                    elif button.text == "🐠 Рыбка":
                        self.add_pet("fish")
                    elif button.text == "🍗 Кормить" and self.selected_pet:
                        result = self.selected_pet.feed()
                        self.show_message(result)
                        self.bubbles.add_bubble(self.selected_pet.x, self.selected_pet.y - 100, "Ням-ням! 🍖", (255, 200, 150))
                    elif button.text == "🎮 Играть" and self.selected_pet:
                        result = self.selected_pet.play()
                        self.show_message(result)
                        self.bubbles.add_bubble(self.selected_pet.x, self.selected_pet.y - 100, "Ура! 🎲", (200, 255, 150))
                    elif button.text == "✨ Чистить" and self.selected_pet:
                        result = self.selected_pet.clean_tank()
                        self.show_message(result)
                        if "чист" in result.lower():
                            self.bubbles.add_bubble(self.selected_pet.x, self.selected_pet.y - 100, "Чисто! 💧", (150, 200, 255))
                    elif button.text == "📊 Статус" and self.selected_pet:
                        status = f"Голод: {int(self.selected_pet.hunger)}/10\nНастроение: {int(self.selected_pet.mood)}/10"
                        if self.selected_pet.pet_type == "fish":
                            status += f"\nЧистота: {int(self.selected_pet.tank_cleanliness)}/10"
                        self.show_message(status)
            
            # Выбор питомца кликом
            if event.type == pygame.MOUSEBUTTONDOWN:
                for pet in self.pets:
                    if (abs(mouse_pos[0] - pet.x) < 60 and 
                        abs(mouse_pos[1] - (pet.y + pet.bounce_offset)) < 50):
                        self.selected_pet = pet
                        self.show_message(f"Выбран: {pet.name}")
                        self.bubbles.add_bubble(pet.x, pet.y - 80, "Я здесь! 👋", (200, 230, 255))
                        break
                        
        return True

    def update(self):
        for pet in self.pets:
            pet.pass_time()
            pet.update()
            
        for button in self.buttons:
            button.update()
            
        self.bubbles.update()
        
        if self.message_timer > 0:
            self.message_timer -= 1

    def draw(self, surface):
        surface.blit(self.background, (0, 0))
        
        for pet in self.pets:
            pet.draw(surface)
            
        self.bubbles.draw(surface)
        
        for button in self.buttons:
            button.draw(surface)
        
        # Заголовок
        title = font_title.render("🐾 Мультяшные питомцы 🐾", True, (50, 100, 150))
        surface.blit(title, (SCREEN_WIDTH//2 - title.get_width()//2, 20))
        
        # Сообщение
        if self.message_timer > 0:
            msg_lines = self.message.split('\n')
            for i, line in enumerate(msg_lines):
                msg_surf = font_medium.render(line, True, BLACK)
                surface.blit(msg_surf, (20, 580 + i * 25))
        
        # Подсказка
        hint = font_small.render("Кликните на питомца чтобы выбрать его", True, (100, 100, 100))
        surface.blit(hint, (20, 550))

    def run(self):
        clock = pygame.time.Clock()
        running = True
        
        while running:
            running = self.handle_events()
            self.update()
            
            screen.fill(WHITE)
            self.draw(screen)
            
            pygame.display.flip()
            clock.tick(60)

# Запуск игры
if __name__ == "__main__":
    game = CartoonGame()
    game.run()
    pygame.quit()
    sys.exit()
