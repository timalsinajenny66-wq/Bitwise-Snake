#include <GL/glut.h>
#include <cstdlib>
#include <vector>
#include <iostream>
using namespace std;
struct Point {
int x, y;
};

vector<Point> snake;
Point food;
char direction = 'R';
int score = 0;
bool gameOver = false;
void drawBlock(int x, int y) {
glBegin(GL_QUADS);
glVertex2f(x, y);
glVertex2f(x + 20, y);
glVertex2f(x + 20, y + 20);
glVertex2f(x, y + 20);
glEnd();
}
void placeFood() {
food.x = (rand() % 25) * 20;
food.y = (rand() % 25) * 20;
}
void drawGrid() {
glColor3f(0.2f, 0.2f, 0.2f);
glBegin(GL_LINES);
for (int i = 0; i <= 500; i += 20) {
glVertex2f(i, 0);
glVertex2f(i, 500);
glVertex2f(0, i);
glVertex2f(500, i);
}
glEnd();
}
void display() {
glClear(GL_COLOR_BUFFER_BIT);
drawGrid();
if (gameOver) {
glColor3f(1, 0, 0);
glRasterPos2f(180, 250);
string msg = "GAME OVER";
for (char c : msg)
glutBitmapCharacter(GLUT_BITMAP_TIMES_ROMAN_24, c);
glutSwapBuffers();
return;
}
// Snake head (yellow)

glColor3f(1, 1, 0);
drawBlock(snake[0].x, snake[0].y);
// Snake body (green)
glColor3f(0, 1, 0);
for (int i = 1; i < snake.size(); i++)
drawBlock(snake[i].x, snake[i].y);
// Food (red)
glColor3f(1, 0, 0);
drawBlock(food.x, food.y);
// Score display
glColor3f(0, 1, 1);
glRasterPos2f(10, 480);
string scoreText = "Score: " + to_string(score);
for (char c : scoreText)
glutBitmapCharacter(GLUT_BITMAP_HELVETICA_18, c);
glutSwapBuffers();
}
void moveSnake() {
for (int i = snake.size()-1; i > 0; i--)
snake[i] = snake[i-1];
if (direction == 'R') snake[0].x += 20;
if (direction == 'L') snake[0].x-= 20;
if (direction == 'U') snake[0].y += 20;
if (direction == 'D') snake[0].y-= 20;
if (snake[0].x < 0 || snake[0].x >= 500 || snake[0].y < 0 || snake[0].y >=
500)
gameOver = true;
for (int i = 1; i < snake.size(); i++)
if (snake[0].x == snake[i].x && snake[0].y == snake[i].y)
gameOver = true;
if (snake[0].x == food.x && snake[0].y == food.y) {
snake.push_back(snake.back());
placeFood();
score += 10;
}
}
void update(int value) {
if (!gameOver) moveSnake();

glutPostRedisplay();
glutTimerFunc(120, update, 0);
}
void keyboard(unsigned char key, int x, int y) {
if (key == 'w') direction = 'U';
if (key == 's') direction = 'D';
if (key == 'a') direction = 'L';
if (key == 'd') direction = 'R';
}
void init() {
// Dark blue / navy background
glClearColor(0.02f, 0.04f, 0.12f, 1.0f);
gluOrtho2D(0, 500, 0, 500);
}
int main(int argc, char** argv) {
glutInit(&argc, argv);
glutInitDisplayMode(GLUT_DOUBLE | GLUT_RGB);
glutInitWindowSize(500, 500);
glutCreateWindow("2D Snake Game");
init();
snake.push_back({100, 100});
snake.push_back({80, 100});
snake.push_back({60, 100});
placeFood();
glutDisplayFunc(display);
glutKeyboardFunc(keyboard);
glutTimerFunc(120, update, 0);
glutMainLoop();
return 0;
}
