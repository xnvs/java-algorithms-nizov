[Оглавление](README.md)

Решебник: Шпаргалка для повторения
===

В качестве шпаргалки на "быстро посмотреть", -
представлю здесь каждую из задач, с быстрым разбором компонентов
кода.

От себя замечу, -
шпараглка будет работать только лишь в случае, если вы до момента её использования
разобрались с теорией и посмотрели полный разбор задачи.

Так как вы изначально должны понимать, что за задачей стоит
прежде чем быстро её вспомнить "как она выглядит" и "как кодировать"

Backtracking_Permutations
-------------------------
Дан массив различных целых чисел nums, вернуть все возможные
перестановки этих чисел. Можно вернуть ответ в любом порядке.
```Java
/*
1. понимаем: делаем генерацию через дфс/бектрек
2. вводим возврат (массив массивов)
3. вызываем бектрек передавая туда 
   а. массив возврата для накопления пермутаций
   б. массив который будет текущий путь вести для откатов
   в. начальный массив nums
4. программируем бектрек (это рекурсивный дфс)
   а. базовый кейс: если набрали в размер - сохраняем в результат
   б. цикл по всем значениям (соседи) для комбинаций
      -. если уже есть в комбинации игнорируем
      -. добавляем (если нет в комбинации) в путь
      -. запускаем бектрек дальше 
      -. убираем из пути если вернулись из бектрака
*/
public class Permutations {
    public List<List<Integer>> permute(int[] nums) {
        List<List<Integer>> result = new ArrayList<>();
        backtrack(result, new ArrayList<>(), nums);
        return result;
    }
    private void backtrack(List<List<Integer>> result, 
                           List<Integer> tempList, int[] nums) {
        
        if (tempList.size() == nums.length) {
            result.add(new ArrayList<>(tempList)); 
            return;
        }
        for (int i = 0; i < nums.length; i++) {
            
            if (tempList.contains(nums[i])) {
                continue;
            }
            
            tempList.add(nums[i]);            

            backtrack(result, tempList, nums);            

            tempList.remove(tempList.size() - 1);
        }
    }
}
```

Backtracking_NQueens
--------------------
Расставить N ферзей на шахматной доске N×N так,
чтобы ни один ферзь не атаковал другого
(никакие два ферзя не находились на одной строке, столбце или диагонали).
Вернуть все различные решения.
```Java
/*
1. понимаем: делаем генерацию через дфс/бектрек
2. вспомогательные методы
    -. метод возможности установки фигуры на гриде со стейтом
        а. тест на прямые линии
        б. тест на (\)
        в. тест на (/)
    -. метод преобразования char[][] в List<String>
3. основные методы
    -. метод запуска решения
        а. заполняет доску стейт '.'
        б. запускает бектрак с 0 строки 'n' позиции
        в. возврашает результат
    -. метод бектрака
        а. базовый кейс когда достигли последней строки
        б. для каждой строчки перебор
            -. попытка установить королеву
            -. бектрек к следующей строке
            -. откат на строку назад 
*/
public class NQueens {
    public List<List<String>> solveNQueens(int n) {
        List<List<String>> result = new ArrayList<>();
        
        char[][] board = new char[n][n];
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                board[i][j] = '.';
            }
        }
        backtrack(result, board, 0, n);
        return result;
    }
    private void backtrack(List<List<String>> result, 
                           char[][] board, int row, int n) {
        
        if (row == n) {
            result.add(constructSolution(board));
            return;
        }
        
        for (int col = 0; col < n; col++) {
            if (isValid(board, row, col, n)) {
                board[row][col] = 'Q'; 
                backtrack(result, board, row + 1, n); 
                board[row][col] = '.'; 
            }
        }
    }
    
    private boolean isValid(char[][] board, int row, int col, int n) {
        
        for (int i = 0; i < row; i++) {
            if (board[i][col] == 'Q') {
                return false;
            }
        }
    
        for (int i = row - 1, j = col - 1; i >= 0 && j >= 0; i--, j--) {
            if (board[i][j] == 'Q') {
                return false;
            }
        }
        
        for (int i = row - 1, j = col + 1; i >= 0 && j < n; i--, j++) {
            if (board[i][j] == 'Q') {
                return false;
            }
        }
        return true;
    }
    
    private List<String> constructSolution(char[][] board) {
        List<String> solution = new ArrayList<>();
        for (char[] row : board) {
            solution.add(new String(row));
        }
        return solution;
    }
}
```

BinaryTreeTraversal_BinaryTreeLevelOrderTraversal
-------------------------------------------------
Дано бинарное дерево. Вернуть обход его узлов по уровням (т.е.,
слева направо, уровень за уровнем).
```Java
/*
1. понимаем: подразумеваем BFS 
2. создаем очередь представляющую уровень обхода
3. создаем массив представляющий уровени для результата
4. цикл пока очередь обхода не пуста (классика BFS)
    -. создаем массив для одного уровня
    -. один уровень = обработка очереди 
    -. очередь освобождает уровень старый 
       и очередь наполняет уровень новый
    -. уровень сохраняется
5. возвращаем массив массивов уровней
*/

class Solution {
    public List<List<Integer>> levelOrder(TreeNode root) {
        List<List<Integer>> result = new ArrayList<>();
        
        if (root == null) {
            return result;
        }
        
        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(root); 
        
        while (!queue.isEmpty()) {
            int levelSize = queue.size(); 
            List<Integer> currentLevel = new ArrayList<>();
                        
            for (int i = 0; i < levelSize; i++) {
                TreeNode currentNode = queue.poll(); 
                currentLevel.add(currentNode.val); 
                                
                if (currentNode.left != null) {
                    queue.offer(currentNode.left);
                }
                if (currentNode.right != null) {
                    queue.offer(currentNode.right);
                }
            }
            
            result.add(currentLevel); 
        }
        
        return result;
    }
}
```

BinaryTreeTraversal_BinaryTreeMaximumPathSum
--------------------------------------------
Путь в бинарном дереве — это последовательность узлов, в которой каждая
пара смежных узлов соединена ребром. Узел может появляться в последовательности
не более одного раза. Путь не обязательно должен проходить через корень.
Сумма пути — это сумма значений узлов в пути.
Дано бинарное дерево. Найти максимальную сумму пути.
```Java
/*
1. понимаем: ищем арки путей через текущую ноду
2. понимаем: что будем работать с деревом и с аналогом DFS
3. создаем враппер согласно заданию 
4. создаем рекурсивный maxGain
    -. описываем базовый случае и возвращаем нуль
    -. рекурсия максимум от левой ветки или нуля
    -. рекурсия максимум от правой ветки или нуля
    -. считаем сумму арки left<-(текущая)->right
       (текущая + максимум слева + максимум справа)
    -. обновляем стат переменную (максимальная сумма)
    -. возвращаем максимальный путь вверх
       (текущее + что больше слева или справа)
*/

class Solution {
    private int maxSum = Integer.MIN_VALUE;
    
    public int maxPathSum(TreeNode root) {
        maxGain(root);
        return maxSum;
    }
    
    private int maxGain(TreeNode node) {

        if (node == null) {return 0;}
        
        int leftGain = Math.max(maxGain(node.left), 0);
        int rightGain = Math.max(maxGain(node.right), 0);
        
        int priceNewPath = node.val + leftGain + rightGain;
        
        maxSum = Math.max(maxSum, priceNewPath);
        
        return node.val + Math.max(leftGain, rightGain);
    }
}
```

BinaryTreeTraversal_BinaryTreePaths
-----------------------------------
Дано бинарное дерево. Вернуть все корневые-листовые пути в любом порядке.
```Java
/*
1. понимаем: возвращаем все уникальные пути dfs 
2. делаем враппер согласно задачи, куда размещаем массив путей
3. запускаем dfs передавая туда 
    а. ноду
    б. текущий путь 
    в. массив путей
4. в рекурсивном дфс:
    -. проверяем создавать ли новый путь или добавлять "->"
    -. если дошли до конца дерева возвращаем текущий путь вверх
    -. рекурсия в левую ветку, если она не пуста
    -. рекурсия в правую ветку, если она не пуста
*/

class Solution {
    public List<String> binaryTreePaths(TreeNode root) {
        List<String> paths = new ArrayList<>();
        if (root == null) {
            return paths;
        }
        dfs(root, "", paths);
        return paths;
    }
    
    private void dfs(TreeNode node, String currentPath, List<String> paths) {
        
        if (currentPath.isEmpty()) {
            currentPath = String.valueOf(node.val);
        } else {
            currentPath += "->" + node.val;
        }        
        
        if (node.left == null && node.right == null) {
            paths.add(currentPath);
            return;
        }
                
        if (node.left != null) {
            dfs(node.left, currentPath, paths);
        }
        if (node.right != null) {
            dfs(node.right, currentPath, paths);
        }
    }
}
```

BinaryTreeTraversal_KthSmallestElementInBST
-------------------------------------------
Дано корень BST и целое число k, вернуть k-й наименьший элемент (1-indexed).
```Java
/*
1. понимаем: бст уже отсортировал элементы, нужен лишь какой то по счету
2. создаем враппер в котором будем хранить массив из бст
3. заполняем массив с помощью дфс
4. возвращаем нужный элемент из массива
*/

class Solution {
    public int kthSmallest(TreeNode root, int k) {
        List<Integer> inorderList = new ArrayList<>();
        inorderTraversal(root, inorderList);
        return inorderList.get(k - 1); 
    }
    
    private void inorderTraversal(TreeNode node, List<Integer> list) {
        if (node == null) return;
        
        inorderTraversal(node.left, list);  
        list.add(node.val);                 
        inorderTraversal(node.right, list); 
    }
}
```

