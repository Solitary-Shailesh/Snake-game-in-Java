# Snake-game-in-Java
Program based on JAVA
MAIN CODE:-
import javax.swing.*;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.awt.event.KeyEvent;
import java.awt.event.KeyListener;
import java.util.ArrayList;
import java.util.Random;

public class SnakeGame extends JFrame implements ActionListener, KeyListener {
    private final int SCALE = 10;
    private final int WIDTH = 60;
    private final int HEIGHT = 40;
    private final int SPEED = 100;

    private boolean running = false;
    private Timer timer;
    private int score = 0;
    private ArrayList<Point> snake = new ArrayList<>();
    private Point food;

    private int direction = KeyEvent.VK_RIGHT;
    private int nextDirection = KeyEvent.VK_RIGHT;

    public SnakeGame() {
        setTitle("Snake Game");
        setSize(WIDTH * SCALE, HEIGHT * SCALE);
        setResizable(false);
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        setLocationRelativeTo(null);
        addKeyListener(this);

        timer = new Timer(SPEED, this);

        initGame();
    }

    private void initGame() {
        running = true;
        score = 0;
        snake.clear();

        // Initialize the snake with three segments
        snake.add(new Point(WIDTH / 2, HEIGHT / 2));
        snake.add(new Point(WIDTH / 2 - 1, HEIGHT / 2));
        snake.add(new Point(WIDTH / 2 - 2, HEIGHT / 2));

        generateFood();
        timer.start();
    }

    private void generateFood() {
        Random random = new Random();
        int x, y;
        do {
            x = random.nextInt(WIDTH);
            y = random.nextInt(HEIGHT);
        } while (snake.contains(new Point(x, y)));

        food = new Point(x, y);
    }

    private void gameOver() {
        running = false;
        timer.stop();
        JOptionPane.showMessageDialog(this, "Game Over! Your score: " + score, "Game Over", JOptionPane.INFORMATION_MESSAGE);
        initGame();
    }

    private void move() {
        Point head = snake.get(0);
        Point newHead = new Point(head.x, head.y);

        switch (direction) {
            case KeyEvent.VK_UP:
                newHead.y--;
                break;
            case KeyEvent.VK_DOWN:
                newHead.y++;
                break;
            case KeyEvent.VK_LEFT:
                newHead.x--;
                break;
            case KeyEvent.VK_RIGHT:
                newHead.x++;
                break;
        }

        if (newHead.equals(food)) {
            snake.add(0, newHead);
            score++;
            generateFood();
        } else {
            snake.add(0, newHead);
            snake.remove(snake.size() - 1);
        }

        // Check for collisions
        if (snake.size() > 1 && snake.subList(1, snake.size()).contains(newHead)) {
            gameOver();
        }

        // Check for out-of-bounds
        if (newHead.x < 0 || newHead.x >= WIDTH || newHead.y < 0 || newHead.y >= HEIGHT) {
            gameOver();
        }
    }

    @Override
    public void actionPerformed(ActionEvent e) {
        if (running) {
            direction = nextDirection;
            move();
            repaint();
        }
    }

    @Override
    public void keyPressed(KeyEvent e) {
        int key = e.getKeyCode();
        if ((key == KeyEvent.VK_LEFT || key == KeyEvent.VK_RIGHT || key == KeyEvent.VK_UP || key == KeyEvent.VK_DOWN) &&
                Math.abs(direction - key) != 2) {
            nextDirection = key;
        }
    }

    @Override
    public void keyTyped(KeyEvent e) {
    }

    @Override
    public void keyReleased(KeyEvent e) {
    }

    @Override
    public void paint(Graphics g) {
        super.paint(g);

        if (running) {
            // Draw snake
            g.setColor(Color.GREEN);
            for (Point p : snake) {
                g.fillRect(p.x * SCALE, p.y * SCALE, SCALE, SCALE);
            }

            // Draw food
            g.setColor(Color.RED);
            g.fillRect(food.x * SCALE, food.y * SCALE, SCALE, SCALE);

            // Draw score
            g.setColor(Color.BLACK);
            g.setFont(new Font("Arial", Font.PLAIN, 20));
            g.drawString("Score: " + score, 10, 30);

            Toolkit.getDefaultToolkit().sync();
        } else {
            // Draw game over message
            g.setColor(Color.BLACK);
            g.setFont(new Font("Arial", Font.BOLD, 30));
            String message = "Press any arrow key to start";
            g.drawString(message, (WIDTH * SCALE - g.getFontMetrics().stringWidth(message)) / 2,
                    HEIGHT * SCALE / 2);
        }
    }

    public static void main(String[] args) {
        SwingUtilities.invokeLater(() -> {
            SnakeGame snakeGame = new SnakeGame();
            snakeGame.setVisible(true);
        });
    }
}
