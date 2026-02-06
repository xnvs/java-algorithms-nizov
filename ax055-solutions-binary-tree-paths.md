[Оглавление](README.md)

Решебник. BinaryTreeTraversal_BinaryTreePaths
===
```java
package ru.pragm;

import static ru.pragm.utils.Out.out;

import java.util.ArrayList;
import java.util.List;
import java.util.Stack;

/*
Задача:
BinaryTreeTraversal
BinaryTreePaths
---------------------------------
Условие: дан корень бинарного дерева.
Вернуть все корневые‑листовые пути в любом порядке.

(1) Формализация задачи и её суть

    Что от нас хотят?
    От нас хотят, по сути, визуализировать обход дерева через DFS
    в виде массива строк.
    
    То есть получить визуализацию того, как работает DFS.
    Про это, по сути, и вся задача.
    
    Если ты понимаешь, как работает DFS, ты добавляешь к нему
    дополнительный отладочный вывод - и получаешь решение.
    
    По сути, задача говорит:
    - заведи список путей в виде строк формата "_->_", где _ - это узел;
    - когда будешь обходить ветки в DFS, дели пути (создавай новые строки - деление же это путь);
    - когда будет достижение узла кроны - сохраняй такой полученный путь.
    
    Проще говоря, у нас есть дерево (и не обращаем внимания на то, что это не BST -
    здесь это не важно, нам нужно просто бинарное дерево, любое):
    
    Дерево: [1, 2, 3, null, 5]

             1
            / \
           2   3
            \
             5

    Очевидное деление тут одно - это деление на уровне (1).
    То есть (1) войдёт в оба пути, и пути будут:
    ["1->2->5", "1->3"]
    
    Проблему создаёт исключительно то, как входное дерево может быть представлено.
    Но в задаче оно представлено не в виде массива (а, как мы помним, деревом можно
    представить вполне себе массивом - если не знаете как, читайте мою книжку про алгоритмы),
    а просто корневым узлом.
    
    Это диктует решение в виде рекурсии - так как рекурсия легко позволяет понять,
    на какой точке новый путь создавать.
    
    Рекурсия по сути видит вот это логически как основание для начала двух путей:

             1
            / \
           2   3

    Итерационная версия в этом плане будет более логически запутанной.
    Она нам также не подойдёт в плане работы и будет, скорее всего, медленнее,
    потому что системный стек работает быстрее. А задача не обладает настолько
    сильной вложенностью, чтобы системный стек получил overflow.
    
    Таким образом, итеративное решение с самописным стеком нам выходит:
    1. и пример плохо решит визуально;
    2. и работать быстрее не будет.
    
    Но мы его всё равно для примера покажем.

(2) Идея решения

    Решения в лоб здесь у нас создать не выйдет - просто потому, что у нас
    всё‑таки обход дерева. Как мы его в лоб обойдём, кроме как не через DFS?
    Через BFS? Но нам пути надо строить, а BFS для этого изначально не предназначен.
    
    (Я сейчас, кстати, подумал: можно ли было бы решить эту задачу с помощью BFS?
    Да, можно - но это было бы что‑то вроде рекурсивной реализации линейного поиска.)
    
    В общем, наш диагноз - DFS с отладкой.
    
    Из отладки у нас будет:
    1. строка, которая будет представлять собою путь - прямо буквально строка;
    2. массив строк, который будет заполняться в процессе пути;
    3. реализация DFS, по которой мы сначала напишем, а потом добавим в него отладку:
       - отладка делит строку, когда находит разделение;
       - отладка сохраняет итоговую строку, когда код доходит до конечного узла (кроны).
    
    В задаче больше технически ничего нет.
    
    Единственное, что здесь можно ещё выделить: так как у нас есть дополнительный массив,
    который будет результаты собирать, его можно выделить во внешний по отношению
    к запуску рекурсивной DFS враппер‑хелпер и разместить его сверху кода.
    В DFS же его спускать в виде ссылки, чтобы получилось что‑то вроде:
    (DFS сделаем чистым)
    
    methodWrapper(...)              <- тут как в задаче параметры
    {
      ArrayList<> pathResults...    <- место, куда результаты складываем
      dfs(rootNode, pathAsString, pathResults)
    
      return pathResults;           <- что от нас задача и хочет вернуть
    }

(3) Порядок кодирования

    Пишем враппер‑хелпер.
    Размещаем туда массив строк.
    Пишем DFS сразу же со строкой входящей.
    
    Программируем DFS.
    Дополняем два условия:
    1. деление строки;
    2. сохранение строки в результат.
    
    Из сложного у нас сверху DFS будет выбор:
    - открывать ли новый путь (начинать его со значения "_") - 
      в этом случае добавляем начальное значение;
    - или продолжать после наличия значения _->_ - в этом случае продолжаем цепочку:
    сначала добавляем "->", а потом выставляем своё значение.
    
    String newPath = currentPath.isEmpty()
      ? String.valueOf(node.val)
      : currentPath + "->" + node.val;
    
    Как ни странно, мне кажется, этот кусок - самая сложная часть алгоритма,
    так как требует понимания того, как формируется цепочка "_->_->_".
    Но здесь достаточно просто посмотреть на пример, что стоит выше,
    чтобы понять, что происходит:
    - если в строке пока ничего нет - добавляем просто значение;
    - если в строке что‑то уже есть - добавляем сначала "->", а потом значение.
    
    Логическое дробление создаётся на момент передачи строк в DFS.
    Несмотря на то что это звучит как «делить строку на варианты», в реальности
    это деление, как видим, происходит простой передачей параметров -
    когда строка сама собой и разделилась.
    
    Строка в Java по значению копируется параметром. Таким образом, в этом месте
    была одна строка на входе, а станет ДВЕ на выходе:
    
    if (node.left != null) {
      dfs(node.left, newPath, result);
    }
    
    if (node.right != null) {
      dfs(node.right, newPath, result);
    }
    
    Ну и остаётся понять, когда сохранять.
    Сохранять нужно, когда потомка нет - причём с двух сторон одновременно.
    Только в случае, если вообще некуда идти (обе стороны null),
    это и есть конец пути:
    
    if (node.left == null && node.right == null) {
      result.add(newPath);
      return;
    }
    
    И код в итоге, как видим, получается крайне лаконичным
    (рекурсивная версия - не итеративная).
* */
public class BinaryTreeTraversal_BinaryTreePaths {

  public class TreeNode {
    int val;
    TreeNode left;
    TreeNode right;
    TreeNode() {}
    TreeNode(int val) { this.val = val; }
    TreeNode(int val, TreeNode left, TreeNode right) {
      this.val = val;
      this.left = left;
      this.right = right;
    }
  }

  public class Solution {
    public List<String> binaryTreePaths(TreeNode root) {
      List<String> result = new ArrayList<>();
      if (root == null) {
        return result;
      }
      dfs(root, "", result);
      return result;
    }

    private void dfs(TreeNode node, String currentPath, List<String> result) {

      String newPath = currentPath.isEmpty()
                       ? String.valueOf(node.val)
                       : currentPath + "->" + node.val;

      if (node.left == null && node.right == null) {
        result.add(newPath);
        return;
      }

      if (node.left != null) {
        dfs(node.left, newPath, result);
      }

      if (node.right != null) {
        dfs(node.right, newPath, result);
      }
    }
  }

  public class SolutionIterative {

    public List<String> binaryTreePaths(TreeNode root) {

      List<String> result = new ArrayList<>();
      if (root == null) return result;

      Stack<TreeNode> nodeStack = new Stack<>();
      Stack<String> pathStack = new Stack<>();

      nodeStack.push(root);
      pathStack.push(String.valueOf(root.val));

      while (!nodeStack.isEmpty()) {
        TreeNode node = nodeStack.pop();
        String path = pathStack.pop();

        if (node.left == null && node.right == null) {
          result.add(path);
        }

        if (node.right != null) {
          nodeStack.push(node.right);
          pathStack.push(path + "->" + node.right.val);
        }

        if (node.left != null) {
          nodeStack.push(node.left);
          pathStack.push(path + "->" + node.left.val);
        }
      }

      return result;
    }
  }

  static void main() {
    var wrapper = new BinaryTreeTraversal_BinaryTreePaths();
    var solution = wrapper.new Solution();
    var solutionIterative = wrapper.new SolutionIterative();

    // дерево:
    //     1
    //    / \
    //   2   3
    //    \
    //     5

    TreeNode root = wrapper.new TreeNode(1);
    root.left = wrapper.new TreeNode(2);
    root.right = wrapper.new TreeNode(3);
    root.left.right = wrapper.new TreeNode(5);

    // ожидаем: [1->2->5, 1->3]
    out(solution.binaryTreePaths(root));
    out(solutionIterative.binaryTreePaths(root));
    out("");
  }

}//c:BinaryTreeTraversal_BinaryTreePaths

```