BreadthFirstSearch_RottingOranges
---------------------------------
Дана сетка m x n, где каждая ячейка может иметь одно из трех значений:
0 — пустая ячейка, 1 — свежий апельсин, 2 — гнилой апельсин
Каждую минуту любой свежий апельсин, соседствующий (по 4 направлениям)
с гнилым апельсином, становится гнилым. Вернуть минимальное количество минут,
через которое все апельсины станут гнилыми. Если это невозможно, вернуть -1.
```Java
/*
1. понимаем: распространение это бфс, минута = итерация движения
2. понимаем: используем поле как стейт будем переписывать значения прямо в поле
3. делаем очередь для бфс в каждом элементе очереди массив передающий {x,y} ячейки
4. учетом окончания движения будет исчерпание свежих апельсинов - считаем их 
5. в одном цикле:
    -. считаем свежие апельсины
    -. заполням очередь на уже сгнивших
6. создаем массив направлений движений
7. запускаем цикл пока в очереди есть элементы бфс
8. обрабатываем уровнями
   соседи = направления движения с проверкой на невыход 
        -. полностью отыгрываем текущий уровень
        -. заполняем новыми соседями от гниения
        -. при гниении убираем свежие апельсины
*/

class Solution {
    public int orangesRotting(int[][] grid) {
        int rows = grid.length;
        int cols = grid[0].length;
        
        Queue<int[]> queue = new LinkedList<>();
        int freshCount = 0;
                
        for (int r = 0; r < rows; r++) {
            for (int c = 0; c < cols; c++) {
                if (grid[r][c] == 2) {
                    queue.offer(new int[]{r, c});
                } else if (grid[r][c] == 1) {
                    freshCount++;
                }
            }
        }
        
        if (freshCount == 0) {return 0;}
        
        int[][] directions = {
            {-1, 0}, {1, 0}, {0, -1}, {0, 1}
        };
        int minutes = -1; 
                
        while (!queue.isEmpty()) {
            int size = queue.size();
            minutes++;
                        
            for (int i = 0; i < size; i++) {
                int[] current = queue.poll();
                int row = current[0];
                int col = current[1];
                                
                for (int[] dir : directions) {
                    int newRow = row + dir[0];
                    int newCol = col + dir[1];
                    
                    
                    if (newRow >= 0 && newRow < rows && 
                        newCol >= 0 && newCol < cols && 
                        grid[newRow][newCol] == 1) {
                        
                        
                        grid[newRow][newCol] = 2;
                        freshCount--;
                        queue.offer(new int[]{newRow, newCol});
                    }
                }
            }
        }
                
        return freshCount == 0 ? minutes : -1;
    }
}
```

BreadthFirstSearch_WordLadder
-----------------------------
Даны два слова (beginWord и endWord) и словарь wordList. Найти длину
кратчайшей последовательности
преобразований от beginWord до endWord, где:
За одно преобразование можно изменить только одну букву
Каждое промежуточное слово должно существовать в wordList
Вернуть 0, если преобразование невозможно
```Java
/*
1. понимаем: двигаемся по неявному(генерируемому) графу с помощью бфс
2. понимаем: правило движения: разница на одну букву
3. понимаем: генерируем возможные пути - берем лишь подходящий для движения дальше
4. создаем очередь бфс из слов 
   -. в очередь первое слово
5. создаем мапу для проверки корректности генерации 
   -. в мапу все слова
6. создаем мапу для проверки уже посещенных (visited) чтобы не ходить кругами
   -. в очередь первое слово
7. создаем стат переменную level для учета достижения результата
8. запускаем бфс цикл пока очередь чтото содержит
    -. проходимся уровнями - по очереди (это же бфс)
       старые значения выбывают новые прибывают
    -. генерируем набор слов по правилу (отличие в одну букву)
    -. те которые подпадают соответсвю по мапе слов -> новые соседи для очереди бфс
    -. не забываем учитывать их как посещенные, чтобы не ходиьт кругами
    -. увеличиваем уровень пока в очереди чтото есть 
    -. отмечаем: что можно добавить дохождение до endWord,
       в цикле бфс  но мы этого можем и неделать (слово же последнее)
*/    

class Solution {
    public int ladderLength(String beginWord, 
                            String endWord, List<String> wordList) {

        Set<String> wordSet = new HashSet<>(wordList);
                
        if (!wordSet.contains(endWord)) {
            return 0;
        }
        
        Queue<String> queue = new LinkedList<>();
        queue.offer(beginWord);
                
        int level = 1;
                
        Set<String> visited = new HashSet<>();
        visited.add(beginWord);
                
        while (!queue.isEmpty()) {
            int size = queue.size();
                        
            for (int i = 0; i < size; i++) {
                String currentWord = queue.poll();
                                
                if (currentWord.equals(endWord)) {
                    return level;
                }
                                
                char[] wordChars = currentWord.toCharArray();
                
                for (int j = 0; j < wordChars.length; j++) {
                    char originalChar = wordChars[j];
                                        
                    for (char c = 'a'; c <= 'z'; c++) {
                        if (c == originalChar) continue;
                        
                        wordChars[j] = c;
                        String transformedWord = new String(wordChars);
                                                
                        if (wordSet.contains(transformedWord) 
                            && !visited.contains(transformedWord)) {
                            queue.offer(transformedWord);
                            visited.add(transformedWord);
                        }
                    }                                        
                    wordChars[j] = originalChar;
                }
            }                        
            level++;
        }
                
        return 0;
    }
}
```

DepthFirstSearch_CloneGraph
---------------------------
Дана ссылка на узел в связном неориентированном графе. Вернуть
глубокую копию (клон) графа. Каждый узел графа содержит:
val (int) — значение узла
neighbors (List[Node]) — список соседних узлов
```Java
/*
1. понимаем: граф связаный и не ориентированый - потребуется стейт visited
2. понимаем: копирование графа = обходи графа со сборкой такого же
3. понимаем: граф связаный - возможно и бфс и дфс, без разницы
3. понимаем: основная проблема: клонировать или нет соседа (может быть в цикле)
4. создаем враппер в котором:
    -. создаем visited
    -. запускаем рекурсию дфс
5. в рекурсии:
    -. проверяем базовый случай на достижение null (здесь не используется)
    -. проверяем если массив уже содержит элемент возврааем его
       (он будет присоединятся)
    -. еслит не содержит:
        а. создаем новый элемент - используемый для клонирования
        б. отмечаем его в visited
        в. обходим по соседям в цикле
            -. получаем клон соседа
            -. добавляем в клонированный граф клонированного соседа 
               (нового или уже имеющегося)
    -. когда закончили с соседями, - возвращаем клона
*/

class Solution {
    public Node cloneGraph(Node node) {
        if (node == null) {
            return null;
        }
                
        Map<Node, Node> visited = new HashMap<>();
        return dfsClone(node, visited);
    }
    
    private Node dfsClone(Node original, Map<Node, Node> visited) {
        
        if (visited.containsKey(original)) {
            return visited.get(original);
        }
        
        Node clone = new Node(original.val);
        
        visited.put(original, clone);        
        
        for (Node neighbor : original.neighbors) {
            Node clonedNeighbor = dfsClone(neighbor, visited);
            clone.neighbors.add(clonedNeighbor);
        }
        
        return clone;
    }
}
```

DepthFirstSearch_CourseSchedule
-------------------------------
Есть numCourses курсов, пронумерованных от 0 до numCourses - 1.
Даны зависимости prerequisites[i] = [a, b], означающие, что для прохождения курса,
a нужно сначала пройти курс b (b → a).
Определить, возможно ли закончить все курсы (нет циклов в зависимостях).
```Java
/*
1. понимаем: нужно просто определить циклы
2. понимаем: самый простой способ перебрать все курсы и везде проверить циклы
3. понимаем: чтобы проверять циклы потребуется граф из оригинального массива
4. понимаем: цикличность это стат переменная массив visited
             типичные отметки: 0 не посещен 1 в процессе обхода 2 посещен
5. заводим метод враппер, в котором:
    -. строим нормальный граф 
    -. для каждого из элементов запускаем проверку на цикличность
       запускаясь только с 0 статуса (1 уже признаёт цикл и 2 не нужны)
6. заводим рекурсивный метод проверки цикличности
    -. зайдя со стороны ноды ставим её как посещенную
    -. проходимся по всем соседям рекурсией
        а. если сосед уже в процессе обхода когда вышли в рекурсии (есть цикл)
        б. если сосед не посещен, но кто то из его соседей посещен (есть цикл)
    -. устанавливаем 2 для того чтбы отметится как отработанные
    -. возвращаем в случае возврата 2 отсутствие цикла
*/

class Solution {
    public boolean canFinish(int numCourses, int[][] prerequisites) {
        
        List<List<Integer>> graph = new ArrayList<>();
        for (int i = 0; i < numCourses; i++) {
            graph.add(new ArrayList<>());
        }
        
        for (int[] prereq : prerequisites) {
            int course = prereq[0];
            int required = prereq[1];
            graph.get(required).add(course); 
        }
        
        int[] visited = new int[numCourses];        
        
        for (int i = 0; i < numCourses; i++) {
            if (visited[i] == 0) {
                if (hasCycleDFS(graph, visited, i)) {
                    return false; 
                }
            }
        }
        
        return true; 
    }
    
    private boolean hasCycleDFS(List<List<Integer>> graph, int[] visited, int node) {
        
        visited[node] = 1;
                
        for (int neighbor : graph.get(node)) {
            if (visited[neighbor] == 1) {
                
                return true;
            }
            if (visited[neighbor] == 0) {
                if (hasCycleDFS(graph, visited, neighbor)) {
                    return true;
                }
            }
        }
                
        visited[node] = 2;
        return false;
    }
}
```

DepthFirstSearch_PathSum
------------------------
Дано бинарное дерево и целевая сумма. Определить, существует
ли путь от корня к листу, где сумма значений узлов равна целевой сумме.
```Java
/*
1. понимаем: дфс обойдет все пути по дереву доходя до null в хвосте 
2. понимаем: есть паттерн возврата из ветки рекурсии true через 
             return recursive(left) || recursive(right)
3. понимаем: для проверки сумму можно или копить (не наш кейс) или сокращать,
             пока она не станет равна node.value
4. создаем рекурсивный метод с параметром targetSum для сокращаемой суммы
    -. проверяем базовый кейс с дохождением до null - возвращаем false
    -. в случае если дошли до конца дерева, возвращаем true если 
       сумма равна node.value
    -. уменьшаем значение суммы на текущее значение
    -. прокидываем true через паттерн return recursive(left) || recursive(right)
*/

class Solution {
    public boolean hasPathSum(TreeNode root, int targetSum) {
        if (root == null) {
            return false;
        }
                
        if (root.left == null && root.right == null) {
            return targetSum == root.val;
        }
                
        int newTarget = targetSum - root.val;
        return hasPathSum(root.left, newTarget) || 
               hasPathSum(root.right, newTarget);
    }
}
```

