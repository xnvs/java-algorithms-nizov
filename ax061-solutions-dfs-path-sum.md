[Оглавление](README.md)

Решебник. DepthFirstSearch_PathSum
===
```java
package ru.pragm;

import static ru.pragm.utils.Out.out;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;
import java.util.Stack;

/*
Задача:
DepthFirstSearch
PathSum
---------------------------------
    Условие: дан корень бинарного дерева и целое число targetSum.
    Вернуть все пути от корня к листьям, где сумма значений узлов пути равна targetSum.

(1) Формализация задачи и её суть    
    Что от нас хотят: у нас есть дерево:

           5
          / \
         4   8
        /   / \
       11  13  4
      /  \    / \
     7    2  5   1

    И нам надо найти пути по этому дереву - то есть провалы сверху вниз,
    в которых общая сумма будет равняться некоторой targetSum.
    Например, при targetSum = 22 этими путями станут:
    5 -> 4 -> 11 -> 2 (5+4+11+2=22)
    5 -> 8 -> 4 -> 5 (5+8+4+5=22)
    
    Как мы понимаем, это история просто по определению - история про DFS,
    который как раз и реализует все пути внутри дерева,
    и сохранение только тех путей, которые в сумме дают targetSum.

(2) Идея решения

    По сути, логику DFS, которая даёт именно то, что от нас хотят, мы
    уже видели в задаче BinaryTreePaths, где мы готовили к распечатке
    каждый из путей, возможных в дереве (а я настоятельно рекомендую
    рассмотреть ту задачу и её решение, прежде чем смотреть эту).
    
    В нашей же задаче это, по сути, то же самое,
    за исключением того, что нам надо сосредоточиться на соответствии targetSum,
    двигаясь по какому‑то пути и сохранить его.
    
    Давайте напомним задачу про BinaryTreePaths:

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

    Как видите, там у нас, собственно, DFS.
    А для того чтобы смотреть на то, чего мы достигли к моменту выхода на каждый
    этап рекурсии, используется currentPath.
    
    У нас же в этой задаче это, очевидно, будет не строка, а либо
    accumulatedSum к моменту прихода к ветке - аккумулирующий сумму,
    либо remainingSum к моменту прихода к ветке - уменьшающий значение,
    представляющее собой отрыв до targetSum.
    
    В классике обычно используют remainingSum.
    
    Но это ещё не всё. Если в задаче с BinaryTreePaths сам путь представляла
    сама строка, то в этой задаче от нас уже требуется, чтобы был какой‑то массив,
    который её представляет. Отчего в параметры рекурсивной функции‑метода
    по определению ещё и войдёт List<Integer> currentPath.
    
    А для того чтобы была возможность в нужный момент
    (когда он настанет, мы поговорим чуть позже) сохранить в результате
    currentPath, мы должны передавать ещё и результирующий массив массивов,
    обычно называемый result (опять же, аналогично с задачей BinaryTreePaths).

(3) Порядок кодирования

    На верхнем уровне задачи на этот момент всё понятно.
    Давайте теперь смотреть кодирование, так как оно, как часто бывает, не самое
    очевидное из‑за возникающих оптимизаций.
    
    И того, что в классическом решении достигнутое значение не аккумулируется
    и не сравнивается с достаточностью того, что на аккумулировалось,
    а уменьшается, что приводит к очень неочевидной оптимизации.
    
    Смотрите: например, наше целевое значение - 30,
    и каждая ветка - это 10 (это не бинарное дерево, но для этого примера
    подойдёт отлично).
    
    Первый уровень рекурсии: мы передаём 30 - это наш remainingSum.
    Значение на ноде - 10. На второй уровень рекурсии передаётся уже 20.
    Значение на ноде снова 10 - и на третий уровень рекурсии передаётся 10.
    
    И вот в чём оптимизация: достижение нужного уровня по сути становится
    сравнением с node.val и равенством node.val с remainingSum.
    
    if (node.left == null && node.right == null &&
        node.val == remainingSum)        <- мы можем сравнивать с node.val
    {}
    
    Поняв, что происходит, у нас по сути появляются логичные компоненты кода
    в части dfs (часть враппера нам и так очевидна):
    
    (1) Первый компонент - это, как ни странно, параметры функции‑метода
    рекурсии. Мы понимаем, что нам нужно для того, чтобы задачу решить.
    Это будут:
    
    TreeNode node                 <- текущая нода
    int remainingSum              <- логический остаток от targetSum с текущим путём
    List<Integer> currentPath    <- текущий путь в виде массива
    List<List<Integer>> result   <- общий результат сохранённых путей по условию
    
    (2) Второй логичный и понятный элемент кода - это выход из рекурсии.
    Он прост и понятен: если мы достигли node == null, значит,
    рекурсия дошла до конца дерева и пора откатываться.
    
    if (node == null) {
        return;
    }
    
    (3) Третий элемент - это добавление в currentPath текущего значения.
    Нам нужно с чем‑то двигаться по ветвям рекурсии:
    
    currentPath.add(node.val);
    
    (4) Четвёртый элемент - это сохранение накопленного currentPath в result
    при достижении уже рассмотренного условия:
    
    if (node.left == null && node.right == null && remainingSum == node.val) {
        result.add(new ArrayList<>(currentPath));
    }
    
    Здесь, кстати, интересно то, что если бы путь считался валидным,
    не достигнув конца дерева, то условие node.left == null / node.right == null
    было бы удалено. Например, мы нашли targetSum в середине дерева,
    двигаясь по нему каким‑либо путём.
    
    (5) Пятый элемент - это собственно движение по ветвям DFS
    в левую и в правую стороны. Здесь всё абсолютно понятно:
    двигаясь в каждую сторону, нам нужно уменьшить remainingSum
    на значение node.val текущего узла.
    
    dfs(node.left, remainingSum - node.val, currentDesktop, result);
    dfs(node.right, remainingSum - node.val, currentPath, result);
    
    (6) И вот шестой элемент - абсолютно неочевидный на первый взгляд,
    и его очень легко забыть. Это удаление из currentPath
    (массива) по факту возврата из функции‑метода рекурсии
    последнего добавленного элемента:
    
    currentPath.remove(currentPath.size() - 1);
    
    По сути, currentPath - это промежуточное вычисление
    на текущем этапе рекурсии, и совсем не факт, что оно пригодится дальше.
    
    По сути, currentPath в этом плане очень похож на очередь в BFS:
    значение от текущего вызова рекурсии актуально для текущего узла
    (чтобы была возможность его сохранить в результирующем массиве)
    и для передачи по ветвям вызова рекурсии - влево и вправо вниз.
    
    Но при поднятии вверх по рекурсии это значение из промежуточного
    результата должно быть откачено.
    
    Это порождает некоторые мысли. Например, взгляните на дерево
    из примера и представьте, что у нас некорректная «нога» растёт дальше,
    как я здесь показываю:

                   5
                  / \
                 4   8
                /   / \
               11  13  4
              /  \    / \
             7    2  5   1
            /
           x
          /
         y
        /
       z

    Здесь и (x), и (y), и (z) породят потребность добавления элемента в массив
    и удаления его из массива - просто потому, что так работает алгоритм.
    А если «нога» будет длинной, то это будут буквально лишние операции.
    
    Но классическое решение это игнорирует и даёт решение в том виде,
    как показано дальше.
    
    Здесь же я дам ещё решение на базе не системного стека (рекурсии),
    а ручного. Мы его смотреть не будем, так как это классическое преобразование
    логики рекурсии в итерацию, и оно будет медленнее в большинстве случаев
    (системный стек всё‑таки работает значительно быстрее).

* */
public class DepthFirstSearch_PathSum {

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

    public List<List<Integer>> pathSum(TreeNode root, int targetSum) {
      List<List<Integer>> result = new ArrayList<>();
      List<Integer> currentPath = new ArrayList<>();
      dfs(root, targetSum, currentPath, result);
      return result;
    }

    private void dfs(TreeNode node,
                     int remainingSum,
                     List<Integer> currentPath,
                     List<List<Integer>> result) {
      if (node == null) {
        return;
      }

      currentPath.add(node.val);

      if (node.left == null && node.right == null && remainingSum == node.val) {
        result.add(new ArrayList<>(currentPath));
      }

      dfs(node.left, remainingSum - node.val, currentPath, result);
      dfs(node.right, remainingSum - node.val, currentPath, result);

      currentPath.remove(currentPath.size() - 1);
    }
  }

  public class SolutionIterative {
    public List<List<Integer>> pathSum(TreeNode root, int targetSum) {
      List<List<Integer>> result = new ArrayList<>();
      if (root == null) return result;

      Stack<TreeNode> nodeStack = new Stack<>();
      Stack<List<Integer>> pathStack = new Stack<>();
      Stack<Integer> sumStack = new Stack<>();

      nodeStack.push(root);
      pathStack.push(new ArrayList<>(Arrays.asList(root.val)));
      sumStack.push(targetSum - root.val);

      while (!nodeStack.isEmpty()) {
        TreeNode node = nodeStack.pop();
        List<Integer> currentPath = pathStack.pop();
        int currentSum = sumStack.pop();

        if (node.left == null && node.right == null && currentSum == 0) {
          result.add(new ArrayList<>(currentPath));
        }

        if (node.right != null) {
          nodeStack.push(node.right);
          List<Integer> newPath = new ArrayList<>(currentPath);
          newPath.add(node.right.val);
          pathStack.push(newPath);
          sumStack.push(currentSum - node.right.val);
        }

        if (node.left != null) {
          nodeStack.push(node.left);
          List<Integer> newPath = new ArrayList<>(currentPath);
          newPath.add(node.left.val);
          pathStack.push(newPath);
          sumStack.push(currentSum - node.left.val);
        }
      }

      return result;
    }
  }

  static void main() {
    var wrapper = new DepthFirstSearch_PathSum();
    var solution = wrapper.new Solution();
    var solutionIterative = wrapper.new SolutionIterative();

    var node7 = wrapper.new TreeNode(7);
    var node2 = wrapper.new TreeNode(2);
    var node11 = wrapper.new TreeNode(11, node7, node2);
    var node4_left = wrapper.new TreeNode(4, node11, null);

    var node13 = wrapper.new TreeNode(13);
    var node5_right = wrapper.new TreeNode(5);
    var node1 = wrapper.new TreeNode(1);
    var node4_right = wrapper.new TreeNode(4, node5_right, node1);
    var node8 = wrapper.new TreeNode(8, node13, node4_right);

    var root = wrapper.new TreeNode(5, node4_left, node8);

    int targetSum = 22;

    out(solution.pathSum(root, targetSum));
    out(solutionIterative.pathSum(root, targetSum));
  }

}//c:DepthFirstSearch_PathSumII

```