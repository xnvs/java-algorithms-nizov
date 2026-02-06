[Оглавление](README.md)

Решебник. MatrixTraversal_SurroundedRegions
===
```java
package ru.pragm;

import java.util.LinkedList;
import java.util.Queue;
import java.util.function.Supplier;

import static java.lang.System.out;

/*
Задача:
MatrixTraversal
SurroundedRegions
---------------------------------

    Условие: дана сетка m x n, содержащая 'X' и 'O'.
    Нужно захватить все регионы, окружённые 'X'.
    Регион считается захваченным, если все 'O' в регионе окружены 'X'
    (соединены по горизонтали или вертикали).
    'O' на границе не считаются окружёнными.
    
    (1) Формализация задачи и её суть
    
    Нам дана матрица, в которой есть 'O' (нули) и 'X' (иксы).
    Требуется закрасить с помощью 'X' все 'O', которые полностью
    окружены 'X'. При этом не считаются окружёнными те 'O',
    которые прилегают к краю матрицы.
    
    Рассмотрим примеры.
    
    Первый вариант:
    
    х-х-х-х-х
    х-0-х-х-х  <- на этой строке 'O' полностью окружён 'X', поэтому закрашивается
    х-х-х-0-0
    0-х-0-х-0  <- на этой строке все 'O' прилегают к краю, поэтому не закрашиваются
    
    Второй вариант:
    
    х-х-х-х-х
    х-0-0-0-х  <- здесь все 'O' окружены
    х-0-0-0-х
    х-х-х-х-х
    
    Результат для первого варианта:
    
    х-х-х-х-х
    х-х-х-х-х
    х-х-х-0-0
    0-х-0-х-0
    
    Результат для второго варианта:
    
    х-х-х-х-х
    х-x-x-x-х
    х-x-x-x-х
    х-х-х-х-х
    
    Интересность задачи в том, что она представляет собой
    инверсивную задачу к подсчёту количества островов.
    Основной принцип решения - не закрашивать окружённые регионы,
    а сохранить прилегающие к краям регионы (их легко определить,
    просто обойдя край), закрасив их промежуточным символом.
    
    После этого всё, что останется, можно закрасить 'X'.
    Получится следующее:
    
    х-х-х-х-х
    х-0-х-х-х   <- на этой строке промежуточной закраски не произошло -> удаляем
    х-х-х-T-T   <- закрашиваем промежуточным символом, например 'T'
    T-х-T-х-T
    
    Очевидно, что для закраски мы можем использовать DFS или BFS
    (проще DFS) - и решение готово.
    
    (2) Идея решения
    
    В плане решения нужно сделать следующее:
    1. Проверить базовый случай (пустая матрица).
    2. Определить размеры матрицы.
    3. Обойти граничные клетки (первая/последняя строка,
       первый/последний столбец).
    4. Для каждой 'O' на границе запустить DFS/BFS,
       чтобы пометить соединённые 'O'.
    5. Пройти по всей матрице для финальной замены:
       - 'O' -> 'X' (не помеченные);
       - помеченные -> 'O' (восстановление).
    6. Вернуть модифицированную матрицу (void-метод по условию задачи).
    
    Это базовое решение. От него возможны оптимизации,
    но в целом классическое решение именно такое.
    
    (3) Порядок кодирования
    
    В итоге будет следующее:
    - Обработка от границ: начинаем с граничных 'O' и помечаем все связанные.
    - Временная пометка 'T' для 'O', которые не должны быть захвачены.
    - Двухэтапный процесс:
      - Пометить незахватываемые 'O' (связанные с границей).
      - Преобразовать оставшиеся 'O' в 'X' и восстановить помеченные.
    - DFS/BFS от границ: найти все регионы, связанные с границей.
    - Восстановление: 'T' обратно в 'O'.
    - Классический подход: обработка от границ с временной пометкой -
      оптимальное решение со сложностью O(m x n).
* */
public class MatrixTraversal_SurroundedRegions {

  public class SolutionDFS {
    public void solve(char[][] board) {
      if (board == null || board.length == 0) {
        return;
      }

      int rows = board.length;
      int cols = board[0].length;

      // <- строки
      //
      for (int j = 0; j < cols; j++) {
        if (board[0][j] == 'O') {
          dfs(board, 0, j);
        }
        if (board[rows - 1][j] == 'O') {
          dfs(board, rows - 1, j);
        }
      }

      // <- столбцы
      //
      for (int i = 0; i < rows; i++) {
        if (board[i][0] == 'O') {
          dfs(board, i, 0);
        }
        if (board[i][cols - 1] == 'O') {
          dfs(board, i, cols - 1);
        }
      }

      // <- оставшиеся в искы + восстанавливаем нули
      //
      for (int i = 0; i < rows; i++) {
        for (int j = 0; j < cols; j++) {
          if (board[i][j] == 'O') {
            board[i][j] = 'X';
          } else if (board[i][j] == 'T') {
            board[i][j] = 'O';
          }
        }
      }
    }

    private void dfs(char[][] board, int row, int col) {
      int rows = board.length;
      int cols = board[0].length;

      if (row < 0 || row >= rows || col < 0 || col >= cols ||
          board[row][col] != 'O') {
        return;
      }

      board[row][col] = 'T';

      dfs(board, row - 1, col);
      dfs(board, row + 1, col);
      dfs(board, row, col - 1);
      dfs(board, row, col + 1);
    }
  }

  // <- логика решения задачи для BFS такая же
  //
  public class SolutionBFS {
    public void solve(char[][] board) {
      if (board == null || board.length == 0) return;

      int rows = board.length;
      int cols = board[0].length;

      Queue<int[]> queue = new LinkedList<>();

      for (int i = 0; i < rows; i++) {
        for (int j = 0; j < cols; j++) {
          if ((i == 0 || i == rows - 1 || j == 0 || j == cols - 1) && board[i][j] == 'O') {
            queue.offer(new int[]{i, j});
            board[i][j] = 'T';
          }
        }
      }

      int[][] directions = {
          {-1, 0}, {1, 0}, {0, -1}, {0, 1}
      };

      while (!queue.isEmpty()) {
        int[] current = queue.poll();
        int row = current[0];
        int col = current[1];

        for (int[] dir : directions) {
          int newRow = row + dir[0];
          int newCol = col + dir[1];

          if (newRow >= 0 && newRow < rows && newCol >= 0 && newCol < cols
              && board[newRow][newCol] == 'O') {
            queue.offer(new int[]{newRow, newCol});
            board[newRow][newCol] = 'T';
          }
        }
      }

      for (int i = 0; i < rows; i++) {
        for (int j = 0; j < cols; j++) {
          if (board[i][j] == 'O') {
            board[i][j] = 'X';
          } else if (board[i][j] == 'T') {
            board[i][j] = 'O';
          }
        }
      }
    }
  }

  class MatrixUtils {
    void printMatrixGrid(char[][] gridToPrint){
      int rows = gridToPrint.length;
      int cols = gridToPrint[0].length;
      for(int i = 0; i < rows; i++){
        out.print("|");
        for(int j = 0; j < cols; j++) {
          var value = gridToPrint[i][j];
          String toPrint = ""+value;
          out.print(toPrint + "|");
        }
        out.println();
      }
    }
  }

  static void main() {
    Supplier<char[][]> giveMeMatrix = () -> new char[][]{
        {'X','X','X','X','X'},
        {'X','O','X','X','X'},
        {'X','X','X','O','O'},
        {'O','X','O','X','O'}
    };

    var wrapper = new MatrixTraversal_SurroundedRegions();
    var solutionDFS = wrapper.new SolutionDFS();
    var solutionBFS = wrapper.new SolutionBFS();
    var utils = wrapper.new MatrixUtils();

    var matrixA = giveMeMatrix.get();
    var matrixB = giveMeMatrix.get();

    solutionDFS.solve(matrixA);
    solutionBFS.solve(matrixB);

    utils.printMatrixGrid(matrixA);
    out.println();
    utils.printMatrixGrid(matrixB);
  }
}//c:MatrixTraversal_SurroundedRegions

```