DynamicProgramming_ClimbingStairs
---------------------------------
Вы поднимаетесь по лестнице. Чтобы достичь вершины, нужно сделать n шагов.
Каждый раз вы можете подняться на 1 шаг или 2 шага.
Сколько существует различных способов подняться на вершину?
```Java
/*
1. понимаем: рекурренная формула 
             dp[i] = dp[i - 1] + dp[i - 2];
2. понимаем: заполнить dp можно снизу вверх пропустив 
             базовые случаи
2. вводим массив dp 
3. инициаиализируем базовые dp случаи 1 = 1, 2 = 2
4. запускаем цикл заполнения dp снизу вверх 
   пропускаем базовые случаи for(int i = 3..)
5. возвращаем вычисленый dp[n]
*/

class Solution {
    public int climbStairs(int n) {
        if (n <= 2) {
            return n;
        }
                
        int[] dp = new int[n + 1];
                
        dp[1] = 1; 
        dp[2] = 2; 
                
        for (int i = 3; i <= n; i++) {
            dp[i] = dp[i - 1] + dp[i - 2];
        }
        
        return dp[n];
    }
}
```

DynamicProgramming_CoinChange
-----------------------------
Даны монеты разных номиналов и сумма. Найти минимальное
количество монет, которое нужно для получения суммы.
Если сумму получить невозможно, вернуть -1.
```Java
/*
1. понимаем: рекуррентная формула передает:
   если ищем 11
   номиналы монет [1, 2, 5]
   если последняя монета - номиналом 2,
    то в итоге будет:
    - одна монета номиналом 2;
    - плюс минимальное количество монет для суммы 9 (потому что 11 − 2 = 9)
2. понимаем: рекуррентная формула относительно всех разменов:
    int вариантСЕдиницей = dp[s - 1] + 1; // «А что если последняя монета была 1?»
    int вариантСДвойкой  = dp[s - 2] + 1; // «А что если последняя монета была 2?»
    int вариантСПятеркой = dp[s - 5] + 1; // «А что если последняя монета была 5?»    
    -и-
    dp[s] = min(вариантСЕдиницей, вариантСДвойкой, вариантСПятеркой);
3. понимаем: нам потребуется цикл + вычислением минимума относительно каждого варианта:
    dp[i] = Math.min(
        dp[i],              <- уже найденый размен ранее другой комбинацией
        dp[i - coin] + 1    <- утверждение из (1)
        );
4. понимаем: dp потребуется заполнить или Integer.MAX_VALUE или максимумом 
             чтобы было что уменьшать
5. вводим dp в размер + 1 чтобы был вариант для нуля монет
6. заполняем dp максимом чтобы было что уменьшать
7. вводим базовый dp кейс: dp[0] = 0
8. запускаем цикл dp снизу вверх минуя базовый кейс
9. выбираем минимум из получившихся кобинаций из монет пропуская кейс 
   когда номинал монеты больше возможностей dp 
a. реализуем формулу
b. проверяем ни максимум ли в dp искомом - если максимум вернем -1 иначе результат
*/

class Solution {
    public int coinChange(int[] coins, int amount) {
        
        int[] dp = new int[amount + 1];
                
        Arrays.fill(dp, amount + 1);
        dp[0] = 0; 
        
        
        for (int i = 1; i <= amount; i++) {
            for (int coin : coins) {
                if (coin <= i) {
                    dp[i] = Math.min(dp[i], dp[i - coin] + 1);
                }
            }
        }
        
        return dp[amount] > amount ? -1 : dp[amount];
    }
}
```

DynamicProgramming_LongestIncreasingSubsequence
-----------------------------------------------
Дан массив целых чисел. Найти длину самой длинной строго возрастающей
подпоследовательности (не обязательно подряд идущих элементов).
```Java
/*
1. понимаем: что мы работаем с последовательностями [1, 2, 3, 0, 4] 
          даст последоватеьность: 1,2,3,4 (нуль пропустится)
2. понимаем: для формирования последовательности надо смотреть на 
             -. максимальное значение последовательности 
             -. для числа меньшего чем имеющееся
             то есть находясь в (4) искать число меньше слева (перебирая все)
             и брать от него последовательность + складывать с последовательностью
             длиной в единицу, задаваемую текущим числом
3. понимаем: задача развивает идею с монетами
             в том плане что придется перебирать в монетах монеты
             в этой задаче все числа ДО чтобы взять максимум
4. понимаем: разница с монетами 
                -. в добавлении кейса: число слева меньше 
                -. берем максимум не минимум
5. понимаем: рекуррентная формула         
            dp[i] = Math.max(
                dp[i],              <- текущий элемент
                dp[j] + 1           <- то что слева + минимальная посоедовательность текущего
                );
            для перебора всего того что слева(dp[j]) от текущего элемента (dp[i])
6. вводим dp в длину
7. заполняем его минимальными последовательностями от каждого
8. вводим стат переменную хранящую максимум длины
9. запускаем цикл по dp пропуская базовый кейс в 1  
    -. в каждом цикле смотрим все варианты слева (j<i)
    -. в случае если значение nums[i] меньше чем nums[j]
       заполняем dp через установку максимума
a. обновляем стат переменную с максимальной длиной от максимума
   между текущей максимальной длиной и получившееся dp[i]
*/

class Solution {
    public int lengthOfLIS(int[] nums) {
        if (nums.length == 0) return 0;        
        
        int[] dp = new int[nums.length];
        Arrays.fill(dp, 1); 
        
        int maxLength = 1;
        
        for (int i = 1; i < nums.length; i++) {
            for (int j = 0; j < i; j++) {
                if (nums[i] > nums[j]) {
                    dp[i] = Math.max(dp[i], dp[j] + 1);
                }
            }
            maxLength = Math.max(maxLength, dp[i]);
        }
        
        return maxLength;
    }
}
```

DynamicProgramming_LongestCommonSequence
----------------------------------------
Даны две строки `text1` и `text2`. Вернуть длину их самой длинной
общей подпоследовательности. Подпоследовательность — это последовательность,
которая появляется в том же порядке, но не обязательно подряд.
```Java
/*
1. понимаем: перешли из 1Д варианта в 2Д и будем заполнять матрицу
2. понимаем: заполнять уже придется полным перебором получая эскалацию:             
                    ""  a  c  e
                ""  0   0  0  0
                a   0  (1) 1  1  <- совпала (a)
                b   0   1  1  1
                c   0   1 (2) 2  <- совпала (c)
                d   0   1  2  2
                e   0   1  2 (3) <- совпала (e)
3. понимаем: заполняя каждую из ячеек мы или эксалируем значение 
             исходя из того что больше (значение слева, значение сверху)
             или при совпадении чисел увеличиваем его на +1
4. понимаем: полный перебор требуется для того чтобы обеспечить эскалацию
             и слева и сверху для каждого dp
5. заводим dp как матрицу
6. запускаем цикл по первой подстроке и сразу внутри него цикл по второй подстроке
    -. если совпали берем прошлое по диагонали вверх-влево и прибавляем единицу
    -. если значения не совпади эскалируем или слева или сверху (берем максимум)
       почему максимум:
            слева: последовательность пришла с первой подстроки
            сверху: последовательность пришла со второй подстроки
*/

class Solution {
    public int longestCommonSubsequence(String text1, String text2) {
        int m = text1.length();
        int n = text2.length();
                
        int[][] dp = new int[m + 1][n + 1];
        
        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                if (text1.charAt(i - 1) == text2.charAt(j - 1)) {
                    
                    dp[i][j] = dp[i - 1][j - 1] + 1;
                } else {                    
                    dp[i][j] = Math.max(dp[i - 1][j], dp[i][j - 1]);
                }
            }
        }
        
        return dp[m][n];
    }
}
```

DynamicProgramming_PartitionEqualSubsetSum
------------------------------------------
Дан непустой массив целых положительных чисел. Определить, можно ли
разбить массив на два подмножества с равными суммами.
```Java
/*
1. понимаем: ищем из [1, 5, 11, 5] вот это [1, 5, 5] и [11]
2. понимаем: измененная задача про монетки, но 
             -. правило -> равная сумма
             -. после обработки число выбывает (монет бесконечно много)
3. понимаем: ищем набор target равный: сосчитать сумму массива / пополам
4. понимаем: рекуррентная формула (очень похоже на монетки)
    dp[s] =                                               <- если «s» = 100
            dp[s] ||                                      <- для номинала 100
            dp[s - nums[i]] для s от target до nums[i]    <- для номинала 50            
5. понимаем: разница обусловлена тем что нам надо хранить не суммы 
             как в монетках - а просто true/false 
             для этого достаточно OR операции            
6. понимаем: разница формул:
    dp[j] = dp[j] + dp[j - coin]          <- хранит количество монеток
    dp[j] = dp[j] | dp[j - num]           <- хранит просто факт true/false
7. понимаем: что рекуррентную формулу слева направо не считать 
             её можно заменить матрицей, формула изменится на:
                if (currentNum > j) {                    
                    dp[i][j] = dp[i - 1][j];
                } else {        
                    dp[i][j] = dp[i - 1][j] || dp[i - 1][j - currentNum];
                }             
8. считаем target, это сумма массива пополам
9. если не делится на два - половины не возможны
a. создаем dp как матрицу, чтобы сократить вычисления
    n - число элементов 
    на (target + 1) - чтобы набрать значение
b. заполняем первую колонку матрицу через true
   она потребуется для вычислений остальных полей
c. проходимся по матрице заполняя её аналогично как это делалось 
   с эскалацией данных в подпоследовательностях, но с уже измененной 
   формулой
d. возвращаем вычисленное с помощью dp значение
*/

class Solution {
    public boolean canPartition(int[] nums) {
        int totalSum = 0;
        for (int num : nums) {
            totalSum += num;
        }        
        
        if (totalSum % 2 != 0) {
            return false;
        }
        
        int target = totalSum / 2;
        int n = nums.length;
                
        boolean[][] dp = new boolean[n + 1][target + 1];        
        
        for (int i = 0; i <= n; i++) {
            dp[i][0] = true;
        }
                
        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= target; j++) {
                int currentNum = nums[i - 1];
                
                if (currentNum > j) {                    
                    dp[i][j] = dp[i - 1][j];
                } else {        
                    dp[i][j] = dp[i - 1][j] || dp[i - 1][j - currentNum];
                }
            }
        }
        
        return dp[n][target];
    }
}
```

