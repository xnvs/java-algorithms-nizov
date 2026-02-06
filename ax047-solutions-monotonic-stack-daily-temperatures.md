[Оглавление](README.md)

Решебник MonotonicStack_DailyTemperatures. Wording - первая часть.md
===
помимо задачи мы здесь смотрим плохой вординг
(условия задачи)

```java
package ru.pragm;

import static ru.pragm.utils.Out.*;

import java.util.ArrayDeque;
import java.util.Deque;
import java.util.Stack;

/*
Задача:
MonotonicStack
DailyTemperatures
---------------------------------    
    Условие: дан массив температур temperatures.
    Для каждого дня нужно найти количество дней до более тёплой температуры.
    Если такой температуры нет, вернуть 0.
    
    Это решение по сути расширяет задачу про NGE,
    но с некоторыми нюансами.
    
    Во‑первых, тут ищется не просто NGE, а разница в днях.
    То есть мы не можем в стеке просто хранить значение - нам
    нужно вычислять расстояние между днями. Для этого есть два варианта:
    
    1. Хранить индексы. После того как нужное NGE будет найдено,
       взять расстояние между NGE и текущим элементом -
       и получить решение.
    2. Хранить в стеке два элемента: индекс и значение.
    
    Обычно для простоты используют вариант (1).
    Однако тут есть нюанс, связанный с производительностью:
    нам придётся раз за разом, оперируя значениями,
    читать их из памяти по индексу массива.
    (Что, кстати, происходит не так медленно и оптимизации, скорее, не требует.)
    
    Это и порождает первое решение - «справа налево».
    Оно полностью понятное: повторяет алгоритм NGE,
    легко читается и механически воспроизводится в голове.
    
    Но классическое решение выглядит как «слева направо».
    Давайте о нём поговорим.
    
    Как вы знаете, в языке любую фразу можно сказать
    в активном залоге: «Я тебя люблю».
    И в пассивном: «Любовь обуяла мной в отношении тебя».
    
    Технически это одно и то же.
    Но в первом случае фраза логична и понятна,
    а во втором - содержит искажённую логику, не очень адекватную для восприятия.
    
    Здесь то же самое:
    - если «справа налево» - логика адекватная:
      вот NGE, вот яма, вот мы её пропускаем;
    - то решение «слева направо» выглядит немного чудным:
    
      «Будучи элементом, ожидающим обнаружения большего значения
      в последующей последовательности, я помещаюсь в структуру данных
      для отложенного разрешения».
    
    Это даже на обычном языке читать не очень понятно,
    но это КОРРЕКТНО.
    
    Или в привязке именно к температурам:
    
    Активный залог:
    «У меня температура 30. Дальше 28 - холоднее.
    Но у 28 уже посчитано, что через 2 дня будет 32.
    Значит, мне тоже через 3 дня будет 32».
    
    Пассивный залог:
    «Я, день с температурой 30, помещаю себя в стек ожидания.
    Когда придёт день с температурой 32,
    он посмотрит в стек и скажет: „Я теплее тебя, вот тебе расстояние“».
    
    Более того, несмотря на контринтуитивность, у этого подхода
    есть практическое преимущество:
    когда мы находим «тёплый день», мы можем сразу обновить результаты
    для ВСЕХ «ожидающих» холодных дней в стеке.
    Это создаёт эффект «каскадного обновления», который в некоторых сценариях
    может быть эффективнее.
    
    Вернёмся к залогам:
    пассивный залог без мышления в терминах активного
    очень тяжело создать как первый вариант.
    Однако он исключает потребность постоянного чтения из массива,
    как происходит в варианте «справа налево»
    (что, опять же, по сути проблемой не является).
    
    Моё мнение: вариант «слева направо» понятно как создан,
    но понять его - именно в плане осознания - достаточно проблематично.
    Мозгу тяжело оперировать тем, у чего нет аналогии в реальном мире.
    
    Из аналогий в задачах это очень похоже на вторую часть алгоритма Флойда
    для вычисления петель в графах:
    - первая часть понятна и легка, так как легко понять,
      что если один бегун движется со скоростью 2 км/ч,
      а второй - 1 км/ч, то первый приближается к нему по кругу
      со скоростью 1 км/ч и в итоге догонит;
    - вторая часть алгоритма Флойда представляет собой математическую игру,
      в результате которой: «откати одного бегуна на начало пути,
      а второй пусть движется дальше, но со скоростью 1 клетка за раз -
      будут двигаться оба, и в итоге они встретятся в начале петли».
    
    Аналогию представить крайне трудно.
    То есть понятно, откуда это берётся,
    но очень тяжело визуализировать.
    
    И это НОРМАЛЬНО. Не потому, что с вами чтото не так и вы не можете её понять,
    а просто так задача устроена.
    
    Здесь всё так же.
* */
public class MonotonicStack_DailyTemperatures {

  // справа налево
  public class SolutionRightToLeft {
    public int[] dailyTemperatures(int[] temps) {
      int n = temps.length;
      int[] res = new int[n];
      Deque<Integer> stack = new ArrayDeque<>();

      for (int i = n-1; i >= 0; i--) {
        while (!stack.isEmpty() && temps[stack.peek()] <= temps[i]) {
          stack.pop();
        }
        res[i] = stack.isEmpty() ? 0 : stack.peek() - i;
        stack.push(i);
      }
      return res;
    }
  }

  // слево направо
  public class SolutionLeftToRight {
    public int[] dailyTemperatures(int[] temperatures) {
      int n = temperatures.length;
      int[] result = new int[n];
      Stack<Integer> stack = new Stack<>();
      for (int i = 0; i < n; i++) {
        int currentTemp = temperatures[i];
        while (!stack.isEmpty() && currentTemp > temperatures[stack.peek()]) {
          int prevDayIndex = stack.pop();
          result[prevDayIndex] = i - prevDayIndex;
        }
        stack.push(i);
      }
      return result;
    }
  }

  static void main() {
    var wrapper = new MonotonicStack_DailyTemperatures();
    var solutionLeftToRight = wrapper.new SolutionLeftToRight();
    var solutionRightToLeft = wrapper.new SolutionRightToLeft();

    // Тест 1: Базовый случай
    int[] temps1 = {73, 74, 75, 71, 69, 72, 76, 73};
    int[] expected1 = {1, 1, 4, 2, 1, 1, 0, 0};

    int[] resultLTR1 = solutionLeftToRight.dailyTemperatures(temps1);
    int[] resultRTL1 = solutionRightToLeft.dailyTemperatures(temps1);

    out("Test 1 - Basic case:");
    out("Expected: " + java.util.Arrays.toString(expected1));
    out("Left-to-Right: " + java.util.Arrays.toString(resultLTR1));
    out("Right-to-Left: " + java.util.Arrays.toString(resultRTL1));
    out("LTR passed: " + java.util.Arrays.equals(expected1, resultLTR1));
    out("RTL passed: " + java.util.Arrays.equals(expected1, resultRTL1));
    out("");

    // Тест 2: Температура только растёт
    int[] temps2 = {30, 40, 50, 60};
    int[] expected2 = {1, 1, 1, 0};

    int[] resultLTR2 = solutionLeftToRight.dailyTemperatures(temps2);
    int[] resultRTL2 = solutionRightToLeft.dailyTemperatures(temps2);

    out("Test 2 - Increasing temperatures:");
    out("Expected: " + java.util.Arrays.toString(expected2));
    out("LTR passed: " + java.util.Arrays.equals(expected2, resultLTR2));
    out("RTL passed: " + java.util.Arrays.equals(expected2, resultRTL2));
    out("");

    // Тест 3: Температура только падает (никогда не становится теплее)
    int[] temps3 = {90, 80, 70, 60};
    int[] expected3 = {0, 0, 0, 0};

    int[] resultLTR3 = solutionLeftToRight.dailyTemperatures(temps3);
    int[] resultRTL3 = solutionRightToLeft.dailyTemperatures(temps3);

    out("Test 3 - Decreasing temperatures:");
    out("Expected: " + java.util.Arrays.toString(expected3));
    out("LTR passed: " + java.util.Arrays.equals(expected3, resultLTR3));
    out("RTL passed: " + java.util.Arrays.equals(expected3, resultRTL3));
    out("");

    // Тест 4: Один элемент
    int[] temps4 = {100};
    int[] expected4 = {0};

    int[] resultLTR4 = solutionLeftToRight.dailyTemperatures(temps4);
    int[] resultRTL4 = solutionRightToLeft.dailyTemperatures(temps4);

    out("Test 4 - Single element:");
    out("Expected: " + java.util.Arrays.toString(expected4));
    out("LTR passed: " + java.util.Arrays.equals(expected4, resultLTR4));
    out("RTL passed: " + java.util.Arrays.equals(expected4, resultRTL4));
    out("");

    // Тест 5: Сложные ямы (пример из описания NGE)
    int[] temps5 = {30, 40, 30, 30, 30, 50};
    int[] expected5 = {1, 4, 3, 2, 1, 0};

    int[] resultLTR5 = solutionLeftToRight.dailyTemperatures(temps5);
    int[] resultRTL5 = solutionRightToLeft.dailyTemperatures(temps5);

    out("Test 5 - Complex valleys:");
    out("Expected: " + java.util.Arrays.toString(expected5));
    out("LTR passed: " + java.util.Arrays.equals(expected5, resultLTR5));
    out("RTL passed: " + java.util.Arrays.equals(expected5, resultRTL5));
    out("");

    // Сравнение производительности
    int[] largeTemps = new int[10000];
    java.util.Random random = new java.util.Random();
    for (int i = 0; i < largeTemps.length; i++) {
      largeTemps[i] = random.nextInt(100) - 50; // от -50 до +50
    }

    long start = System.currentTimeMillis();
    solutionLeftToRight.dailyTemperatures(largeTemps);
    long endLTR = System.currentTimeMillis();

    solutionRightToLeft.dailyTemperatures(largeTemps);
    long endRTL = System.currentTimeMillis();

    out("Performance test (10000 elements):");
    out("Left-to-Right time: " + (endLTR - start) + "ms");
    out("Right-to-Left time: " + (endRTL - endLTR) + "ms");
  }
}//c:MonotonicStack_DailyTemperatures

```