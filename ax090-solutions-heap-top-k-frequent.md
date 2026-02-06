[Оглавление](README.md)

Решебник. TopKElementsOrMinMaxHeap_TopKFrequentElements
===
```java
package ru.pragm;

import java.util.Arrays;
import java.util.HashMap;
import java.util.Map;
import java.util.PriorityQueue;

import static ru.pragm.utils.Out.*;
/*
Задача:
TopKElementsORminMaxHeap
TopKFrequentElements
---------------------------------
    Условие: дан массив целых чисел и целое число k.
    Вернуть k наиболее часто встречающихся элементов.
    
    Эта задача по сути полностью аналогична задаче на K‑largest.
    Единственное, что она требует, - считать не значение для каждого индекса,
    а частотность - и не для индекса, а для отдельного числа.
    
    Но для показательности и понятности, чтобы не путаться с цифрами,
    давайте представим, что у нас считается частотность не для набора чисел,
    а для строки символов. Так у нас будет не «отдельное число»,
    а «отдельный символ» (частотность же в строке - это то же самое).
    
    Так как «отдельный символ» становится по сути ключом для такой
    дата‑структуры, нам потребуется дополнительно к массиву ввести
    ещё и мапу - то есть дата‑структуру, в которой как раз «отдельные символы»
    могут быть ключами.
    
    То есть буквально для значений у нас было следующее:
    индекс 0 -> 8
    индекс 1 -> 6
    индекс 2 -> 3
    индекс 3 -> 4
    
    И в случае, если в хипе есть три значения (8), (6), (3),
    по факту обработки массива слева направо мы дошли до (4) и положили его в хип.
    Минимальным значением в хипе стал (3) - и он оттуда удаляется.
    
    Для символов будет всё то же самое, но с учётом того, что у нас
    будет построена дополнительная мапа для частот (я специально возьму
    те же самые значения, чтобы было понятней):
    
    ключ 'a' -> 8  <- 'a' встречается 8 раз
    ключ 'b' -> 6  <- 'b' встречается 6 раз
    ключ 'c' -> 3
    ключ 'd' -> 4
    
    И в случае, если в хипе есть три значения (8), (6), (3),
    по факту обработки массива слева направо мы дошли до (4) и положили его в хип.
    Минимальным значением в хипе стал (3) - и он оттуда удаляется.
    
    Возвращаясь обратно к числам:
    ключ 1 -> 8    <- единица встречается 8 раз
    ключ 2 -> 6    <- двойка 6 раз
    ключ 3 -> 3
    ключ 4 -> 4
    
    Причём тут интересно только то, что будет лежать конкретно в хипе.
    
    Если изначально у нас там лежали просто значения, то теперь там будут лежать
    вхождения в мапу, которые выглядят вот так:
    Map.Entry<Integer, Integer>
    
    Для символов строки было бы очевидно:
    Map.Entry<Character, Integer>
    
    Теперь, чтобы пазл полностью сложился, не хватает лишь признака «лучшести»
    для хипа - то есть на базе чего хип будет выталкивать символ вверх.
    
    Так как у нас содержится пара значений, очевидно, выталкивать вверх
    будем по признаку getValue в мап‑ентри (как мы помним, сравнение
    делается через логику: отрицательный результат / равны / положительный
    результат в разнице):
    
    PriorityQueue<Map.Entry<Integer, Integer>> minHeap =
        new PriorityQueue<>((a, b) -> a.getValue() - b.getValue());
    
    Или более читаемый вариант (мы же сравниваем два Integer):
    new PriorityQueue<>(
        (a, b) -> Integer.compare(a.getValue(), b.getValue())
    );
    
    Всё. С этого момента задачи становятся по логике полностью одинаковыми.
    Единственное отличие - это предзаполнение мапы с частотами
    и использование для итераций её, а не оригинального массива (так как данные
    как раз переносятся в эту мапу).
    
    public class Solution {
        public int[] topKFrequent(int[] nums, int k) {
    
            // <- предзаполнение мапы с частотами
            Map<Integer, Integer> frequencyMap = new HashMap<>();
            for (int num : nums) {
                frequencyMap.put(num, frequencyMap.getOrDefault(num, 0) + 1);
            }
    
            // <- хип с признаком «лучшести» на базе getValue
            PriorityQueue<Map.Entry<Integer, Integer>> minHeap =
                new PriorityQueue<>((a, b) -> a.getValue() - b.getValue());
    
            // <- массив нам не нужен, нам нужен frequencyMap
            for (Map.Entry<Integer, Integer> entry : frequencyMap.entrySet()) {
    
                minHeap.offer(entry);
    
                if (minHeap.size() > k) {
                    minHeap.poll();
                }
            }
    
            // <- осталось получить последние (k) элементов:
            int[] result = new int[k];
            int index = 0;
            while (!minHeap.isEmpty()) {
                result[index++] = minHeap.poll().getKey();
            }
    
            return result;
        }
    }
    
    Сложность аналогичная k‑largest: O(n * log k),
    где (n) = длина массива,
    и (k) = параметр задачи.
    
    Каждая операция offer() и poll() в куче размера (k) стоит O(log k).
    Всего таких операций: O(n * log k).
    
    Единственное, что, как я написал выше, я рекомендую эту задачу решать
    с мыслью о том, что вы обрабатываете не числа, а символы в строке -
    то есть у вас на входе char[] nums. В этом случае запутаться будет куда
    сложнее с происходящим.
    
    И также, как и с k‑largest, мы можем, естественно, поместить все бакеты
    в массив, отсортировать сначала его и после чего взять просто три самых
    лучших из сортировки. Работать будет быстрее - так как избавимся
    от потребности каждый раз перестраивать кучу.

* */
public class TopKElementsOrMinMaxHeap_TopKFrequentElements {
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

  static void main() {
    int[] arr = {1,1,1,2,2,2,3,3,4,4,4,5};
    var wrapper = new TopKElementsOrMinMaxHeap_TopKFrequentElements();
    var solution = wrapper.new Solution();
    var pureResult = solution.topKFrequent(arr, 3);
    var printResult = Arrays.stream(pureResult).boxed().toList();
    out(printResult); // ожидаем: 1,2,4 не упорядоченно
  }
}//c:TopKElementsOrMinMaxHeap_TopKFrequentElements

```