FastAndSlowPointers_FindTheDuplicateNumber
------------------------------------------
Дан массив nums длины n + 1, содержащий целые числа от 1 до n (включительно).
Гарантируется, что существует ровно одно повторяющееся число.
Найти это число, используя:
O(1) дополнительной памяти
Не изменяя исходный массив
O(n) времени
```Java
/*
1. понимаем: концептуально тяжелая задача представляющая две части алгоритма флойда
2. понимаем: значения можно трактовать как индексы повтор индекса = цикл = дубль
3. понимаем: решение же концептуально просто - придется запоминать визуально
4. задаем два указателя как значения
5. первая часть алгоритма флойда - ожидаем встречи
6. вторая часть алгоритма флойда - ищем значение (ищем начало цикла)
*/

class Solution {
    public int findDuplicate(int[] nums) {
        
        int slow = nums[0];
        int fast = nums[0];
                
        do {
            slow = nums[slow];        
            fast = nums[nums[fast]];  
        } while (slow != fast);        
        
        slow = nums[0];
        while (slow != fast) {
            slow = nums[slow];
            fast = nums[fast];
        }
        
        return slow; 
    }
}
```

FastAndSlowPointers_HappyNumber
-------------------------------
Число называется "счастливым", если в процессе:
Начинаем с любого положительного целого числа
Заменяем число суммой квадратов его цифр
Повторяем процесс, пока число не станет 1 (счастливое)
или не зациклится (несчастливое)
Вернуть true, если число счастливое, иначе false.
```Java
/*
1. понимаем: генерируемый граф (следующее число зависит от прошлого)
    для 19  = 1² + 9² = 1 + 81 = 82
    для 82  = 8² + 2² = 64 + 4 = 68
    для 68  = 6² + 8² = 36 + 64 = 100
    для 100 = 1² + 0² + 0² = 1 -> счастливое
2. понимаем: число не счастливое - зацикленность генерации
3. понимаем: следующее чило это метод генерации
             всё остальное это просто алгоритм флойда первая часть
4. вводим метод генерации числа - по правилу задачи
5. вводим slow / fast указатели
6. двигаем их по праилу флойда пока 
    -. число не равно 1 (победа)
    -или-
    -. значения сравнялись (произошел цикл)
7. возвращаем равность 1 по выходу из цикла (победа или нет)
*/

class Solution {
    public boolean isHappy(int n) {
        int slow = n;
        int fast = getNext(n);
                
        while (fast != 1 && slow != fast) {
            slow = getNext(slow);        
            fast = getNext(getNext(fast)); 
        }        
        
        return fast == 1;
    }
        
    private int getNext(int n) {
        int sum = 0;
        while (n > 0) {
            int digit = n % 10;
            sum += digit * digit;
            n /= 10;
        }
        return sum;
    }
}
```

FastAndSlowPointers_LinkedListCycle
-----------------------------------
Определить, содержит ли связанный список цикл
```Java
/*
1. понимаем: для отслеживания цикла запускаем два указателя
             один со скоростью 2 другой со скоростью 1
             если они встретятся - мы имеем цикл
2. понимаем: что это работает по принципу приблежения
             быстрого к медленному на разницу скорости 1
3. оба указателя ставим на головную ноду
4. пока какой то указатель не указал на null
   -. двигаем медленный на одну единицу
   -. двигаем быстрый на две единицы
   -. если встретились - есть цикл
*/

class Solution {
    public boolean hasCycle(ListNode head) {
        
        if (head == null || head.next == null) {
            return false;
        }
        
        ListNode slow = head;
        ListNode fast = head;        
        
        while (fast != null && fast.next != null) {
            slow = slow.next;          
            fast = fast.next.next;     
                        
            if (slow == fast) {return true;}
        }
                
        return false;
    }
}
```

LinkedListReversal_ReverseLinkedList
------------------------------------
Развернуть связанный список
```Java
/*
1. понимаем: разворот листа это перемена у нод соединений next/prev
2. понимаем: для того чтобы сработало и мы не потеряли 
             возможность двигаться по листу слева направо: потербуется временная нода
3. представляем формацию: 
    nodeA = prev.nodeB.next = nodeC
    находимся в nodeB(curerent)
    -. создаем temp из current.next чтобы было куда двигаться
    -. меняем местами next/prev
    -. двигаемся дальше в сторону сохраненной temp 
       current = temp 
*/

class Solution {
    public ListNode reverseList(ListNode head) {
        ListNode prev = null;
        ListNode current = head;
                
        while (current != null) {
            ListNode nextTemp = current.next;  
            current.next = prev;               
            prev = current;                    
            current = nextTemp;                
        }
        
        
        return prev;
    }
}
```

LinkedListReversal_SwapNodesInPairs
-----------------------------------
Поменять местами каждые два соседних узла в связанном списке.
```Java
/*
1. понимаем: двигаемся по два узла зараз
2. понимаем: prev <- first<->second -> next
3. понимаем: статистически смещаем каждый раз в цикле current 
4. понимаем: для того чтобы оперировать с prev/current вводим dummy
5. понимаем: используем схему:
                    nodeA{   <- prev
                    }
                    nodeB {  <- current
                    next = ..
                    }
                    nodeC {
                    next = ..
                    }
                    nodeD { <- next
                    next = ...
                    }
6. вводим dummy ставим в prev
7. заводим цикл движения по парам: current / current.next 
8. вводим пару first / second, что и будут меняться ссылками
9. меняем их ссылками
а. обеспечиваем движение сдвигом prev/current
*/

class Solution {
    public ListNode swapPairs(ListNode head) {
        
        ListNode dummy = new ListNode(0);
        dummy.next = head;
        
        ListNode prev = dummy;
        ListNode current = head;
        
        while (current != null && current.next != null) {
            
            ListNode first = current;
            ListNode second = current.next;
                        
            prev.next = second;      
            first.next = second.next; 
            second.next = first;     
                        
            prev = first;
            current = first.next;
        }
        
        return dummy.next;
    }
}
```

MatrixTraversal_FloodFill
-------------------------
Заполнение области (Flood Fill) на изображении
```Java
/*
1. понимаем: работаем с сеткой являющейся стейтом
2. понимаем: можем залить обходом дфс или бфс
3. понимаем: учет того что обошли = установка в стейте значения заливки
4. вводим враппер 
    -. получаем цвет заливки 
    -. запускаем дфс по обходу заливки
5. в дфс рекурсивной
    -. передаем в метод:
        а. картинку-стей
        б. текущие колонку и строку
        в. оригинальный цвет
        г. новый цвет
    -. проверяем, что не вышли за границы
    -. проверяем, что цвет не равен оригинальному (можно заливать)
       если не соблюдено выходим как базовый случай
    -. заливаем: устанавливаем в новый цвет 
    -. запускаем дфс во все стороны по сетке
*/

class Solution {
    public int[][] floodFill(int[][] image, int sr, int sc, int newColor) {
        int originalColor = image[sr][sc];
                
        if (originalColor == newColor) {
            return image;
        }
                
        dfs(image, sr, sc, originalColor, newColor);
        return image;
    }
    
    private void dfs(int[][] image, int row, int col, 
                     int originalColor, int newColor) {
        
        if (row < 0 || row >= image.length || col < 0 || col >= image[0].length 
            || image[row][col] != originalColor) {
            return;
        }        
        
        image[row][col] = newColor;        
        
        dfs(image, row - 1, col, originalColor, newColor); 
        dfs(image, row + 1, col, originalColor, newColor); 
        dfs(image, row, col - 1, originalColor, newColor); 
        dfs(image, row, col + 1, originalColor, newColor); 
    }
}
```

MatrixTraversal_NumberOfIslands
-------------------------------
Подсчитать количество островов в сетке (остров - группа смежных '1'
по горизонтали и вертикали).
```Java
/*
1. понимаем: работаем с стейтом который можно модифицировать
2. понимаем: учет острова - это счет его + удаление со стейта
3. вводим стейт подсчета островов 
4. проходимся по сетке - в случае нахождения '1' 
    -. считаем остров 
    -. удаляем его с сетки через рекурсивную функцию
5. в рекурсивной функции
    -. проверяем что не вышли за границы
    -. проверяем что еще рабоатем с 1
    -. ставим в нуль
    -. запускаем рекурсию по всем направлениям
*/

class Solution {
    public int numIslands(char[][] grid) {
        if (grid == null || grid.length == 0) {
            return 0;
        }
        
        int numIslands = 0;
        int rows = grid.length;
        int cols = grid[0].length;
        
        
        for (int r = 0; r < rows; r++) {
            for (int c = 0; c < cols; c++) {
                
                if (grid[r][c] == '1') {
                    numIslands++;
                    
                    dfs(grid, r, c);
                }
            }
        }
        
        return numIslands;
    }
    
    private void dfs(char[][] grid, int row, int col) {
        int rows = grid.length;
        int cols = grid[0].length;
        
        
        if (row < 0 || row >= rows || col < 0 || col >= cols || 
            grid[row][col] != '1') {
            return;
        }
                
        grid[row][col] = '0';
                
        dfs(grid, row - 1, col); 
        dfs(grid, row + 1, col); 
        dfs(grid, row, col - 1); 
        dfs(grid, row, col + 1); 
    }
}
```

