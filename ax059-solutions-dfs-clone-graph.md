[Оглавление](README.md)

Решебник. DepthFirstSearch_CloneGraph
===
```java
package ru.pragm;

import java.util.*;
import static ru.pragm.utils.Out.out;

/*
Задача:
DepthFirstSearch
CloneGraph
---------------------------------
    Условие: дана ссылка на узел в связном неориентированном графе.
    Вернуть глубокую копию (клон) графа.

(1) Формализация задачи и её суть
    Что от нас по сути хотят?
    У нас не дерево, а именно неориентированный граф - то есть узлы могут идти куда угодно.
    
    Нам надо:
    а) как‑то обойти его;
    б) обходя его, создать полную копию того, что мы обошли.
    
    Это и будет глубокой копией.
    При этом граф может быть ещё и с циклами:
    
        1 -- 2
        |    |
        4 -- 3
    
    Появляется третья задача - начать отслеживать наше перемещение по графу. То есть ввести
    структуру данных, которая будет отмечать: были ли мы где‑то или нет.
    
    Граф можно обойти двумя способами: с помощью BFS или с помощью DFS.
    Разницы тут особенно нет - это граф, а не дерево (особенно разница теряется, если
    смотреть на примере сверху).
    
    Логика работы алгоритма прозрачна, если мы знаем, как работает DFS/BFS:
    - идём по графу;
    - если узел не посещён - делаем его клон через создание нового элемента и прикрепляем
      его к структуре‑дублю;
    - в структуре данных, отслеживающей посещения, отмечаем этот узел как посещённый;
    - как только обойдём весь граф, структура‑дубль заполнится сама собой.
    
    При BFS граф обойдён, когда закончится очередь, в которую новые узлы прибывают раз
    за разом.
    
    При DFS граф обойдён, когда дойдём по всем ветвям рекурсии до if(node == null) {}.
    (Визуально это чуть тяжелее отслеживать, но не принципиально - результат будет одним
    и тем же.)
    
    Разница BFS / DFS здесь:
    
        1 -- 2
        |    |
        4 -- 3
    
    BFS: (1) -> [(2),(4)] -> (4) -> очередь кончилась.
    DFS: из (1) в (4), дальше (3), дальше (2) -> (1) уже были -> конец.
    
    Мне больше нравится логика BFS - её проще отлаживать (если посмотреть на задачу
    BinaryTreeLevelTraversal, видно, как это делается).
    
    О том, как работают деревья, смотрите в разделе задач BinaryTreeTraversal и в основной
    части книги - здесь мы это повторно не расписываем.

(2) Идея решения
    У нас есть понимание, что мы программируем, и методы, которыми будем решать.
    Дадим два решения.
    
    Единственное, чего не хватает - инструментов.
    
    При примитивном обходе деревьев с помощью DFS/BFS задача учит нас тому, что нужно
    вести учёт посещённых узлов. То есть потребуется структура данных для этого.
    
    В Java объект (его адрес) может быть ключом - почему бы его и не взять?
    
    Также потребуется собирать новый граф - то есть ещё одна структура: «итоговый граф».
    
    Возникает вопрос оптимизации: нужны ли две структуры данных (отслеживание посещённых
    и новый граф) или можно обойтись одной?
    
    Классическое решение алгоритма говорит: обходись одной.
    
    Несмотря на то, что это запутывает код и делает его неадекватным с точки зрения единой
    ответственности (структура выполняет две логически разорванные вещи), это применимо
    в кодировании алгоритмов, где оптимизация обычно поощряется.
    
    В итоге получаем:
        private Map<Node, Node> visited = new HashMap<>();
    
    С одной стороны, это:
        private Map<Node, Boolean> visited = new HashMap<>();
    
    С другой стороны, это:
        private List<Node> result = ...
    
    При этом задаётся соответствие того, что было, к чему скопировано.
    
    В private Map<Node, Node> visited = new HashMap<>():
    - ключ - нода, с которой копируем;
    - значение - нода, в которую копируем.

(3) Порядок кодирования
    Теперь, когда всё понятно, давайте кодировать.
    Не ограничимся DFS - дадим и BFS‑решение, так как в плане логики задачи они аналогичны.
    
    Если понять, что у нас появляется этот «чудо‑агрегат»:
        private Map<Node, Node> visited = new HashMap<>();
    
    - то всё становится яснее.
    
    Я бы до такого не додумался и стал бы вести структуры отдельно. Если вам кажется, что
    это решение выделяется - вам не случайно так кажется. Это оптимизация, и тот, кто её
    придумал, сильно продвинул алгоритм вперёд, создав карту копирования и убрав, по моему
    мнению, четыре операции из неоптимальной версии.
    
    Как будем кодировать:
    1. Напишем алгоритм DFS/BFS в чистом виде.
    2. В самую его верхушку вынесем нашу «чудо‑карту».
    3. Так как эта карта стала «отслеживателем копирования», единственное условие при
       достижении узла и решении вопроса - делать клон или нет - это наличие узла в этой
       карте (что делает алгоритм примитивным).
    
    Если бы не было этой карты в таком виде, а были бы карта visited + лист с результатом,
    код стал бы сложнее.
    
    Ещё одно важное замечание: у нас интересное задание ноды. В отличие от деревьев,
    здесь используются соседи:
        public List<Node> neighbors;
    
    Это означает, что алгоритм в любом случае на каждой итерации - 
    будь то DFS или BFS - будет проходить по соседям через цикл for.
    
    С точки зрения когнитивной сложности здесь происходит следующее:
    - две механики (DFS/BFS + клонирование);
    - одна архитектурная механика: отслеживание visited и результата клонирования
      в одной структуре данных.
    
    Из‑за этого код становится немного запутанным.
    
    Я рекомендую писать код в таком порядке:
    1. Сначала реализовать алгоритм обхода и убедиться, что он работает.
    2. Затем добавить структуру данных visited.
    3. Потом реализовать создание нового узла и его добавление в visited.
    
    Есть ещё один инсайт: обратите внимание, что, поскольку граф может быть замкнутым,
    - соседей мы добавляем всегда;
    - а клонируем узлы не всегда (см. комментарий в коде).
    
    То есть «клонировать и добавить» - это неправильно. Должно быть:
    - клонировать - отдельная логика (не всегда);
    - добавить - отдельная логика (всегда).
    
    Также обратите внимание: я ввожу большой блок просто для вывода того,
    что происходит. Это значит, что программировать придётся вслепую,
    и это тоже усложняет задачу.
    
    Моё мнение: эта задача когнитивно довольно сложна. В ней есть:
    - инсайты;
    - смешение механик;
    - сложность отладки.
    
    В решении мало строк кода, но очень легко сделать ошибку.
    
    Для примера посмотрим, что будет с решением, если не объединять
    структуры данных в одну. Как видим, это уже больше похоже на тренажёр
    по набору кода - его стало просто очень много, - чем на алгоритм:

    public class SolutionNaive {
        public Node cloneGraph(Node node) {
            if (node == null) return null;

            Map<Node, Boolean> visited = new HashMap<>();
            Map<Node, Node> clones = new HashMap<>();
            Queue<Node> queue = new LinkedList<>();

            // <- первый проход - создаём все клоны
            //
            queue.offer(node);
            visited.put(node, true);
            while (!queue.isEmpty()) {
                Node current = queue.poll();
                clones.put(current, new Node(current.val));

                for (Node neighbor : current.neighbors) {
                    if (!visited.containsKey(neighbor)) {
                        visited.put(neighbor, true);
                        queue.offer(neighbor);
                    }
                }
            }

            // <- второй проход - устанавливаем связи
            //
            visited.clear();
            queue.offer(node);
            visited.put(node, true);
            while (!queue.isEmpty()) {
                Node current = queue.poll();
                Node currentClone = clones.get(current);

                for (Node neighbor : current.neighbors) {
                    currentClone.neighbors.add(clones.get(neighbor));
                    if (!visited.containsKey(neighbor)) {
                        visited.put(neighbor, true);
                        queue.offer(neighbor);
                    }
                }
            }

            return clones.get(node);
        }
    }
* */
public class DepthFirstSearch_CloneGraph {

  public class GraphPrinter {
    public static String printGraph(Node node) {
      if (node == null) return "Empty graph";

      Map<Node, Integer> visited = new HashMap<>();
      Queue<Node> queue = new LinkedList<>();
      List<String> result = new ArrayList<>();

      visited.put(node, 1);
      queue.offer(node);

      while (!queue.isEmpty()) {
        Node current = queue.poll();
        List<String> neighborVals = new ArrayList<>();

        for (Node neighbor : current.neighbors) {
          neighborVals.add(String.valueOf(neighbor.val));
          if (!visited.containsKey(neighbor)) {
            visited.put(neighbor, 1);
            queue.offer(neighbor);
          }
        }

        result.add(String.format("Node %d -> [%s]",
                                 current.val,
                                 String.join(", ", neighborVals)));
      }

      return String.join("\n", result);
    }
  }

  class Node {
    public int val;
    public List<Node> neighbors;
    public Node() {
      val = 0;
      neighbors = new ArrayList<Node>();
    }
    public Node(int _val) {
      val = _val;
      neighbors = new ArrayList<Node>();
    }
    public Node(int _val, ArrayList<Node> _neighbors) {
      val = _val;
      neighbors = _neighbors;
    }
  }

  public class SolutionBFS {
    public Node cloneGraph(Node node) {

      if (node == null) { return null;}

      Map<Node, Node> visited = new HashMap<>();
      Queue<Node> queue = new LinkedList<>();

      Node cloneNode = new Node(node.val);
      visited.put(node, cloneNode);
      queue.offer(node);

      while (!queue.isEmpty()) {
        Node current = queue.poll();
        Node currentClone = visited.get(current);

        for (Node neighbor : current.neighbors) {
          if (!visited.containsKey(neighbor)) {               // <- единственное условие клонирования
            Node neighborClone = new Node(neighbor.val);
            visited.put(neighbor, neighborClone);
            queue.offer(neighbor);
          }
          currentClone.neighbors.add(visited.get(neighbor));  // <- добалвение в любом случае
                                                              //    граф же может содержать циклы
        }
      }

      return cloneNode;
    }
  }

  // <- рекурсивная же версия DFS обладает совсем малым числом строк кода,
  //    но когнитивно еще больше запутанная, поэтому я её прокомментирую
  //
  public class Solution {
    private Map<Node, Node> visited = new HashMap<>();

    public Node cloneGraph(Node node) {
      if (node == null) {
        return null;
      }

      // <- если узел уже посещен, возвращаем его клон
      //
      if (visited.containsKey(node)) {
        return visited.get(node);
      }

      // <- создаем клон текущего узла
      //
      Node cloneNode = new Node(node.val);
      visited.put(node, cloneNode);

      // <- рекурсивно клонируем всех соседей
      //
      for (Node neighbor : node.neighbors) {
        cloneNode.neighbors.add(cloneGraph(neighbor));
      }

      return cloneNode;
    }
  }

  static void main() {
    var wrapper = new DepthFirstSearch_CloneGraph();

    // граф: 1 -- 2
    //       |    |
    //       4 -- 3
    Node node1 = wrapper.new Node(1);
    Node node2 = wrapper.new Node(2);
    Node node3 = wrapper.new Node(3);
    Node node4 = wrapper.new Node(4);

    node1.neighbors.add(node2);
    node1.neighbors.add(node4);

    node2.neighbors.add(node1);
    node2.neighbors.add(node3);

    node3.neighbors.add(node2);
    node3.neighbors.add(node4);

    node4.neighbors.add(node1);
    node4.neighbors.add(node3);

    out("оригинал:");
    out(GraphPrinter.printGraph(node1));

    var solution = wrapper.new Solution();
    var cloned = solution.cloneGraph(node1);

    var solutionBFS = wrapper.new SolutionBFS();
    var clonedBFS = solutionBFS.cloneGraph(node1);

    out("");
    out(GraphPrinter.printGraph(cloned));

    out("");
    out(GraphPrinter.printGraph(clonedBFS));

  }
}//c:DepthFirstSearch_CloneGraph

```