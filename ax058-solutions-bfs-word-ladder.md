[Оглавление](README.md)

Решебник. BreadthFirstSearch_WordLadder
===
```java
package ru.pragm;

import java.util.*;
import static ru.pragm.utils.Out.out;

/*
Задача:
BreadthFirstSearch
WordLadder
---------------------------------
    Условие: даны начальное слово (beginWord), конечное слово (endWord) и словарь слов (wordList).
    Нужно найти длину кратчайшей последовательности преобразований из beginWord в endWord, где:
    - каждое преобразование меняет ровно одну букву;
    - каждое промежуточное слово должно быть в wordList.
    Вернуть 0, если преобразование невозможно.

Ироничное вступление:
---------------------

    Прежде чем рассматривать эту задачу, я предлагаю рассмотреть другую -
    также из категории hard. Она называется «всегда выбирай „правильный путь“».
    Единственное: так как вы за себя отвечать не можете, это всё-таки книга -
    я буду отвечать за вас.
    
    Итак, задача.
    
    Я задумал граф, вот он:
    
                      (1)
                      / \
                    (2)(3)
                       / \
                     (4) (5)
    
    Но это очень хитрый граф, потому что я его не только задумал,
    но и спрятал все соединения!
    (Забудьте, что вы только что выше видели.)
    
    Граф выглядит так:
    
                      (1)
    
                    (2)(3)
    
                     (4) (5)
    
    И вам надо по этому графу путешествовать из ноды (1)
    до ноды (5). Однако я буду каждый раз давать вам подсказку:
    
    Я буду называть в каждой ноде вам варианты, и вам надо будет
    угадать там «правильный путь»
    (я напомню: буду отвечать за вас).
    
    Итак, поехали.
    Из ноды (1) до ноды (3) есть три варианта пути:
    1. колбаса;
    2. номер 144;
    3. «правильный путь».
    
    Какой путь вы выберите?
    
    - Вы, не веря в происходящее: третий.
    
    Ладно, в этот раз вам повезло.
    
    Из ноды (3) в ноду (5) есть три пути:
    1. номер четыреста семьдесят семь;
    2. «правильный путь»;
    3. сложность алгоритма O(n).
    
    Какой путь вы выбираете?
    
    - Вы смотрите на меня, очень медленно моргая,
    и говорите: второй.
    
    А я, не веря в происходящее, вас спрашиваю:
    - Как вам это удаётся? Каждый раз же разные варианты
    я вам даю. Почему вы каждый раз угадываете пути?
    В графе же они все СКРЫТЫ.
    
    На что вы отвечаете:
    - Как же они скрыты, если они всегда детерминированы?
    Если мы всегда можем получить путь,
    как же он тогда скрыт, если он виден?
    Его просто видно не на графе целиком, а лишь с каждого отдельного нода
    до другого нода.
    И кому вообще пришло в голову
    происходящее назвать hard-задачей?
    
    Вот и на этой мысли
    мы теперь переходим к разбору задачи WordLadder.

(1) Формализация задачи и её суть.

    Итак, что от нас хотят?
    У нас есть какой-то массив слов (wordList):
    ["hot","dot","dog","lot","log","cog"].
    
    Начальное слово (beginWord): "hit".
    Конечное слово (endWord): "cog".
    
    Задача просит нас добраться из beginWord до endWord
    через wordList, меняя в каждом из них лишь одну букву.
    
    Вообще, читая задачу, создаётся ощущение, что это задача на что-то среднее
    между лингвистикой и теорией графов.
    
    Но это совершенно не так. Достаточно её просто перефразировать:
    
    У нас есть ГРАФ,
    образованный словами wordList.
    Соединения между ними скрыты и являются вычисляемыми,
    но вычисляемыми детерминировано, всегда по одной и той же формуле.
    И вот это вычисление равняется «замени одну букву».
    Нам надо по этому графу добраться от beginWord до endWord
    и вернуть количество шагов, за которые мы сможем добраться.
    
    Отбросив то, что соединения «скрыты», и вспомнив нашу мета-иронию
    в начале задачи, можем увидеть: от нас просто просят
    по графу добраться из одной точки до другой, считая уровни.
    
    Как добираются в графах, считая уровни?
    
    (1) Или с помощью DFS, вводя дополнительную структуру visited,
    или times, или что-то типа того (нужно программировать дополнительную
    структуру данных).
    
    (2) Или итерационно волновым способом, перебирая все направления
    на каждой итерации (медленно).
    
    (3) Или с помощью BFS, который и так распространяется уровнями,
    особенно в его версии «BFS с накоплением уровня».
    И всё, что от нас потребуется, - это просто на каждой итерации
    этот уровень считать.
    
    Остаётся только вопрос с вычислением правила:
    для того чтобы было понятно, куда на каждой итерации BFS можно двигаться,
    а куда нет.
    
    Если такое понимание есть,
    то задача становится максимально примитивной. Если нет,
    тогда она, да, действительно выглядит сложной.
    
    И вот этот вот вид графов, с вычисляемыми переходами,
    называется «неявный граф».
    
    Смотрите:
    
    явный граф -> O(1) доступ к соседям, но может не помещаться в память;
    неявный граф -> соседи генерируются «на лету», экономя память, но требуя вычислений.
    
    Но с точки зрения алгоритма - это один и тот же граф.
    Просто его рёбра «проявляются по запросу».

(2) идея решения

    У нас тут явный BFS. Решение в лоб нам явно не интересно,
    поэтому давайте сразу переходить к нему и попытаемся понять,
    как нам из этой задачи BFS в принципе организовать.
    
    Напомню, единственное отличие от «явного» собрата -
    это наличие вычисления правильного пути на каждой из итераций.
    Проблема восприятия алгоритма скорее порождается тем, что это вычисление
    втянуто в основной код, а не выделено в виде отдельной лямбды.
    Но так как вы его именно в таком виде и будете обычно видеть,
    предлагаю на него именно так и посмотреть.
    
    Давайте прямо сравним:
    - BFS с накоплением уровней;
    - требуемое от нас решение по этой задаче.
    
    BFS с накоплением уровней:
    --------------------------
    Основная идея в том, что мы в начале каждой итерации:
    1) сбрасываем всё, что есть в очереди BFS,
       и обрабатываем эти элементы согласно задаче;
    2) помещаем в очередь вместо них
       уже «соседей»/«потомков»/«детей».
    
    В примере у нас потомка два - поэтому это просто
    if(current.left != null) / if(current.right != null).
    Если бы их было несколько и это был бы массив,
    там стоял бы просто цикл.

          public List<List<Integer>> bfsLevelByLevel(TreeNode root) {
            List<List<Integer>> result = new ArrayList<>();
            if (root == null) return result;

            Queue<TreeNode> queue = new LinkedList<>();
            queue.offer(root);                                  // <- начальное заполнене очереди

            while (!queue.isEmpty()) {                          // <- цикл BFS
              int levelSize = queue.size();
              List<Integer> currentLevel = new ArrayList<>();

              for (int i = 0; i < levelSize; i++) {             // <- обработка уровня
                TreeNode current = queue.poll();                // <- выброс того что в очереди
                currentLevel.add(current.val);

                if (current.left != null) {
                  queue.offer(current.left);                    // <- заполнение потомками
                }
                if (current.right != null) {
                  queue.offer(current.right);                   // <- заполнение потомками
                }
              }
              result.add(currentLevel);
            }
            return result;
          }

    а теперь рассмотрим решение задачи про слова -
    решение с неявным графом и BFS с накоплением уровней:
    -----------------------------------------------------
    сначала может показаться, что код выглядит довольно объёмным,
    но это только потому, что мы распределили логику выбора «правильного пути»
    прямо по нему.
    
    логика выбора такого пути здесь очень простая:
    1. у нас есть хешмеп со словами из wordList;
    2. на каждой итерации, когда нужно определить «правильный путь»,
       мы генерируем изменения для текущего слова - того, в котором находимся;
    3. все сгенерированные изменения сравниваем с тем, что есть в хешмепе;
    4. вуаля - у нас есть правильные пути, и мы можем продолжать двигать BFS.
    
    смотрите сами:

          public class Solution {
              public int ladderLength(String beginWord, String endWord, List<String> wordList) {

                  // <- Преобразуем wordList в Set для быстрого поиска O(1)
                  //
                  Set<String> wordSet = new HashSet<>(wordList);

                  // <- Проверка: если endWord нет в словаре, преобразование невозможно
                  //
                  if (!wordSet.contains(endWord)) {
                      return 0;
                  }

                  // <- BFS очередь для хранения слов и их уровня
                  //
                  Queue<String> queue = new LinkedList<>();
                  queue.offer(beginWord);

                  // <- Множество посещенных слов
                  //
                  Set<String> visited = new HashSet<>();
                  visited.add(beginWord);

                  // <- Начинаем с уровня 1 (beginWord)
                  //
                  int level = 1;

                  // <- BFS обход
                  //
                  while (!queue.isEmpty()) {
                      int levelSize = queue.size();

                      // <- Обрабатываем все слова текущего уровня
                      //
                      for (int i = 0; i < levelSize; i++) {

                          String currentWord = queue.poll();

                          // <- Если достигли endWord, возвращаем текущий уровень
                          //
                          if (currentWord.equals(endWord)) {
                              return level;
                          }

                          // <- Генерируем все возможные преобразования текущего слова
                          //
                          char[] wordChars = currentWord.toCharArray();

                          for (int j = 0; j < wordChars.length; j++) {
                              char originalChar = wordChars[j];

                              // <- Пробуем все возможные буквы на этой позиции
                              //
                              for (char c = 'a'; c <= 'z'; c++) {
                                  if (c == originalChar) continue;

                                  wordChars[j] = c;
                                  String newWord = new String(wordChars);

                                  // <- Если новое слово в словаре и не посещено
                                  //
                                  if (wordSet.contains(newWord) && !visited.contains(newWord)) {
                                      queue.offer(newWord);
                                      visited.add(newWord);
                                  }
                              }

                              // <- Восстанавливаем оригинальную букву
                              //
                              wordChars[j] = originalChar;
                          }
                      }

                      // <- Переходим на следующий уровень
                      //
                      level++;
                  }

                  // <- Не нашли путь до endWord
                  //
                  return 0;
              }
          }

    здесь можно было бы сказать, что здесь, кроме BFS, хоть что‑то сложное проявляется.
    нет.
    не проявляется.
    здесь имеет место типичное смешивание логики - и вот как раз антипаттерн:
    нарушение «единой ответственности».
    
    смотрите, я специально сейчас определение «правильного пути» выделю в лямбду -
    как резко очистится код!
    и решение сведётся к написанию лямбды, определяющей «правильный путь».
    и в алгоритме больше ничего не останется в плане сложности.

          public class Solution {

              public int ladderLength(String beginWord, String endWord, List<String> wordList) {

                  // 1. Преобразуем словарь в Set для O(1) проверок
                  //
                  Set<String> wordSet = new HashSet<>(wordList);
                  if (!wordSet.contains(endWord)) return 0; // Нет конечной точки - нет пути

                  // 2. Лямбда для получения "правильных путей" (соседей в неявном графе)
                  //
                  //    Принимает текущее слово и возвращает список слов, достижимых из него
                  //    за одно преобразование (одна буква) и присутствующих в словаре.
                  //
                  Function<String, List<String>> getNeighbors = (String word) -> {
                      List<String> neighbors = new ArrayList<>();
                      char[] chars = word.toCharArray();

                      for (int i = 0; i < chars.length; i++) {
                          char originalChar = chars[i];
                          // Пробуем все возможные замены на буквы от 'a' до 'z'
                          for (char c = 'a'; c <= 'z'; c++) {
                              if (c == originalChar) continue;
                              chars[i] = c;
                              String newWord = new String(chars);
                              // Проверка, является ли слово допустимым переходом
                              if (wordSet.contains(newWord)) {
                                  neighbors.add(newWord);
                              }
                          }
                          chars[i] = originalChar; // Восстанавливаем букву
                      }
                      return neighbors;
                  };

                  // 3. Классический BFS по уровням
                  //
                  Queue<String> queue = new LinkedList<>();
                  Set<String> visited = new HashSet<>();

                  queue.offer(beginWord);
                  visited.add(beginWord);

                  int level = 1;

                  while (!queue.isEmpty()) {
                      int levelSize = queue.size();

                      for (int i = 0; i < levelSize; i++) {
                          String currentWord = queue.poll();

                          if (currentWord.equals(endWord)) {
                              return level;
                          }

                          // <- сложность ушла сюда
                          //
                          List<String> neighbors = getNeighbors.apply(currentWord);

                          for (String neighbor : neighbors) {
                              if (!visited.contains(neighbor)) {
                                  visited.add(neighbor);
                                  queue.offer(neighbor);
                              }
                          }
                      }
                      level++;
                  }

                  return 0;
              }
          }

(3) Порядок кодирования

    Я мог бы сказать, что в этой задаче возможна оптимизация с двунаправленным BFS, 
    но, на мой взгляд, для решения подобной задачи это уже излишне (в примере я его 
    приведу).
    
    Давайте лучше подойдём к тому, как же кодировать такие задачи.
    Очень просто.
    
    Разделять: код, ответственный за поиск «правильного пути» - моё предложение.
    Не выводить его в отдельный метод, а просто писать лямбду, поскольку метод не
    совсем самостоятельный (хотя это дело вкуса).
    
    И код, который реализует BFS.
    
    Более того, вы только что наглядно увидели, что происходит, когда нарушается
    принцип единой ответственности в коде, и к чему это приводит.
    
    Здесь всего ДВЕ таких концепции. А теперь подумайте, что будет, если их станет
    три, четыре, пять? Именно поэтому вам нужно учиться видеть точки логического
    разреза отдельно взятых процессов - и не смешивать всё в кучу.
* */
public class BreadthFirstSearch_WordLadder {

  public class Solution {
    public int ladderLength(String beginWord, String endWord, List<String> wordList) {
      Set<String> wordSet = new HashSet<>(wordList);
      if (!wordSet.contains(endWord)) {
        return 0;
      }
      Queue<String> queue = new LinkedList<>();
      queue.offer(beginWord);
      Set<String> visited = new HashSet<>();
      visited.add(beginWord);
      int level = 1;
      while (!queue.isEmpty()) {
        int levelSize = queue.size();
        for (int i = 0; i < levelSize; i++) {
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
              String newWord = new String(wordChars);
              if (wordSet.contains(newWord) && !visited.contains(newWord)) {
                queue.offer(newWord);
                visited.add(newWord);
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

  public class Solution2WayBFS {
    public int ladderLength(String beginWord, String endWord, List<String> wordList) {
      Set<String> wordSet = new HashSet<>(wordList);
      if (!wordSet.contains(endWord)) return 0;

      // <- два набора для двунаправленного BFS
      //
      Set<String> beginSet = new HashSet<>();
      Set<String> endSet = new HashSet<>();
      beginSet.add(beginWord);
      endSet.add(endWord);

      Set<String> visited = new HashSet<>();
      visited.add(beginWord);
      visited.add(endWord);

      int level = 1;

      while (!beginSet.isEmpty() && !endSet.isEmpty()) {

        // <- всегда работаем с меньшим набором для оптимизации
        //
        if (beginSet.size() > endSet.size()) {
          Set<String> temp = beginSet;
          beginSet = endSet;
          endSet = temp;
        }

        Set<String> nextLevel = new HashSet<>();

        for (String word : beginSet) {
          char[] wordChars = word.toCharArray();

          for (int i = 0; i < wordChars.length; i++) {
            char original = wordChars[i];

            for (char c = 'a'; c <= 'z'; c++) {
              if (c == original) continue;

              wordChars[i] = c;
              String newWord = new String(wordChars);

              // <- если встретили слово из другого набора - путь найден
              //
              if (endSet.contains(newWord)) {
                return level + 1;
              }

              if (wordSet.contains(newWord) && !visited.contains(newWord)) {
                nextLevel.add(newWord);
                visited.add(newWord);
              }
            }

            wordChars[i] = original;
          }
        }

        beginSet = nextLevel;
        level++;
      }

      return 0;
    }
  }

  static void main() {
    var wrapper = new BreadthFirstSearch_WordLadder();
    var solution = wrapper.new Solution();
    var solution2WayBFS = wrapper.new Solution2WayBFS();

    var beginWord = "hit";
    var endWord = "cog";
    var wordList = Arrays.asList("hot", "dot", "dog", "lot", "log", "cog");

    int result1 = solution.ladderLength(beginWord, endWord, wordList);
    int result2 = solution2WayBFS.ladderLength(beginWord, endWord, wordList);

    out("Стандартный BFS: " + result1);
    out("Двунаправленный BFS: " + result2);

    if (result1 == 5 && result2 == 5) {
      out("✓ Тест пройден. Оба алгоритма дают правильный результат: 5");
    } else {
      out("✗ Тест не пройден.");
      out("  Ожидалось: 5");
      out("  Получено (стандартный): " + result1);
      out("  Получено (двунаправленный): " + result2);
    }
  }
}//c:BreadthFirstSearch_WordLadder

```