MatrixTraversal_SurroundedRegions
---------------------------------
Захватить окруженные регионы (поменять 'O' на 'X' только для
полностью окруженных областей).
```Java
/*
1. понимаем: задача аналогичная на заливку или число островов
2. пониамем: задача отличается лишь тем что решается в два этапа
3. понимаем: если грид это стейт мы можем использовать временные метки
4. понимаем: "захватятся" лишь регионы не имеющие доступа к краю
5. понимаем: логика решения: обойти край закрасить временным символом 
             всё что имеет доступ к краю через дфс/бфс
             после чего все что не временный цвет закрасить "захватом"
6. проходим циклом по нулевой строке и последней строке запуская закраску 'M'
7. проходим циклом по нулевому столбцу и последнем делая тоже
8. проходим циклом по всему гриду и всё что не 'M' будет захваченым
9. вводим дфс для закраски 'M' работающим аналогично заливке
*/

class Solution {
    public void solve(char[][] board) {
        if (board == null || board.length == 0) {
            return;
        }
        
        int rows = board.length;
        int cols = board[0].length;        
                
        for (int c = 0; c < cols; c++) {
            if (board[0][c] == 'O') {
                dfs(board, 0, c);
            }
            if (board[rows - 1][c] == 'O') {
                dfs(board, rows - 1, c);
            }
        }
                
        for (int r = 0; r < rows; r++) {
            if (board[r][0] == 'O') {
                dfs(board, r, 0);
            }
            if (board[r][cols - 1] == 'O') {
                dfs(board, r, cols - 1);
            }
        }
                
        for (int r = 0; r < rows; r++) {
            for (int c = 0; c < cols; c++) {
                if (board[r][c] == 'O') {
                    board[r][c] = 'X';  
                } else if (board[r][c] == 'M') {
                    board[r][c] = 'O';  
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
                
        board[row][col] = 'M';        
        
        dfs(board, row - 1, col); 
        dfs(board, row + 1, col); 
        dfs(board, row, col - 1); 
        dfs(board, row, col + 1); 
    }
}
```

ModifiedBinarySearch_FindMinimumInRotatedSortedArray
----------------------------------------------------
Найти минимальный элемент в отсортированном и повернутом массиве.
```Java
/*
1. понимаем: {5,6,7,8} {1,2,3,4} <- точка поворота и есть минимальный элемент
2. понимаем: элемент идентифицируем: вокруг него слева - большее число, справа - большее
3. понимаем: точка поиска будет или в неотсортированой части, или по центру 
                        |    | -> сортированная часть
            {7,8,9},{1, |  2,| 3,4,5,6}
                        |    |
                    <- очевидное расположение (1) в не отсортированной части
4. понимаем: при движении по массиву мы:
       - или нашли точку поворота по условию выше;
       - или должны сместиться в неотсортированную часть.
5. понимаем: определяем, какая сторона отсортирована, через:
       if(nums[mid] > nums[right]) {..} <- левая отсортирована, минимум в правой.
6. вводим left/right указатели с концов
7. в цикле пока не встретились указатели
    -. делим пополам
    -. элемент нашелся: если nums[mid] < nums[mid - 1]
    -. для проверки двух условия зараз, элемент нашелся если:
       nums[mid] > nums[mid + 1]
    -. двигаем еказатели всегда в не отсортированную сторону 
*/

class Solution {
    public int findMin(int[] nums) {
        int left = 0;
        int right = nums.length - 1;
                
        if (nums[left] <= nums[right]) {
            return nums[left];
        }
                
        while (left <= right) {
            int mid = left + (right - left) / 2;            
            
            if (mid > 0 && nums[mid] < nums[mid - 1]) {
                return nums[mid];
            }
            if (mid < nums.length - 1 && nums[mid] > nums[mid + 1]) {
                return nums[mid + 1];
            }
                        
            if (nums[mid] > nums[left]) {left = mid + 1;} 
            else {right = mid - 1;}
        }
        
        return nums[0];
    }
}
```

ModifiedBinarySearch_Search2DMatrix
-----------------------------------
Найти целевое значение в отсортированной матрице (каждая
строка отсортирована по возрастанию, первый элемент каждой строки
больше последнего элемента предыдущей строки).
```Java
/*
1. понимаем: отличие от обычного 2Д BS то что движемся или по колонке или по строке вниз
2. понимаем: что нужно начинать из правого угла, для того чтобы пространство сокращалось    
            matrix = [
                [ 1 ,   4,  7, 11, (15)], <- 15 начало движения
                =======================
                [2,     5,  8, 12, |19],
                [3,     6,  9, 16, |22],
                [10,   13, 14, 17, |24],
                [18,   21, 23, 26, |30]
            ]
3. понимаем: порядок движения: 
    -. отсекаем строки двигаясь вперед
    -. отсекаем столбцы двигаясь назад
    -. движение выглядит следующим образом:
          row = 2, col = 3, value = 16
          [ [  _,   _,   _,   _,   _ ],
            [  _,   _,   _,   _,   _ ],
            [ 3,   6,   9, (16),  _ ],
            [10,  13,  14,  17,   _ ],
            [18,  21,  23,  26,   _ ] ]
4. понимаем: очень специфический формат данных дал возможность сделать поиск            
5. ставим rows/cols на длины
6. в цикле двигаем или row или col в зависимости от кейса
*/

public class Solution {
    public boolean searchMatrix(int[][] matrix, int target) {
        if (matrix == null || matrix.length == 0 || matrix[0].length == 0) {
        return false;
        }
        int row = 0;
        int col = matrix[0].length - 1;

        while (row < matrix.length && col >= 0) {
            if (matrix[row][col] == target) {
                return true;
            } else if (matrix[row][col] > target) {
                col--;
            } else {
                row++;
            }
        }
        return false;
    }
}
```

ModifiedBinarySearch_SearchInRotatedSortedArray
-----------------------------------------------
Найти целевое значение в отсортированном и повернутом массиве
```Java
/*
1. понимаем: нормальный BS использует принцип сортированного массива {1,2,3} -- {5,6,7}
2. понимаем: с повернутым массивов логика сортированного массива не возможна
3. понимаем: возможна инверсионная логика:
    -. Если одна сторона сортированная и элемента там нет,
    -. значит, единственное место, где он может быть, - это другая сторона.
4. понимаем: использование инверсионной логики позволяет задачу решить 
             в два шага:
             -. делить и использовать инверсионную логику для отсечения сортированной части
             -. в сортированной части использовать обычный BS
5. понимаем: принцип не отсортированной части - (2) меньше (7) 
             второй принцип (не нужный) и (2) больше (1)
                  |    | -> сортированная часть
      {7,8,9},{1, |  2,| 3,4,5,6}
                  |    |             
6. понимаем: логическая таблица решений:
     Условие                 | Target в отсортированной части? | Действие
     ------------------------|---------------------------------|-----------------
     Левая половина сортир.  | Да (left <= target < mid)       | right = mid - 1
     Левая половина сортир.  | Нет                             | left = mid + 1
     Правая половина сортир. | Да (mid < target <= right)      | left = mid + 1
     Правая половина сортир. | Нет                             | right = mid - 1
7. задаем левый и правые указатели
8. пока указатели не встретились запускаем цикл:
    -. ищем мид как и в обычном BS
    -. проверяем на то, что нашли значение
    -. условие для несортированной части 
       и условие для сортированной части согласно таблице
       запутывает - можно выписать и явно, или запомнить код:
            if-if
            if-else
            else-if
            else-else
*/      

class Solution {
public class Solution {
    public int search(int[] nums, int target) {
      int left = 0;
      int right = nums.length - 1;

      while (left <= right) {

        int mid = left + (right - left) / 2;

        if (nums[mid] == target) {
          return mid;
        }

        if (nums[left] <= nums[mid]) {
          if (target >= nums[left] && target < nums[mid]) {right = mid - 1;}
          else {left = mid + 1;}
        } else {
          if (target > nums[mid] && target <= nums[right]) {left = mid + 1;}
          else {right = mid - 1;}
        }
      }
      return -1;
    }
  }
}
```

MonotonicStack_LargestRectangleInHistogram
------------------------------------------
Найти площадь самого большого прямоугольника в гистограмме
```Java
/*
1. понимаем: задача не простая и требует графического понимания
2. понимаем: по возрастанию горки кандидаты растут 
             по убыванию кандидаты выбывают
             стек копит кандидатов - и с их уровнем (когда выбывают) считает площади

            x
           xxх
          xxxхх
          xxxххх
          123456
          ------
          123      <- стек и кандидаты в нём
           12
            1

            x
           xxх
          xxxхх
          xxxххх
          123456
          ------
          1232     <- (3) выбыл реализовав свою площадь - и мы получили вычисление
           121
            1

3. понимаем: захватываемые "за компанию" получается благодаря монотонному стеку 
4. понимаем: логика:
                    for (слева направо)
                            <- обработка логического «на следующем цикле»
                            while (кандидаты ниже, чем дошли до ячейки)
                                реализация кандидатом площади
                            <- обработка логического «на этом цикле»
                            push (в список кандидатов текущего)
5. вводим maxArea для статистики (она у нас упрощенная)
6. вводим стек просто высот
7. двигаемся по циклу
8. получаем текущую высоту
9. обрабатываем "на следующем цикле" для прошлой сохраненной в стеке высоты
    (этот момент кстати и делает алгоритм плохо читаемым - так как это забегающая
    вперед логика - но это именно "на следующем цикле" логически)
a. решаем вопрос с выбытием кандидатов и реализацией ими своей площади
b. сохраняем текущую высоту в стек в обязательном порядке - так как выбывать
   она сможет лишь в "на следующем цикле"
c. и как всех обойдем - возвращаем статистику
*/

class Solution {
    public int largestRectangleArea(int[] heights) {
      int n = heights.length;
      int maxArea = 0;
      Stack<Integer> stack = new Stack<>();

      for (int i = 0; i <= n; i++) {

        int currentHeight = (i == n) ? 0 : heights[i];

        while (!stack.isEmpty() && currentHeight < heights[stack.peek()]) {

          int height = heights[stack.pop()];
          int width = stack.isEmpty() ? i : i - stack.peek() - 1;
          maxArea = Math.max(maxArea, height * width);

        }
        stack.push(i);
      }

      return maxArea;
    }
}
```

