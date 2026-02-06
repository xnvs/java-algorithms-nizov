[Оглавление](README.md)

Решебник. MatrixTraversal_FloodFill
===
```java
package ru.pragm;

import java.util.LinkedList;
import java.util.Queue;

import static java.lang.System.out;

/*
Задача:
MatrixTraversal
FloodFill
---------------------------------
    Условие: дано изображение (сетка m × n), начальная точка (sr, sc) и новый цвет.
    Нужно выполнить заливку (flood fill) начиная с начальной точки:
    поменять цвет всех смежных пикселей того же цвета на новый цвет.
    
    (1) Формализация задачи и её суть
    В этой задаче требуется типичное решение для matrix traversal -
    сделать заливку по начальной точке. Грубо говоря, у нас есть матрица:

          (1)(1)(2)(3)(3)
          (1)(1)(2)(3)(3)
          (1)(1)(2)(3)(3)
          (1)(1)(2)(3)(3)

    Если выбрать точку {row=0, col=0} и задать для неё «цвет» в виде 7,
    получим:

           /- точка начала заливки
          (7)(7)(2)(3)(3)
          (7)(7)(2)(3)(3)
          (7)(7)(2)(3)(3)
          (7)(7)(2)(3)(3)

  Если выбрать {row=0, col=2}, то получим:

                 /- точка начала заливки
          (1)(1)(7)(3)(3)
          (1)(1)(7)(3)(3)
          (1)(1)(7)(3)(3)
          (1)(1)(7)(3)(3)

    Технически алгоритм задаёт «цвет» клетки, по которому идёт движение,
    базируясь на начальном содержании ячейки матрицы. Затем он запускает
    обход с заполнением на базе DFS или BFS. В этом кейсе нет разницы,
    но именно здесь DFS будет чуть быстрее.
    
    (2) Идея решения
    Задача решается примитивно: реализуем обход, ограниченный единственным
    признаком - цветом начальной точки. Буквально мы смотрим на матрицу
    и видим только дерево, образованное цветом начальной точки.
    
    Начальный цвет точки - (7). Имеем такую матрицу:

           /- точка начала заливки
          (7)(7)(3)(3)(3)
          (7)(7)(7)(7)(3)
          (7)(7)(7)(7)(3)
          (7)(7)(3)(3)(3)

    По сути, алгоритм увидит дерево, потому что всё, что не этого цвета,
    будет просто игнорироваться:

          (7)(7)
          (7)(7)(7)(7)
          (7)(7)(7)(7)
          (7)(7)

    (3) Порядок кодирования
    Чтобы решить задачу, нужно:
    - написать код обхода;
    - добавить ограничение, по которому всё, кроме начального цвета,
      будет игнорироваться;
    - в начале движения алгоритма получить начальный цвет.
    
    Все нюансы работы смотрите в разделе по «matrix traversal».
* */
public class MatrixTraversal_FloodFill {

  public class SolutionDFS {
    public int[][] floodFill(int[][] image, int sr, int sc, int newColor) {

      // <- получаем исходный цвет начальной точки
      //
      int originalColor = image[sr][sc];

      // <- если новый цвет совпадает с исходным, ничего не делаем
      //
      if (originalColor == newColor) {
        return image;
      }

      // <- запускаем DFS заливку
      //
      dfs(image, sr, sc, originalColor, newColor);
      return image;
    }

    private void dfs(int[][] image, int row, int col, int originalColor, int newColor) {

      // <- проверка границ и соответствия исходному цвету
      //
      if (
          row < 0 || row >= image.length ||
          col < 0 || col >= image[0].length ||
          image[row][col] != originalColor) {   // <- вот условие отличающее "заливку"
        return;
      }

      // <- меняем цвет текущей ячейки
      //
      image[row][col] = newColor;

      // <- рекурсивно заливаем 4 соседние ячейки
      //
      dfs(image, row - 1, col, originalColor, newColor); // вверх
      dfs(image, row + 1, col, originalColor, newColor); // вниз
      dfs(image, row, col - 1, originalColor, newColor); // влево
      dfs(image, row, col + 1, originalColor, newColor); // вправо
    }
  }

  public class Solution {
    public int[][] floodFill(int[][] image, int sr, int sc, int newColor) {
      int originalColor = image[sr][sc];

      if (originalColor == newColor) {
        return image;
      }

      int rows = image.length;
      int cols = image[0].length;

      // <- BFS очередь для хранения позиций
      //
      Queue<int[]> queue = new LinkedList<>();
      queue.offer(new int[]{sr, sc});

      int[][] directions = {{-1, 0}, {1, 0}, {0, -1}, {0, 1}};

      while (!queue.isEmpty()) {
        int[] current = queue.poll();
        int row = current[0];
        int col = current[1];

        // <- меняем цвет текущей ячейки
        //
        image[row][col] = newColor;

        // <- проверяем всех соседей
        //
        for (int[] dir : directions) {
          int newRow = row + dir[0];
          int newCol = col + dir[1];

          // <- проверяем границы и соответствие исходному цвету
          //
          if (newRow >= 0 && newRow < rows && newCol >= 0 && newCol < cols
              && image[newRow][newCol] == originalColor) {
            queue.offer(new int[]{newRow, newCol});
          }
        }
      }

      return image;
    }
  }

  class MatrixUtils {
    void printMatrixGrid(int[][] gridToPrint){
      int rows = gridToPrint.length;
      int cols = gridToPrint[0].length;
      for(int i = 0; i < rows; i++){
        out.print("|");
        for(int j = 0; j < cols; j++) {
          int value = gridToPrint[i][j];
          String toPrint = "";
          if(value < 0) toPrint = ""+value;
          else if(value < 10) toPrint = " "+value;
          else toPrint = ""+value;
          out.print(toPrint + "|");
        }
        out.println();
      }
    }
  }

  static void main() {
    var wrapper = new MatrixTraversal_FloodFill();
    var utils = wrapper.new MatrixUtils();

    var solutionDFS = wrapper.new SolutionDFS();
    var solutionBFS = wrapper.new Solution();

    int[][] matrixGrid = {
        {1,1,3,2,2},
        {1,1,3,2,2},
        {1,1,3,2,2},
        {1,1,3,2,2}
    };

    solutionDFS.floodFill(matrixGrid, 0,0, 9);
    utils.printMatrixGrid(matrixGrid);
    out.println();

    solutionBFS.floodFill(matrixGrid,0,0,7);
    utils.printMatrixGrid(matrixGrid);
  }
}//c:MatrixTraversal_FloodFill

```