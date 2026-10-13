```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

class Program
{
    // Константы для размера и символов
    const int Size = 6;
    const char Wall = '#';
    const char Corridor = '.';
    const char Exit = 'E';
    const char Rod = 'R';
    const char Screen = 'S';

    static void Main()
    {
        // 1. Назначение матриц
        char[,] station = new char[Size, Size];
        int[,] radiationLevel = new int[Size, Size];
        Random rng = new Random();

        // 2. Заполнение матриц 
        for (int row = 0; row < Size; row++)
        {
            for (int col = 0; col < Size; col++)
            {
                // Внешние границы — стены, внутри — коридоры
                if (row == 0 || row == Size - 1 || col == 0 || col == Size - 1)
                {
                    station[row, col] = Wall;
                }
                else
                {
                    station[row, col] = Corridor;
                }
            }
        }
        // 3. Размещение выхода
        station[1, 1] = Exit;

        // Размещение радиационных стержней в случайных внутренних ячейках коридоров
        int rodsPlaced = 0;
        while (rodsPlaced < 3)
        {
            int r = rng.Next(1, Size - 1);
            int c = rng.Next(1, Size - 1);
            if (station[r, c] == Corridor)
            {
                station[r, c] = Rod;
                rodsPlaced++;
            }
        }
        // 4. Заполнение параллельной матрицы радиации числами от 0 до 100
        for (int row = 0; row < Size; row++)
        {
            for (int col = 0; col < Size; col++)
            {
                radiationLevel[row, col] = rng.Next(0, 101); // (101, чтобы число 100 тоже могло выпасть)
            }
        }
        // 5. Вывод матрицы станции с выделением стержней красным цветом
        for (int row = 0; row < Size; row++)
        {
            for (int col = 0; col < Size; col++)
            {
                if (station[row, col] == Rod)
                {
                    Console.ForegroundColor = ConsoleColor.Red;
                    Console.Write(station[row, col] + " ");
                    Console.ResetColor();
                }
                else
                {
                    Console.Write(station[row, col] + " ");
                }
            }
            Console.WriteLine();
        }
        Console.WriteLine();

        // 6. Суммарный уровень радиации в каждом ряду
        Console.WriteLine("Уровень радиации по рядам:");
        for (int row = 1; row < Size - 1; row++)
        {
            int rowSum = 0;
            for (int col = 0; col < Size; col++)
            {
                rowSum += radiationLevel[row, col];
            }
            Console.WriteLine($"Ряд {row}: {rowSum}");
        }
        Console.WriteLine();

        // 7. Координаты отсека с максимальным уровнем радиации
        int maxRadiation = radiationLevel.Cast<int>().Max();
        var maxCoords = new List<(int, int)>();
        for (int row = 0; row < Size; row++)
        {
            for (int col = 0; col < Size; col++)
            {
                if (radiationLevel[row, col] == maxRadiation)
                {
                    maxCoords.Add((row, col));
                }
            }
        }
        Console.WriteLine($"Максимальный уровень радиации: {maxRadiation}");
        Console.WriteLine("Координаты отсекa (клетки) с максимальной радиацией: " +
            string.Join(", ", maxCoords.Select(c => $"[{c.Item1}, {c.Item2}]")));
        Console.WriteLine();

        // 8. Подсчет безопасных коридоров . на главной диагонали
        int safeDiag = 0;
        for (int i = 0; i < Size; i++)
        {
            if (station[i, i] == Corridor) safeDiag++;
        }
        Console.WriteLine($"Безопасных коридоров на главной диагонали: {safeDiag}");

        // 9. Запрос координат для установки свинцового экрана
        Console.WriteLine("\nУстановка свинцового экрана.");
        Console.Write($"Введите номер строки (1-4): ");
        bool rowOk = int.TryParse(Console.ReadLine(), out int targetRow);
        Console.Write($"Введите номер столбца (1-4): ");
        bool colOk = int.TryParse(Console.ReadLine(), out int targetCol);

        if (rowOk && colOk &&
           targetRow >= 1 && targetRow <= 4 && 
           targetCol >= 1 && targetCol <= 4)   
        {
            station[targetRow, targetCol] = Screen;
            Console.WriteLine($"Экран установлен в отсеке [{targetRow}, {targetCol}].");
        }
        else
        {
            Console.WriteLine("Ошибка: введены некорректные координаты. Экран не установлен.");
        }

        // 10. Подсчет общего числа непроходимых завалов (# это завал есче) на всей станции
        int WallCount = 0;
        foreach (char cell in station)
        {
            if (cell == Wall) WallCount++;
        }
        Console.WriteLine($"\nОбщее число непроходимых завалов на станции: {WallCount}");

        // Финальный вид станции после установки экрана
        Console.WriteLine("\nИтоговое состояние станции:");
        for (int row = 0; row < Size; row++)
        {
            for (int col = 0; col < Size; col++)
            {
                if (station[row, col] == Rod)
                {
                    Console.ForegroundColor = ConsoleColor.Red;
                    Console.Write(station[row, col] + " ");
                    Console.ResetColor();
                }
                else if (station[row, col] == Screen)
                {
                    Console.ForegroundColor = ConsoleColor.Blue;
                    Console.Write(station[row, col] + " ");
                    Console.ResetColor();
                }
                else
                {
                    Console.Write(station[row, col] + " ");
                }
            }
            Console.WriteLine();
        }
    }
}


```
`Результат выполнения:`
<picture>
  <img src="https://github.com/KseniaBashkatova/Task6-/blob/main/screen/1.png?raw=true">
</picture>


<picture>
  <img src="https://github.com/KseniaBashkatova/Task6-/blob/main/screen/4.png?raw=true">
</picture>
<img src="https://github.com/KseniaBashkatova/Task6-/blob/main/screen/2.png?raw=true">
<picture>
  <img src="https://github.com/KseniaBashkatova/Task6-/blob/main/screen/3.png?raw=true">
</picture>

// ДЕЛАЛИ АРИСТОВ И БАШКАТОВА
:P