MonotonicStack_NextGreaterElement
---------------------------------
Найти следующий больший элемент для каждого элемента в массиве
```Java
/*
1. понимаем: логика задачи на двух массивах, но можно показать на одном:
          {1,2,3,2,1, 5}
          {2,3,5,5,5,-1} <- для (1) следующее NGE это (2), для (2) это (3), для (3) это (5)
                            а для (2) и (1) это тоже пять так как (1) меньше чем (2)
2. понимаем: вычисления слева направо - это постоянные забегания
             вперед и они не эффективны, нужно решать справа налево
3. понимаем: логика обработки сохранненых в стеке высот:

                   x  (4)
           x     x x  (3)
           x   x x x  (2) <- следующая высота для клетки где мы находимся, актуальная высота(!)
           x x x x x  (1)
            /|\       <- вид стека к этому моменту

    Когда мы дойдём вот сюда:

                   x  (4) <- все что можно оставить в стеке - это (4)
           x     x x  (_) <- не актуально яма уже пройдена
           x   x x x  (_) <- не актуально яма уже пройдена
           x x x x x  (_) <- не актуально яма уже пройдеан
          /|\

4. понимаем: конечное решение:
    -. Движемся справа налево, накапливая стек, в котором будет актуальная
       для нас сохранённая высота. Она будет избавлять нас от забегания
       слева направо каждый раз вперёд (яма).
    -. Для сохранения высот нам потребуется структура, из которой элементы
       будут входить с одной стороны и удаляться из той же стороны -
       это и есть стек.
    -. Понимая, что яма может быть локальной, когда мы видим значение
       меньше того, что было сохранено, мы это меньшее значение также сохраняем.
       Оно может быть NGE для локальной ямы (речь идёт о сохранении (8)
       при том, что в стеке находится (10)).
    -. Когда мы видим число, двигаясь справа налево, которое больше того,
       что мы сохранили в стеке, мы вычищаем из стека все лишние числа.
       К моменту, как мы до него дошли, эти числа уже неактуальны.
       Мы находимся на границе глобальной ямы, и локальная яма,
       как и все числа внутри неё, уже были обработаны ранее - просто по определению.
5. понимаем: приведенное решение для одного массива, но массивов два 
6. кодируем:
    -. Заводим сначала стек, который будет передавать высоты.
    -. Потом задаём для хранения результатов хеш-мап - так как у нас элементы
       уникальные. (Если бы они были не уникальные, мы могли бы хранить их как-то
       иначе - или в массиве, или в линкд-листе.) То есть хеш-мап стал возможным
       только в случае, когда все элементы уникальные. Если бы были повторы, мы бы
       задачу с ним не решили: {1,2,1,3} <- дало бы провал, так как для (1), (2)
       и (3).
    -. Двигаемся справа налево и реализуем нашу логику: выкидываем элементы в случае,
       если они меньше или равны элементу, до которого мы дошли.
*/

class Solution {
    public int[] nextGreaterElement(int[] nums1, int[] nums2) {
        
        Map<Integer, Integer> indexMap = new HashMap<>();
        for (int i = 0; i < nums2.length; i++) {
            indexMap.put(nums2[i], i);
        }
            
        int[] result = new int[nums1.length];
        
        
        int[] nge = new int[nums2.length];
        Arrays.fill(nge, -1);
        Stack<Integer> stack = new Stack<>();
                
        for (int i = nums2.length - 1; i >= 0; i--) {
            int current = nums2[i];
                        
            while (!stack.isEmpty() && stack.peek() <= current) {
                stack.pop();
            }            
            
            if (!stack.isEmpty()) {
                nge[i] = stack.peek();
            }
                        
            stack.push(current);
        }
        
        
        for (int i = 0; i < nums1.length; i++) {
            int index = indexMap.get(nums1[i]);
            result[i] = nge[index];
        }
        
        return result;
    }
}
```

OverlappingIntervals_InsertInterval
-----------------------------------
Вставить новый интервал в отсортированный список непересекающихся
интервалов и объединить пересекающиеся
```Java
/*
1. понимаем: интервалы хранятся в формате {начало,конец}
2. понимаем: будет три цикла с общим итератором:
             (а) пропуск слева от нового интервала: 
                 (конец в имеющихся < начала нового)
             (б) объединение: пока: начало имеющегося меньше конца нового
             (в) пропуск справа: после операции (б) всё что осталось
3. создаем массив результатов
4. создаем общий итератор по трем циклам
5. исполняем пропуск левой части 
6. ищем точки объединяемого сегмента
    -. начало: минимум от точек начал объединяемых и нового
    -. конец: максимум от точек конца объединяемых и нового
7. добавляем его в результат
8. прокручиваем оставшуюся правую часть
*/

class Solution {
    public int[][] insert(int[][] intervals, int[] newInterval) {
        List<int[]> result = new ArrayList<>();
        
        int i = 0;
        int n = intervals.length;
        
        
        while (i < n && intervals[i][1] < newInterval[0]) {
            result.add(intervals[i]);
            i++;
        }        
        
        while (i < n && intervals[i][0] <= newInterval[1]) {
            newInterval[0] = Math.min(newInterval[0], intervals[i][0]);
            newInterval[1] = Math.max(newInterval[1], intervals[i][1]);
            i++;
        }
                
        result.add(newInterval);
                
        while (i < n) {
            result.add(intervals[i]);
            i++;
        }
                
        return result.toArray(new int[result.size()][2]);
    }
}
```

OverlappingIntervals_MergeIntervals
-----------------------------------
Объединить все перекрывающиеся интервалы в отсортированном массиве интервалов.
```Java
/*
1. понимаем: будем просто двигаться по сортированному массиву (цикл)
2. понимаем: если кейс: старый не закончился новый уже начался 
             будем выбирать точку окончания как максимум от начатого и нового
             появившегося 
3. понимаем: только в кейсе когда старый закончился когда новый начался
             будем добавлять полученное в результат
4. отсортируем массив интервалов (сможем сделать задуманное)
5. введем лист для хранения возврата
6. ложим в результат первый элемент : будем его уточнять
7. запускаем цикл объединения интервалов
    -. ловим interval это текущий обрабатываемый интервал
    -. оперируем двумя интервалами:
        а. interval <- следующий поступивший
        б. currentInterval <- текущий открытый
    -. если текущего конец больше ново поступившего начала
       обновляем текущего конец максимумом от текущего и поступившего
    -. если текущего конец меньше нового поступившего начала
       время сохранять интервал в результате
8. возвращаем результат как массив, не как лист
*/

class Solution {
    public int[][] merge(int[][] intervals) {
        if (intervals.length <= 1) {
            return intervals;
        }
                
        Arrays.sort(intervals, (a, b) -> Integer.compare(a[0], b[0]));
        
        List<int[]> result = new ArrayList<>();
        int[] currentInterval = intervals[0];
        result.add(currentInterval);
                
        for (int[] interval : intervals) {
            int currentEnd = currentInterval[1];
            int nextStart = interval[0];
            int nextEnd = interval[1];
            
            if (currentEnd >= nextStart) {
                
                currentInterval[1] = Math.max(currentEnd, nextEnd);
            } else {
                
                currentInterval = interval;
                result.add(currentInterval);
            }
        }
                
        return result.toArray(new int[result.size()][2]);
    }
}
```

PrefixSum_ContiguousArray
-------------------------
Найти максимальную длину непрерывного подмассива с равным количеством 0 и 1.
```Java
/*
1. понимаем: кажется, что нам надо просто надо копить count
   при появлении 0 увеличивать на 1
   при появлении 1 уменьшать на   1
   и отслеживать нулевое значение (количество нулей равно количеству едениц)
   это не так
   так как текущая последовательность может быть частью другой последовательности
   и отслеживать нужно не нуль, а повторение сальдо нулей и единиц
2. будем накапливать префиксную сумму по факту движения через +1/-1.
3. будем сохранять каждую префиксную сумму в Map<prefixSum,index>.
4. в случае если при попытке сохранения выясняется, что префиксная сумма
   уже была сохранена, мы берём разницу в индексах как претендента длины.
   сохранение при этом производим, так как сегмент с равными нулями
   и единицами окончился.
5. в случае если претендент длины больше текущего максимального претендента,
   заменяем его на текущего.
6. так как через введение Map технически массив префиксных сумм будет сдвинут влево
   (для нулевого индекса также нужна какая-то префиксная сумма - а это сумма (0)):

            nums =                [0, 1,  0,  0,  1, 1,  0]
            Префиксные суммы: [0, -1, 0, -1, -2, -1, 0, -1]
            Индексы:          [-1, 0, 1,  2,  3,  4, 5,  6]

            учтем это:
            prefixMap.put(
                0,            <- префиксная сумма учитываемая в рассчетах для (0) индекса
                -1            <- для индекса (-1) - индекса ПЕРЕД нулевым
                                (последовательно может начаться с нуля)
            )
    это типичный паттерн введения "самого левого" чтобы алгоритм был однообразен
*/

class Solution {
    public int findMaxLength(int[] nums) {
        
        Map<Integer, Integer> prefixSumMap = new HashMap<>();
        prefixSumMap.put(0, -1); 
        
        int maxLength = 0;
        int count = 0; 
        
        for (int i = 0; i < nums.length; i++) {
            
            if (nums[i] == 1) {count += 1;} 
            else {count -= 1;}
                        
            if (prefixSumMap.containsKey(count)) {
                
                int prevIndex = prefixSumMap.get(count);
                maxLength = Math.max(maxLength, i - prevIndex);

            } else {prefixSumMap.put(count, i);}
        }
        
        return maxLength;
    }
}
```

