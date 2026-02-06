[Оглавление](README.md)

Решебник. Путешествия по деревьям
=================================
```java
package ru.pragm;

import java.util.*;

import static ru.pragm.utils.Out.out;

/*
    Путешествия по деревьям
    -----------------------
    
    Я специально приведу примеры путешествий по деревьям в разном стиле,
    чтобы их можно было собрать в одном месте и сравнить.
    
    У нас будут следующие варианты:
    
    1. DFS классический.
    2. DFS с цветами для графов (0 - не посещено, 1 - посещается, 2 - посещено).
    3. BFS классический (в очередь попало - из очереди выбыло).
    4. BFS с накоплением уровня (сначала копится уровень, потом обрабатывается).
    5. BFS c Kahn (вариация обработки графов с циклами).
       Обычно используется для топологической сортировки и DAG (Directed Acyclic Graph).
    6. BFS с несколькими источниками движения.
    7. DFS с несколькими источниками движения.
    
    Плюс - классический итеративный волновой подход, считающийся брутфорсом.
    В нём мы создаём параллельную структуру данных, в которой отслеживаем движение,
    и обрабатываем шаг на базе тех нод графа, которые достигли к текущему моменту:
    1. Мы знаем, в каких нодах находимся (обработали на этом шаге).
    2. Мы знаем, какие соседи там есть (ноды для обработки на следующем шаге).
    3. Мы обрабатываем следующий шаг.
    
    Его обычно делят на логику времени today/tomorrow или currentStep/nextStep.
    
    Итеративно-волновой подход позволяет также обрабатывать несколько источников за раз,
    но проигрывает во времени и памяти - например, на задаче с апельсинами:
    
      итеративно волновой     BFS                 DFS
      O(d*m*n)/O(2*m*n)       O(m*n)/O(m*n)       O(m*n*k)/O(m*n)
    
    Но его легко отлаживать и крайне просто представить визуально. Плюс, если требуется
    отслеживание именно движения как процесса, итеративно-волновой подход
    часто будет единственным вариантом. Он подходит для любых графов и деревьев
    в принципе и может быть темплеизирован.
    
    С какими деревьями и графами вы можете столкнуться - чтобы понимать,
    какой алгоритм где использовать предпочтительней:
    
    По стилю задания:
    -----------------
    
    - Представленные в виде AdjacencyList:
      а. List<Integer>[] adjList;
         Это массив, каждый элемент которого представляет собой ноду,
         представленную в виде массива, который описывает её соединения с другими нодами:
         adjList[0].add(1); // из 0 можно перейти в 1
         adjList[0].add(2); // из 0 можно перейти в 2
         adjList[1].add(3); // из 1 можно перейти в 3
      б. Упрощённый вариант - просто int[] adjList.
         В этом случае: индекс => нода, значение => нода, с которой соединены.
      в. Подвариант с adjacency matrix: int[][] adjacencyMatrix.
         Если между вершинами v1 и v2 есть ребро, то задаём так:
         adjacencyMatrix[v1][v2] = 1;
    
    - Представленные в виде нод, исходящих от корня:
      а. Для общего случая:
         class Node {
           public int val;
           public List<Node> neighbors;
           public int state;
    
           public Node(int _val) {
             val = _val;
             neighbors = new ArrayList<>();
             state = 0;
           }
         }
      б. Для частного случая:
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
         В этом случае граф представляется просто корневой нодой: Node root;
    
    - Представленные в виде массива нод.
      Используются для разорванных графов - как в примере с зависимыми курсами.
      Например, если у нас есть набор курсов, в которых:
      «линейная алгебра» зависит от «базовой математики»,
      «история Рима» зависит от «древней истории».
      У нас по сути два графа, не связанных друг с другом, но логически
      представляющих собой абстракцию «курсы»: Node[] nodes;
    
    По общему виду графа:
    ---------------------
    
    Дерево - это вид графа, который имеет один общий корень, от которого исходят ветви,
    и который в обязательном порядке заканчивается нодой, в которой null в любом ответвлении.
    Дерево, в отличие от графа, имеет чёткое начало (верх) и чёткое окончание (снизу).
    Дерево всегда является ацикличным графом.
    
    Однако это не всё. Технически графы делятся на:
    - циклический - в графе могут встречаться петли;
    - ациклический - петли не встречаются;
    - разорванный - как в примере с курсами.

          (1)   (4)
         /   \ /
       (2)   (3)     <- от третьего курса зависит и (1) и (4)

    Передаётся как:
    (1) neighbors: (2),(3)
    (4) neighbors: (3)
    
    По подвидам деревьев:
    ---------------------
    
    1. Классическое бинарное дерево.
       Это дерево без циклов, где каждое значение делится ещё на два -
       безо всякого дополнительного смысла (значения с любой стороны любые).
       Показатель того, что мы дошли до конца дерева - null в виде двух указателей.
    
    2. Дерево, в котором ветви в отдельной ноде передают несколько соседей.
       Трактуется как обычное дерево, но обрабатываются не left-right, а целый массив.
       По сути, такое дерево - это ациклический граф.
    
    3. Классическое BST.
       Упорядоченное дерево: слева всегда меньшее значение, справа - всегда большее.
       При использовании DFS классического мы всегда будем обходить дерево по порядку
       размещения значений (обведение по контуру).
       Ключевой момент: начало обхода дерева для DFS - не корень дерева (который может
       быть любым), а значение, до которого мы доходим, всегда смещаясь к меньшему
       значению (обычно всегда влево). Это и есть точка начала данных.
    
    По режиму работы DFS / BFS:
    ---------------------------
    
    1. Режим, когда DFS/BFS двигается с одного источника - single source режим.
    2. Режим, когда DFS/BFS двигается с нескольких источников - multiple source режим,
       как в задаче с rotten oranges.
    
       Логика multiple source режима проявляется в том числе на разорванных графах -
       так как там одна и та же нода может относиться к нескольким разным графам
       (несколько источников строят несколько разных графов на базе одного набора нодов).

* */
public class BaseAlgorithmicFramework_001_TreeTraversalsSummary {

  // ========== 1. DFS классический (рекурсивный) ==========
  // Лучше всего для деревьев (особенно бинарных)
  // Время: O(n), Память: O(h) - высота дерева

  // Пример для бинарного дерева (TreeNode)
  public void dfsClassic(TreeNode root) {
    if (root == null) return;

    // Здесь порядок важен:

    // Pre-order: посещаем корень ДО детей
    //out(root.val + " ");
    //dfsClassic(root.left);
    //dfsClassic(root.right);

    // In-order (для BST дает отсортированный порядок)
    dfsClassic(root.left);
    out(root.val + " ");
    dfsClassic(root.right);

    // Post-order: посещаем корень ПОСЛЕ детей
    // dfsClassic(root.left);
    // dfsClassic(root.right);
    // out(root.val + " ");
  }

  // Пример для дерева с произвольным количеством детей (Node)
  public void dfsClassic(Node root) {
    if (root == null) return;

    out(root.val + " ");
    for (Node child : root.neighbors) {
      dfsClassic(child);
    }
  }

  // ========== 2. DFS с цветами для графов ==========
  // Обрабатывает любые графы, включая циклические
  // Обнаружение циклов, топологическая сортировка
  // Время: O(V + E), Память: O(V)

  public boolean dfsWithColors(Node[] graph) {
    // 0 - не посещено (WHITE)
    // 1 - в процессе посещения (GRAY)
    // 2 - полностью посещено (BLACK)
    int[] colors = new int[graph.length];

    for (int i = 0; i < graph.length; i++) {
      if (colors[i] == 0) {
        if (hasCycleDFS(graph, i, colors)) {
          return true; // найден цикл
        }
      }
    }
    return false;
  }

  private boolean hasCycleDFS(Node[] graph, int node, int[] colors) {
    colors[node] = 1; // посещаем

    for (Node neighbor : graph[node].neighbors) {
      if (colors[neighbor.val] == 0) {
        if (hasCycleDFS(graph, neighbor.val, colors)) {
          return true;
        }
      } else if (colors[neighbor.val] == 1) {
        return true; // нашли обратное ребро - цикл!
      }
    }

    colors[node] = 2; // завершили
    return false;
  }

  // ========== 3. BFS классический ==========
  // Поиск кратчайшего пути (в невзвешенном графе)
  // Поуровневая обработка
  // Время: O(V + E), Память: O(V)

  public void bfsClassic(TreeNode root) {
    if (root == null) return;

    Queue<TreeNode> queue = new LinkedList<>();
    queue.offer(root);

    while (!queue.isEmpty()) {
      TreeNode current = queue.poll();
      out(current.val + " ");

      if (current.left != null) {
        queue.offer(current.left);
      }
      if (current.right != null) {
        queue.offer(current.right);
      }
    }
  }

  // Для графа в виде списка смежности
  public void bfsClassic(int start, List<Integer>[] adjList) {
    boolean[] visited = new boolean[adjList.length];
    Queue<Integer> queue = new LinkedList<>();

    queue.offer(start);
    visited[start] = true;

    while (!queue.isEmpty()) {
      int current = queue.poll();
      out(current + " ");

      for (int neighbor : adjList[current]) {
        if (!visited[neighbor]) {
          visited[neighbor] = true;
          queue.offer(neighbor);
        }
      }
    }
  }

  // ========== 4. BFS с накоплением уровня ==========
  // Когда нужна информация о каждом уровне отдельно
  // Находит минимальную глубину, сохраняет структуру уровней

  public List<List<Integer>> bfsLevelByLevel(TreeNode root) {
    List<List<Integer>> result = new ArrayList<>();
    if (root == null) return result;

    Queue<TreeNode> queue = new LinkedList<>();
    queue.offer(root);

    while (!queue.isEmpty()) {
      int levelSize = queue.size();
      List<Integer> currentLevel = new ArrayList<>();

      for (int i = 0; i < levelSize; i++) {
        TreeNode current = queue.poll();
        currentLevel.add(current.val);

        if (current.left != null) {
          queue.offer(current.left);
        }
        if (current.right != null) {
          queue.offer(current.right);
        }
      }
      result.add(currentLevel);
    }
    return result;
  }

  // Пример: поиск минимальной глубины бинарного дерева
  public int minDepth(TreeNode root) {
    if (root == null) return 0;

    Queue<TreeNode> queue = new LinkedList<>();
    queue.offer(root);
    int depth = 1;

    while (!queue.isEmpty()) {
      int levelSize = queue.size();

      for (int i = 0; i < levelSize; i++) {
        TreeNode current = queue.poll();

        // Первый лист на этом уровне
        if (current.left == null && current.right == null) {
          return depth;
        }

        if (current.left != null) queue.offer(current.left);
        if (current.right != null) queue.offer(current.right);
      }
      depth++;
    }
    return depth;
  }

  // ========== 5. BFS с алгоритмом Кана ==========
  // Топологическая сортировка для DAG (Directed Acyclic Graph)
  // Обнаружение циклов через подсчет входящих рёбер
  // Время: O(V + E), Память: O(V)

  public List<Integer> topologicalSortKahn(int numCourses, int[][] prerequisites) {
    // Строим граф и массив входящих степеней
    List<Integer>[] adjList = new ArrayList[numCourses];
    int[] inDegree = new int[numCourses];

    for (int i = 0; i < numCourses; i++) {
      adjList[i] = new ArrayList<>();
    }

    for (int[] prereq : prerequisites) {
      int course = prereq[0];
      int dependency = prereq[1];
      adjList[dependency].add(course);
      inDegree[course]++;
    }

    // Очередь вершин с нулевой входящей степенью
    Queue<Integer> queue = new LinkedList<>();
    for (int i = 0; i < numCourses; i++) {
      if (inDegree[i] == 0) {
        queue.offer(i);
      }
    }

    List<Integer> result = new ArrayList<>();
    int visitedCount = 0;

    while (!queue.isEmpty()) {
      int current = queue.poll();
      result.add(current);
      visitedCount++;

      for (int neighbor : adjList[current]) {
        inDegree[neighbor]--;
        if (inDegree[neighbor] == 0) {
          queue.offer(neighbor);
        }
      }
    }

    // Если посетили не все вершины - есть цикл
    if (visitedCount != numCourses) {
      return new ArrayList<>(); // или выбросить исключение
    }

    return result;
  }

  // ========== 6. BFS с несколькими источниками (Multi-source BFS) ==========
  // Задача Rotten Oranges, распространение волны из нескольких начальных точек
  // Время: O(m*n), Память: O(m*n)

  public int orangesRotting(int[][] grid) {
    int rows = grid.length;
    int cols = grid[0].length;

    Queue<int[]> queue = new LinkedList<>();
    int freshCount = 0;
    int minutes = 0;

    // 1. Находим все гнилые апельсины (множественные источники)
    for (int i = 0; i < rows; i++) {
      for (int j = 0; j < cols; j++) {
        if (grid[i][j] == 2) {
          queue.offer(new int[]{i, j});
        } else if (grid[i][j] == 1) {
          freshCount++;
        }
      }
    }

    // Если нет свежих апельсинов
    if (freshCount == 0) return 0;

    // Направления: вверх, вниз, влево, вправо
    int[][] directions = {{-1, 0}, {1, 0}, {0, -1}, {0, 1}};

    // 2. BFS из нескольких источников одновременно
    while (!queue.isEmpty() && freshCount > 0) {
      int size = queue.size();

      // Обрабатываем все гнилые апельсины на текущей минуте
      for (int i = 0; i < size; i++) {
        int[] current = queue.poll();
        int x = current[0];
        int y = current[1];

        // Проверяем всех соседей
        for (int[] dir : directions) {
          int newX = x + dir[0];
          int newY = y + dir[1];

          // Если соседний апельсин свежий
          if (newX >= 0 && newX < rows &&
              newY >= 0 && newY < cols &&
              grid[newX][newY] == 1) {

            // Гноим его
            grid[newX][newY] = 2;
            queue.offer(new int[]{newX, newY});
            freshCount--;
          }
        }
      }
      minutes++; // Прошла еще одна минута
    }

    return freshCount == 0 ? minutes : -1;
  }

  // ========== 7. DFS с несколькими источниками ==========
  // Задача Rotten Oranges с использованием DFS (аналог BFS с несколькими источниками)
  // Использует мемоизацию времени и отсечение для гарантии минимального времени
  // Время: O(m*n * k) где k - количество источников, Память: O(m*n)

  public int orangesRottingDFS(int[][] grid) {
    if (grid == null || grid.length == 0) return -1;

    int rows = grid.length, cols = grid[0].length;
    int[][] minTime = new int[rows][cols];

    // Инициализируем массив времени: Integer.MAX_VALUE = еще не гниет
    for (int i = 0; i < rows; i++) {
      Arrays.fill(minTime[i], Integer.MAX_VALUE);
    }

    // 1. Находим все начальные источники (гнилые апельсины)
    //    и запускаем DFS из каждого из них
    for (int i = 0; i < rows; i++) {
      for (int j = 0; j < cols; j++) {
        if (grid[i][j] == 2) {
          // Запускаем DFS с временем 0 от каждого источника
          dfsFromSource(grid, minTime, i, j, 0);
        }
      }
    }

    // 2. Проверяем результат:
    //    - Находим максимальное время среди всех апельсинов
    //    - Если остались апельсины со временем Integer.MAX_VALUE - они свежие
    int maxTime = 0;
    for (int i = 0; i < rows; i++) {
      for (int j = 0; j < cols; j++) {
        if (grid[i][j] == 1) {
          if (minTime[i][j] == Integer.MAX_VALUE) {
            return -1; // остался свежий апельсин
          }
          maxTime = Math.max(maxTime, minTime[i][j]);
        }
      }
    }

    return maxTime;
  }

  private void dfsFromSource(int[][] grid, int[][] minTime,
                             int i, int j, int currentTime) {
    // Выход за границы или пустая ячейка
    if (i < 0 || i >= grid.length || j < 0 || j >= grid[0].length
        || grid[i][j] == 0) {
      return;
    }

    // КЛЮЧЕВАЯ ОПТИМИЗАЦИЯ:
    // Если пришли с худшим (большим) временем, чем уже найденное - отсекаем
    if (currentTime >= minTime[i][j]) {
      return;
    }

    // Сохраняем лучшее (минимальное) время для этой ячейки
    minTime[i][j] = currentTime;

    // Рекурсивно распространяем гниение во все 4 направления
    dfsFromSource(grid, minTime, i - 1, j, currentTime + 1); // вверх
    dfsFromSource(grid, minTime, i + 1, j, currentTime + 1); // вниз
    dfsFromSource(grid, minTime, i, j - 1, currentTime + 1); // влево
    dfsFromSource(grid, minTime, i, j + 1, currentTime + 1); // вправо
  }

  // ========== 8. Итеративный волновой алгоритм ==========
  // "День за днем" - брут-форс подход для любых деревьев и графов
  // Легко визуализировать, легко отлаживать, работает для любых задач
  // Время: O(d * V * E) в худшем случае, Память: O(2 * V)

  public int orangesRottingIterativeWave(int[][] grid) {
    int rows = grid.length, cols = grid[0].length;

    // Глубокое копирование исходной сетки
    int[][] today = new int[rows][cols];
    for (int i = 0; i < rows; i++) {
      today[i] = grid[i].clone();
    }

    int[][] tomorrow = new int[rows][cols];
    int minutes = 0, fresh = 0;

    // Считаем свежие и копируем grid в tomorrow для начального состояния
    for (int i = 0; i < rows; i++) {
      tomorrow[i] = grid[i].clone();
      for (int j = 0; j < cols; j++) {
        if (grid[i][j] == 1) fresh++;
      }
    }

    if (fresh == 0) return 0;

    int[][] dirs = {{-1,0},{1,0},{0,-1},{0,1}};

    while (true) {
      boolean changed = false;

      // Копируем today из предыдущего tomorrow
      for (int i = 0; i < rows; i++) {
        today[i] = tomorrow[i].clone();
      }

      // Обрабатываем "сегодняшний день"
      for (int i = 0; i < rows; i++) {
        for (int j = 0; j < cols; j++) {
          if (today[i][j] == 2) {
            for (int[] d : dirs) {
              int x = i + d[0], y = j + d[1];
              if (x >= 0 && x < rows && y >= 0 && y < cols
                  && tomorrow[x][y] == 1) {
                tomorrow[x][y] = 2;
                fresh--;
                changed = true;
              }
            }
          }
        }
      }

      if (!changed) break;
      minutes++;

      if (fresh == 0) return minutes;
    }

    return -1;
  }

  // ========== Обобщенная версия для любых графов ==========
  // Работает с любым представлением графа через интерфейс

  interface GraphProcessor<T> {
    List<T> getNeighbors(T node);
    boolean isTarget(T node);
    boolean isSource(T node);
    void markAsVisited(T node);
  }

  public <T> int iterativeWaveForGraph(T[] allNodes, GraphProcessor<T> processor) {
    // Текущее состояние: какие ноды активны на этом шаге
    Set<T> currentActive = new HashSet<>();
    Set<T> nextActive = new HashSet<>();

    int steps = 0;
    boolean anyChange;

    // Инициализация: находим все начальные ноды
    for (T node : allNodes) {
      if (processor.isSource(node)) {
        currentActive.add(node);
        processor.markAsVisited(node);
      }
    }

    do {
      anyChange = false;
      nextActive.clear();

      // Обрабатываем все активные ноды на текущем шаге
      for (T node : currentActive) {
        // Получаем соседей
        for (T neighbor : processor.getNeighbors(node)) {
          // Если сосед еще не посещен и не является целью/препятствием
          if (!processor.isTarget(neighbor)) {
            nextActive.add(neighbor);
            processor.markAsVisited(neighbor);
            anyChange = true;
          }
        }
      }

      // Если были изменения - переходим к следующему шагу
      if (anyChange) {
        // Меняем множества местами
        Set<T> temp = currentActive;
        currentActive = nextActive;
        nextActive = temp;
        steps++;
      }

    } while (anyChange);

    // Проверяем, все ли целевые ноды достигнуты
    for (T node : allNodes) {
      if (processor.isTarget(node)) {
        return -1; // не все цели достигнуты
      }
    }

    return steps;
  }

  // ========== представления нод ==========
  // нода для дерева
  // нода для графа
  static class TreeNode {
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

  static class Node {
    public int val;
    public List<Node> neighbors;
    public int state;

    public Node(int _val) {
      val = _val;
      neighbors = new ArrayList<>();
      state = 0;
    }
  }

  static void main(String[] args) {
    BaseAlgorithmicFramework_001_TreeTraversalsSummary
        solver = new BaseAlgorithmicFramework_001_TreeTraversalsSummary();

    var bst = new TreeNode(4,
              new TreeNode(2, new TreeNode(1), new TreeNode(3)),
              new TreeNode(6, new TreeNode(5), new TreeNode(7))
    );

    out("DFS классический (in-order для BST):");
    solver.dfsClassic(bst);
    out("\n");

    out("BFS по уровням:");
    List<List<Integer>> levels = solver.bfsLevelByLevel(bst);
    for (int i = 0; i < levels.size(); i++) {
      out("Уровень " + i + ": " + levels.get(i));
    }
    out("");

    int numCourses = 6;
    int[][] prerequisites = {
        {1, 0}, {2, 1}, {3, 1},
        {4, 2}, {5, 3}, {5, 4}
    };

    out("Топологическая сортировка (алгоритм Кана):");
    List<Integer> order = solver.topologicalSortKahn(numCourses, prerequisites);
    out(order);
  }
}
```
