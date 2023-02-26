import pygame
import random

# 定义常量
SCREEN_WIDTH = 640
SCREEN_HEIGHT = 480
CELL_SIZE = 20
SNAKE_COLOR = (0, 255, 0)
FOOD_COLOR = (255, 0, 0)
BG_COLOR = (0, 0, 0)
SNAKE_SPEED = 5

# 初始化Pygame
pygame.init()

# 创建游戏窗口
screen = pygame.display.set_mode((SCREEN_WIDTH, SCREEN_HEIGHT))
pygame.display.set_caption("Snake Game")

# 创建游戏时钟
clock = pygame.time.Clock()


# 定义贪吃蛇类
class Snake:
    def __init__(self):
        self.head = [SCREEN_WIDTH // 2, SCREEN_HEIGHT // 2]
        self.body = [[self.head[0], self.head[1]],
                     [self.head[0] - CELL_SIZE, self.head[1]],
                     [self.head[0] - (2 * CELL_SIZE), self.head[1]]]
        self.direction = "RIGHT"

    def move(self):
        if self.direction == "RIGHT":
            self.head[0] += CELL_SIZE
        elif self.direction == "LEFT":
            self.head[0] -= CELL_SIZE
        elif self.direction == "UP":
            self.head[1] -= CELL_SIZE
        elif self.direction == "DOWN":
            self.head[1] += CELL_SIZE

        self.body.insert(0, list(self.head))

    def change_direction(self, direction):
        if direction == "RIGHT" and self.direction != "LEFT":
            self.direction = "RIGHT"
        elif direction == "LEFT" and self.direction != "RIGHT":
            self.direction = "LEFT"
        elif direction == "UP" and self.direction != "DOWN":
            self.direction = "UP"
        elif direction == "DOWN" and self.direction != "UP":
            self.direction = "DOWN"

    def check_collision(self):
        if self.head[0] > SCREEN_WIDTH - CELL_SIZE or self.head[0] < 0 or self.head[1] > SCREEN_HEIGHT - CELL_SIZE or \
                self.head[1] < 0:
            return True
        for block in self.body[1:]:
            if self.head[0] == block[0] and self.head[1] == block[1]:
                return True
        return False

    def draw(self):
        for block in self.body:
            pygame.draw.rect(screen, SNAKE_COLOR, pygame.Rect(
                block[0], block[1], CELL_SIZE, CELL_SIZE))


# 定义食物类
class Food:
    def __init__(self):
        self.x = random.randrange(0, SCREEN_WIDTH - CELL_SIZE, CELL_SIZE)
        self.y = random.randrange(0, SCREEN_HEIGHT - CELL_SIZE, CELL_SIZE)

    def draw(self):
        pygame.draw.rect(screen, FOOD_COLOR, pygame.Rect(
            self.x, self.y, CELL_SIZE, CELL_SIZE))


# 定义游戏循环
def main():
    snake = Snake()
    food = Food()

    while True:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                exit()
            elif event.type == pygame.KEYDOWN:
                if event.key == pygame.K_RIGHT:
                    snake.change_direction("RIGHT")
                elif event.key == pygame.K_LEFT:
                    snake.change_direction("LEFT")
                elif event.key == pygame.K_UP:
                    snake.change_direction("UP")
                elif event.key == pygame.K_DOWN:
                    snake.change_direction("DOWN")

        screen.fill(BG_COLOR)

        # 移动贪吃蛇
        snake.move()

        # 检查碰撞
        if snake.check_collision():
            pygame.quit()
            exit()

        # 绘制食物和贪吃蛇
        food.draw()
        snake.draw()

        # 判断是否吃到食物
        if snake.head[0] == food.x and snake.head[1] == food.y:
            food = Food()

        pygame.display.update()

        # 控制帧率
        clock.tick(SNAKE_SPEED)

# 启动游戏循环
if __name__ == "__main__":
    main()


