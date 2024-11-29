using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading;

class SnakeGame
{
    static int width = 40;
    static int height = 20;
    static int score = 0;

    static List<(int x, int y)> snake = new List<(int x, int y)>();
    static (int x, int y) food = (0, 0);
    static (int x, int y) direction = (1, 0);
    static bool gameOver = false;

    static void Main()
    {
        Console.CursorVisible = false;
        Console.Clear();

        InitializeGame();

        while (!gameOver)
        {
            if (Console.KeyAvailable)
            {
                var key = Console.ReadKey(true).Key;
                ChangeDirection(key);
            }

            MoveSnake();
            CheckCollision();
            Draw();
            Thread.Sleep(100);  // Control speed of the game
        }

        Console.Clear();
        Console.WriteLine("Game Over! Your score: " + score);
    }

    static void InitializeGame()
    {
        snake.Add((width / 2, height / 2));  // Initial snake position
        GenerateFood();
    }

    static void ChangeDirection(ConsoleKey key)
    {
        switch (key)
        {
            case ConsoleKey.UpArrow:
                if (direction != (0, 1)) direction = (0, -1);
                break;
            case ConsoleKey.DownArrow:
                if (direction != (0, -1)) direction = (0, 1);
                break;
            case ConsoleKey.LeftArrow:
                if (direction != (1, 0)) direction = (-1, 0);
                break;
            case ConsoleKey.RightArrow:
                if (direction != (-1, 0)) direction = (1, 0);
                break;
        }
    }

    static void MoveSnake()
    {
        (int x, int y) newHead = (snake[0].x + direction.x, snake[0].y + direction.y);
        snake.Insert(0, newHead);

        if (newHead == food)
        {
            score++;
            GenerateFood();
        }
        else
        {
            snake.RemoveAt(snake.Count - 1);
        }
    }

    static void CheckCollision()
    {
        (int x, int y) head = snake[0];

        // Check wall collision
        if (head.x < 0 || head.y < 0 || head.x >= width || head.y >= height)
        {
            gameOver = true;
        }

        // Check self-collision
        if (snake.Skip(1).Contains(head))
        {
            gameOver = true;
        }
    }

    static void GenerateFood()
    {
        Random rnd = new Random();
        do
        {
            food = (rnd.Next(0, width), rnd.Next(0, height));
        } while (snake.Contains(food));
    }

    static void Draw()
    {
        Console.Clear();
        Console.SetCursorPosition(food.x, food.y);
        Console.Write("O");

        foreach (var (x, y) in snake)
        {
            Console.SetCursorPosition(x, y);
            Console.Write("#");
        }

        Console.SetCursorPosition(0, height);
        Console.Write($"Score: {score}");
    }
}
