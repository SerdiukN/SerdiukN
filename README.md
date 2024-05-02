                                                                                        Робив Сердюк Нікіта (252 група) 
                                                                                            "Sea battle"
                                                                                                    
                                                                                                    
                                                                                                    
                                                                                                    
                                                                                                    
                                                                                                    using System;

namespace BattleshipGame
{
    public class Program
    {
        public static void Main(string[] args)
        {
            Console.WriteLine("Welcome to Battleship!");

            // Initialize game boards for player and computer
            Board playerBoard = new Board();
            Board computerBoard = new Board();

            // Place ships for player and computer
            playerBoard.PlaceShipsRandomly();
            computerBoard.PlaceShipsRandomly();

            // Game loop
            while (true)
            {
                Console.WriteLine("Player's Turn:");
                playerBoard.Display(false);
                Console.WriteLine("Enter target coordinates (e.g., A5):");
                string target = Console.ReadLine()?.ToUpper();
                if (string.IsNullOrEmpty(target) || target.Length != 2)
                {
                    Console.WriteLine("Invalid input! Try again.");
                    continue;
                }

                bool isValid = playerBoard.Shoot(target);
                if (!isValid)
                {
                    Console.WriteLine("Invalid target! Try again.");
                    continue;
                }

                if (computerBoard.AllShipsSunk())
                {
                    Console.WriteLine("Congratulations! You won!");
                    break;
                }

                Console.WriteLine("\nComputer's Turn:");
                computerBoard.ShootRandomly();
                playerBoard.Display(true);

                if (playerBoard.AllShipsSunk())
                {
                    Console.WriteLine("Computer won! Better luck next time.");
                    break;
                }
            }
        }
    }

    public class Board
    {
        private const int BoardSize = 10;
        private readonly int[,] board = new int[BoardSize, BoardSize];
        private readonly Random random = new Random();

        public void PlaceShipsRandomly()
        {
            // Place ships of different lengths randomly on the board
            int[] shipLengths = {5, 4, 3, 3, 2};
            foreach (var length in shipLengths)
            {
                bool shipPlaced = false;
                while (!shipPlaced)
                {
                    int x = random.Next(0, BoardSize);
                    int y = random.Next(0, BoardSize);
                    bool horizontal = random.Next(0, 2) == 0;

                    if (CanPlaceShip(x, y, length, horizontal))
                    {
                        PlaceShip(x, y, length, horizontal);
                        shipPlaced = true;
                    }
                }
            }
        }

        private bool CanPlaceShip(int x, int y, int length, bool horizontal)
        {
            if (horizontal && y + length > BoardSize)
                return false;
            if (!horizontal && x + length > BoardSize)
                return false;

            for (int i = 0; i < length; i++)
            {
                int posX = horizontal ? x : x + i;
                int posY = horizontal ? y + i : y;

                if (board[posX, posY] == 1)
                    return false;

                // Check adjacent cells
                for (int dx = -1; dx <= 1; dx++)
                {
                    for (int dy = -1; dy <= 1; dy++)
                    {
                        int adjX = posX + dx;
                        int adjY = posY + dy;

                        if (adjX >= 0 && adjX < BoardSize && adjY >= 0 && adjY < BoardSize &&
                            board[adjX, adjY] == 1)
                            return false;
                    }
                }
            }

            return true;
        }

        private void PlaceShip(int x, int y, int length, bool horizontal)
        {
            for (int i = 0; i < length; i++)
            {
                int posX = horizontal ? x : x + i;
                int posY = horizontal ? y + i : y;
                board[posX, posY] = 1;
            }
        }

        public bool Shoot(string target)
        {
            int x = target[0] - 'A';
            int y = int.Parse(target.Substring(1)) - 1;

            if (x < 0 || x >= BoardSize || y < 0 || y >= BoardSize || board[x, y] == -1 || board[x, y] == 2)
                return false;

            if (board[x, y] == 1)
            {
                board[x, y] = 2;
                Console.WriteLine("Hit!");
            }
            else
            {
                board[x, y] = -1;
                Console.WriteLine("Miss!");
            }

            return true;
        }

        public void ShootRandomly()
        {
            int x, y;
            do
            {
                x = random.Next(0, BoardSize);
                y = random.Next(0, BoardSize);
            } while (board[x, y] == -1 || board[x, y] == 2);

            if (board[x, y] == 1)
            {
                board[x, y] = 2;
                Console.WriteLine($"Computer hit at {Convert.ToChar('A' + x)}{y + 1}!");
            }
            else
            {
                board[x, y] = -1;
                Console.WriteLine($"Computer missed at {Convert.ToChar('A' + x)}{y + 1}!");
            }
        }

        public void Display(bool showShips)
        {
            Console.WriteLine("  1 2 3 4 5 6 7 8 9 10");
            for (int i = 0; i < BoardSize; i++)
            {
                Console.Write((char)('A' + i) + " ");
                for (int j = 0; j < BoardSize; j++)
                {
                    if (board[i, j] == 1 && showShips)
                        Console.Write("O ");
                    else if (board[i, j] == 2)
                        Console.Write("X ");
                    else if (board[i, j] == -1)
                        Console.Write(". ");
                    else
                        Console.Write("~ ");
                }
                Console.WriteLine();
            }
            Console.WriteLine();
        }

        public bool AllShipsSunk()
        {
            foreach (var cell in board)
            {
                if (cell == 1)
                    return false;
            }
            return true;
        }
    }
}