PrefixSum_RangeSumQueryImmutable
--------------------------------
Реализовать класс для быстрого вычисления суммы элементов в заданном диапазоне
индексов неизменяемого массива.
```Java
/*
1. понимаем: решение строиться в два этапа
    -. вычислить массив префиксных сумм
                [ 2     5     3     1     7 ] <- массив
          [ 0     2     7    10    11    18 ] <- префиксные суммы
    -. вычислить сумма элементов в диапазоне
       через разницу между префиксными суммами
2. понимаем: префиксную сумму придется вести с -1 значения,
             чтобы вычисления были однообразными
3. зададим дополнительный массив, где будет размещаться префиксная сумма,
   увеличенная на 1 по длине относительно оригинала.
4. сначала спрограммируем предвычисление префиксной суммы и зададим на него тест.
5. после чего зададим забор префиксной суммы.             
*/

class NumArray {
    private int[] prefixSum;
    
    public NumArray(int[] nums) {
        
        prefixSum = new int[nums.length + 1];
                        
        for (int i = 0; i < nums.length; i++) {
            prefixSum[i + 1] = prefixSum[i] + nums[i];
        }
    }
    
    public int sumRange(int left, int right) {
        
        return prefixSum[right + 1] - prefixSum[left];
    }
}
```

PrefixSum_SubarraySumEqualsK
----------------------------
Найти общее количество непрерывных подмассивов, сумма которых равна k.
```Java
/*
1. понимаем: для суммы 3:

    {1,1,1},2,3,4,5,1,2,3,4
    1,1,{1,2},3,4,5,1,2,3,4     <- как видим может возникать перекрытие
    1,1,1,2,{3},4,5,1,2,3,4     <- здесь интересный момент называть ли подмассивом 1 элемент
    1,1,1,2,3,4,5,{1,2},3,4
    1,1,1,2,3,4,5,1,2,{3},4

2. понимаем: используем формулу:

    Если существует префиксная сумма (prefixSum – k),
    значит, между соответствующим индексом и текущим
    есть подмассив с суммой k.

3. понимаем: префиксная сумма обезличенная передача процесса:

    nums = [100000000000, 200000000, 300000, 400]
    k = 200300400 <- искомая сумма подмассивов

          prefix[0] = 0
          prefix[1] = 100000000000
          prefix[2] = 100200000000
          prefix[3] = 100200300000
          prefix[4] = 100200300400

    Важное замечание:
    порядок наложения чисел друг на друга может быть любым!
    
    Ищем момент, когда:
    prefixSum[current] – prefixSum[previous] = k
    
    Поскольку префиксная сумма - это сумма подмассивов,
    если от текущей префиксной суммы отнять префиксную сумму
    на каком-то предыдущем этапе и получить k в остатке,
    это значит, что подмассив с суммой k существует!
    
    Иначе можно записать:
    prefixSum[current] – k = prefixSum[previous]

4. создать мапу для подсчёта префиксных сумм
5. сразу записать для нулевого индекса префиксную сумму, равную нулю
6. ввести счётчик для подсчёта подмассивов (индексы не нужны)
7. ввести аккумулятор для префиксной суммы (её не нужно предсохранять)
8. пройти по массиву, вычисляя префиксную сумму
9. если выполняется условие по описанной выше формуле - увеличить счётчик
a. в противном случае добавить в мапу найденную префиксную сумму
   и количество её появлений
*/

class Solution {
    public int subarraySum(int[] nums, int k) {
        
        Map<Integer, Integer> prefixSumCount = new HashMap<>();
        prefixSumCount.put(0, 1); 
        
        int count = 0;
        int currentSum = 0;
        
        for (int num : nums) {
            
            currentSum += num;
                        
            int target = currentSum - k;
            if (prefixSumCount.containsKey(target)) {
                count += prefixSumCount.get(target);
            }
                        
            prefixSumCount.put(currentSum, 
                               prefixSumCount.getOrDefault(currentSum, 0) + 1);
        }
        
        return count;
    }
}
```

SlidingWindow_LongestSubstringWithoutRepeatingCharacters
--------------------------------------------------------
Найти длину самой длинной подстроки без повторяющихся символов
```Java
/*
1. понимаем: типичный кейс из sliding window
2. понимаем: что мы ищем, - это, по сути, набор, ограниченный слева
             и справа, в котором мы считаем количество неповторяющихся символов,
             чтобы сравнивать с другими такими же наборами.
    
3. понимаем: логика
        -. ведем агрегат
        -. добавляем в него значения пока уникальны
        -. удаляем из него значения при нахождения дубликата

       abcdefd
       ------  <- начальный агрегат, когда дошли до (d)
       ----    <- то что придется удалить из агрегата
           --  <- каким будет агрегат на выходе при добавлении (d)

4. понимаем: агрегат представляем левым и правым указателями
             а его содержимое мапой

5. понимаем: остается вычисление промежуточной длины
             так как левый и правый указатель это индексы
             очевидно: промежуточная длина = правый_индекс - левый_индекс + 1
6. ввели стат переменную по максимальной длине
7. ввели сет для представления содержимого агрегата
8. двигаем оба указателя с нулевой отметки
   правый указатель двигается циклом - левый его догоняет на каждой итерации
   если находятся дубли
9. пока в сете хранятся дубли - подгоняем левый указатель
   вслед за правым
a. получаем за счет разницы право-лево длину и обновляем
   если требуется стат переменную
*/

  public class Solution {
    public int lengthOfLongestSubstring(String s) {
      Set<Character> windowChars = new HashSet<>();
      int maxLength = 0;
      int left = 0;
      for (int right = 0; right < s.length(); right++) {
        char currentChar = s.charAt(right);
        while (windowChars.contains(currentChar)) {
          windowChars.remove(s.charAt(left));
          left++;
        }
        windowChars.add(currentChar);
        maxLength = Math.max(maxLength, right - left + 1);
      }
      return maxLength;
    }
  }
```

SlidingWindow_MaximumAverageSubarray
------------------------------------
Найти максимальное среднее значение в подмассиве фиксированной длины k.
```Java
/*
1. понимаем: опять же классическое sliding window
2. понимаем: 

  например, подмассив из 4 элементов - и действие для этого агрегата,
  например, получение среднего.

        {1,2,3,4,5,6,7}
         -------            <- среднее для {1,2,3,4}
           -------          <- среднее для {2,3,4,5}
             -------        <- среднее для {3,4,5,6}
               -------      <- среднее для {4,5,6,7}
3. накапливаем окно
4. движем окно, и модифицируем статистику
*/

class Solution {
    public double findMaxAverage(int[] nums, int k) {
        
        double windowSum = 0;

        for (int i = 0; i < k; i++) {
            windowSum += nums[i];
        }
        
        double maxSum = windowSum;
                
        for (int i = k; i < nums.length; i++) {
            
            windowSum += nums[i] - nums[i - k];                        
            maxSum = Math.max(maxSum, windowSum);
        }
                
        return maxSum / k;
    }
}
```

SlidingWindow_MinimumWindowSubstring
------------------------------------
Найти минимальную подстроку в s, которая содержит все символы строки t.
```Java
/*
1. понимаем: на массиве: abxxxxcxxabc
   Мы ищем подстроку, в которой будет «abc»,
   при этом такую, где общее количество символов минимально.
    
   Прогресс на нашем примере будет таким:
        
          [abxxxxc]xxabc
          a[bxxxxcxxa]bc
          abxxxx[cxxa]bc
          abxxxxcxx[abc]    <- и вот мы наткнулись на самую короткую

2. понимаем: нам нужно окно с abc в любой последовательности
             как только выбывает из неё abc - идем искать следующее окно

3. для начала введем карту с частотностями - она будет использоваться 
   для определения вхождений
4. после этого зададим логически окно
    мапа + 
    левый индекс + 
    необходимое число из подстроки + 
    сформированное в моменте
5. введем статистику для возврата 
6. запускаем цикл для ПРАВОГО указателя    
    -. учитываем кейс: если найдено вхождение в (s) равное имеющемуся в (t)
    -. запускаем цикл для левого указателя, оно же сжатие окна
    
        abxxxxcxxabc
        [-------]      <- было
                [----]   <- стало (сжались иксы)
                [----] <- потом стало
                
        а. считаетм текущую длину в окне
        б. если текущая длина в окне меньше статистической минимальной
           обновляем статистику и индекс который начинает строку с этой статистикой
        в. учитываем левый указатель после сдвига вправо
        г. если символ выбыл учитываем это
        д. двигаем левый указатель вправо
7. возвращаем или пустую строку или вычисленную на базе статистики        
*/

public class Solution {

    public String minWindow(String s,String t) {

      Map<Character, Integer> tMap = new HashMap<>();
      for (char c : t.toCharArray()) {
        tMap.put(c,tMap.getOrDefault(c,0) + 1);
      }

      Map<Character, Integer> windowMap = new HashMap<>();
      int leftWindowIndex = 0;
      int requiredCharsInWindow = tMap.size();
      int formedCharsInwindow = 0;

      int minLeftIndexWhichFormResult = 0;
      int minResultLength = Integer.MAX_VALUE;

      for (int right = 0; right < s.length(); right++) {
        char rightChar = s.charAt(right);
        windowMap.put(rightChar, windowMap.getOrDefault(rightChar, 0) + 1);

        if (
            tMap.containsKey(rightChar) &&
            windowMap.get(rightChar).intValue() == tMap.get(rightChar).intValue()
        ) {
          formedCharsInwindow++;
        }

        while (leftWindowIndex <= right && formedCharsInwindow == requiredCharsInWindow) {

          int currentLength = right - leftWindowIndex + 1;

          if (currentLength < minResultLength) {
            minResultLength = currentLength;
            minLeftIndexWhichFormResult = leftWindowIndex;
          }

          char leftChar = s.charAt(leftWindowIndex);
          windowMap.put(leftChar, windowMap.get(leftChar) - 1);

          if (tMap.containsKey(leftChar) && windowMap.get(leftChar) < tMap.get(leftChar)) {
            formedCharsInwindow--;
          }

          leftWindowIndex++;
        }
      }
    }
}
```

TopKElementsOrMinMaxHeap_FindKPairsWithSmallestSums
---------------------------------------------------
Найти k пар с наименьшими суммами из двух массивов
Особенность задачи: nums1 и nums2 в неубывающем порядке.
```Java
/*
1. понимаем: все комбинации можно запихать в кучу и взять от туда лучший вариант
2. понимаем: куча будет огромной по памяти однако даст решение
3. понимаем: задачу можно оптимизировать, понимая расположение элементов 
                nums2[0]=4   nums2[1]=5    nums2[2]=6
    nums1[0]=1           5            6             7
    nums1[1]=1           5            6             7
    nums1[2]=2           6            7             8

    как видим решение идет увеличиваясь в нижний правый угол
    а лучшие решения находятся сверху слева, не понятно однако 
    брать их как по строке или по столбцу сдвигаясь
4. понимаем: очевидна формула для лучшести в куче, по условию задачи:
     (a, b) -> (nums1[a[0]] + nums2[a[1]]) - (nums1[b[0]] + nums2[b[1]])
5. создадим массив результата
6. создадим кучу с нашей формулой "лучшести"
7. не будем кучу забивать значениями целиком, а будем постепенно двигаться по ней
   согласно того как это выглядит на матрице слева направо, сверху вниз
   соблюдая то - что в куче всегда будет максимум "k" элементов, а не все
*/

  class Solution {
    public List<List<Integer>> kSmallestPairs(int[] nums1, int[] nums2, int k) {
      List<List<Integer>> result = new ArrayList<>();
      if (nums1.length == 0 || nums2.length == 0 || k == 0) {
        return result;
      }

      PriorityQueue<int[]> minHeap = new PriorityQueue<>(
          (a, b) -> (nums1[a[0]] + nums2[a[1]]) - (nums1[b[0]] + nums2[b[1]])
      );

      for (int i = 0; i < Math.min(nums1.length, k); i++) {
        minHeap.offer(new int[]{i, 0});
      }

      while (k-- > 0 && !minHeap.isEmpty()) {
        int[] indices = minHeap.poll();
        int i = indices[0];
        int j = indices[1];
        result.add(Arrays.asList(nums1[i], nums2[j]));

        if (j + 1 < nums2.length) {
          minHeap.offer(new int[]{i, j + 1});
        }
      }

      return result;
    }
  }
```

TopKElementsOrMinMaxHeap_KthLargestElement
------------------------------------------
Найти k-й наибольший элемент в массиве
```Java
/*
1. понимаем: куча обеспечит наибольше или наименьший элемента сама
2. помещаем все элементы в кучу
3. берем сверху неё лучший (наибольший) элемент
*/

class Solution {
    public int findKthLargest(int[] nums, int k) {
        
        PriorityQueue<Integer> minHeap = new PriorityQueue<>();
        
        for (int num : nums) {
            
            minHeap.offer(num);            
            
            if (minHeap.size() > k) {
                minHeap.poll();
            }
        }
                
        return minHeap.peek();
    }
}
```

TopKElementsOrMinMaxHeap_TopKFrequentElements
---------------------------------------------
Найти k наиболее часто встречающихся элементов в массиве
```Java
/*
1. понимаем: считаем частотность в формате: индекс(ключ) = частотность
        индекс 0 -> 8
        индекс 1 -> 6
        индекс 2 -> 3
        индекс 3 -> 4

2. понимаем: мы в куче храним частоты:

        Если изначально у нас там лежали просто значения, то теперь там будут лежать
        вхождения в мапу, которые выглядят вот так:
        Map.Entry<Integer, Integer>
        
        Для символов строки было бы очевидно:
        Map.Entry<Character, Integer>

3. понимаем: наша "особенная" куча с кастомной "лучшестью":
    Так как у нас содержится пара значений, очевидно, выталкивать вверх
    будем по признаку getValue в мап‑ентри (как мы помним, сравнение
    делается через логику: отрицательный результат / равны / положительный
    результат в разнице):

    PriorityQueue<Map.Entry<Integer, Integer>> minHeap =
        new PriorityQueue<>((a, b) -> a.getValue() - b.getValue());
4. предзаполнение мапы с частотами
5. хип с признаком «лучшести» на базе getValue
6. массив нам не нужен, нам нужен frequencyMap
7. осталось получить последние (k) элементов:
*/

public class Solution {
    public int[] topKFrequent(int[] nums, int k) {
      Map<Integer, Integer> frequencyMap = new HashMap<>();
      for (int num : nums) {
        frequencyMap.put(num, frequencyMap.getOrDefault(num, 0) + 1);
      }

      PriorityQueue<Map.Entry<Integer, Integer>> minHeap =
          new PriorityQueue<>((a, b) -> a.getValue() - b.getValue());

      for (Map.Entry<Integer, Integer> entry : frequencyMap.entrySet()) {
        minHeap.offer(entry);
        if (minHeap.size() > k) {
          minHeap.poll();
        }
      }

      int[] result = new int[k];
      int index = 0;
      while (!minHeap.isEmpty()) {
        result[index++] = minHeap.poll().getKey();
      }

      return result;
    }
}
```

TwoPointers_ContainerWithMostWater
----------------------------------
Найти два столбца, которые вместе с осью X образуют контейнер,
содержащий максимальное количество воды
```Java
/*
1. понимаем: ищем не сложную площадь, как здесь
   между двойкой и тройкой, а просто два столбца 
   который образуют максимальную площадь

                        x
                    x - х
        x - - - - - x - х
      x x x x x x x x x x x
      ---------------------
      1 2 1 1 1 1 1 3 1 4 1

2. понимаем: достаточно найти просто два столбца
             и сделать вычисление между ними:

        здесь всё просто: 
        ищем 3 и 4 и площадь по меньшей высоте (3)*(разнцу индексов)

                        x
        x               х
        x               х
      x x x x x x x x x x x
      ---------------------
      1 3 1 1 1 1 1 1 1 4 1

3. понимаем: двигаем левый и правый указатели и считаем 
             на каждом шаге площади, пока они не встретятся
4. понимаем: логика движения указателей
             принцип стремления к большему
                -. сдвиг указателя с меньшей высотой вперёд.
                -. если высоты одинаковы - двигаем левый.            
             
             почему не с большей? может найтись с другой стороны более 
             крупный бортик и там окажется большая площадь

5. понимаем: жадное движение сделает возможным отлов такого кейса
             (левый указатель должен двигаться вперед изначально, не правый)

            x - - - - - x
        x - x - - - x - х
        x - x - - - x - х
      x x x x x x x x x x x
      ---------------------
      1 4 1 3 1 1 1 3 1 4 1

6. расставляем указатели по краям (бортики ванной)
7. всегда двигаем к центру меньший
8. ширина = разница индексов
9. высота = минимум между левым и правым
a. считаем площадь + обовляем стат переменную с максимумом
*/

class Solution {
    public int maxArea(int[] height) {
        int left = 0;
        int right = height.length - 1;
        int maxArea = 0;
                
        while (left < right) {
            
            int width = right - left;
            int minHeight = Math.min(height[left], height[right]);
                        
            int area = width * minHeight;
            maxArea = Math.max(maxArea, area);
                        
            if (height[left] < height[right]) {left++;} 
            else {right--;}
        }
        
        return maxArea;
    }
}
```

TwoPointers_TwoSum2InputArrayIsSorted
-------------------------------------
Найти два числа в отсортированном массиве, которые в сумме дают целевое
значение (индексы начинаются с 1).
```Java
/*
1. понимаем: {1,2,3,4,5,6,7,8,9,10,11} у нас есть массив
2. понимаем: указатели на стороны левую и правую
3. понимаем: берем большое число и маленькое - и ищем сумму target
4. понимаем: как двигаются указатели (логика):
    - если результат МЕНЬШЕ целевого из суммы двух чисел с краёв -
      сдвигаем левый указатель (подтверждая: если мы уже и так меньше,
      нам двигаться отсюда только к увеличению);
    - если результат БОЛЬШЕ целевого из суммы двух чисел с краёв -
      сдвигаем правый указатель (подтверждая: если мы и так решения
      с этим числом не нашли, следовательно, его надо уменьшать).
5. понимаем: 
    Почему двигается левый указатель? Потому что если сумма крупного числа
    с небольшим числом меньше, чем целевое, то сдвиг крупного числа вправо
    всё равно не приведёт к решению.
    
    Почему сдвигается правый указатель? Потому что если у нас недолёт,
    надо сдвигать левый указатель, а если перелёт - очевидно, мы уже все варианты
    с правым («крупным») числом попробовали, и решения с ним просто нет.
    То есть его надо уменьшать.
6. устанавливаем указатели в края
7. пока они не пересекуться запускаем цикл
    считаем сумму 
        сумма меньше сдвигаем левый вправо
        сумма больше сдвигаем правый влево
*/

class Solution {
    public int[] twoSum(int[] numbers, int target) {
        int left = 0;
        int right = numbers.length - 1;
                
        while (left < right) {
            int sum = numbers[left] + numbers[right];
            
            if (sum == target) {
                
                return new int[]{left + 1, right + 1};
            } 
            else if (sum < target) {left++;} 
            else {right--;}
        }
                
        return new int[]{-1, -1};
    }
}
```

TwoPointers_ThreeSum
--------------------
Найти все уникальные тройки в массиве, сумма которых равна нулю.
```Java
/*
1. понимаем: отдельного решения для три суммы просто нет
2. понимаем: запускаем цикл по всем элементам, после чего запускаем
             стандартную дву сумму
3. понимаем: незабываем сужать область поиска по каждой 
             итерации верхнего цикла  
4. в коде оптимизация в кейсе когда нашлась комбинация, её может не быть
*/

class Solution {
    public List<List<Integer>> threeSum(int[] nums) {
        List<List<Integer>> result = new ArrayList<>();
                
        Arrays.sort(nums);
        
        for (int i = 0; i < nums.length - 2; i++) {
            
            if (i > 0 && nums[i] == nums[i - 1]) {continue; }
            
            int left = i + 1;
            int right = nums.length - 1;
            int target = -nums[i];            
            
            while (left < right) {
                int sum = nums[left] + nums[right];
                
                if (sum == target) {
                    
                    result.add(Arrays.asList(nums[i], nums[left], nums[right]));
                    
                    while (left < right && nums[left] == nums[left + 1]) {
                        left++;
                    }
                    
                    while (left < right && nums[right] == nums[right - 1]) {
                        right--;
                    }
                                        
                    left++;
                    right--;

                } 
                else if (sum < target) {left++;} 
                else {right--;}
            }
        }
        
        return result;
    }
}
```
