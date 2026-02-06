[Оглавление](README.md)

Решебник. SlidingWindow_MinimumWindowSubstring
===
```java
package ru.pragm;

import java.util.HashMap;
import java.util.Map;
import static ru.pragm.utils.Out.*;

/*
Задача:
SlidingWindow
MinimumWindowSubstring
---------------------------------
    Условие: даны строки s и t.
    Нужно найти минимальную подстроку в s,
    которая содержит все символы строки t (включая дубликаты).

(1) Формализация задачи и её суть

    Если посмотреть на задачу логически, то вот что от нас хотят:
    есть строка
    
          abxxxxcxxabc
    
    Мы ищем подстроку, в которой будет «abc»,
    при этом такую, где общее количество символов минимально.
    
    Прогресс на нашем примере будет таким:
        
          [abxxxxc]xxabc
          a[bxxxxcxxa]bc
          abxxxx[cxxa]bc
          abxxxxcxx[abc]    <- и вот мы наткнулись на самую короткую

    Здесь визуально видно работу с:
    1) окном;
    2) левым и правым указателями.
    
    Суть решения:
    1) как-то формировать окно;
    2) контролировать движение левого и правого указателей.
    
    При этом движение правого указателя понятно -
    он просто двигается вперёд посимвольно.
    
    Значит, нужно решить, как будет двигаться левый указатель.
    Кроме того, задача не просто о вхождениях, а о частотных вхождениях.
    Нужно придумать, как контролировать вхождения для строк вроде

          aabccc

    где первого символа два, второго - один, третьего - три.
    На этом сложность задачи исчезает. Давайте разбирать.

(2) Идея решения

    Примитивное решение «в лоб», через брутфорс:
    двигаться одним указателем (в контексте окна - только правым),
    затем идти вперёд и проверять нахождение каждого из символов,
    раз за разом выполняя одну и ту же работу.
    
    Решение на базе окна возникает логично:
    нужно подтаскивать левую часть окна за правой,
    чтобы избавляться от мусорных символов.

        axxxxxxbcda
         ------ мусорные символы которые двигаясь вперед правым указателем
                 нам желательно просто пропускать

    Когда начинать убирать мусорные символы?
    Как только сдвинулись вперёд правым указателем
    и нашли корректную комбинацию:
    
            [axxxxxxbc]da  <- окно было
            a[xxxxxxbcda]  <- (а) вылетела - окно стало
            axxxxxx[bcda]  <- надо привести к такому удалив все мусорные символы
    
    Общая логика движения:
    - если представление окна совпадает - двигаем левый указатель;
    - если не совпадает - двигаем правый указатель
      (так как совпадения уже нет, и одной буквы не хватает для оптимизации).
    
    Движение правого указателя - основное
    (верхний цикл до момента, пока не дойдём до конца строки).
    Движение левого - вспомогательное
    (до момента, пока не будут удалены все мусорные символы
    или строка не будет соответствовать - тогда надо двигать правый указатель).
    
    С решением разобрались. Теперь нужно понять,
    как представлять окно, чтобы движение было возможным.
    
    Условие выбора движения левым или правым указателем:
    если содержимое окна не соответствует частотности символов строки t,
    мы переключаемся с движения левым указателем на движение правым.
    
    Есть три очевидных варианта:
    
    1. Держать окно как логический компонент
       и каждый раз проверять его содержимое
       на наличие всех символов из строки t.
       Это почти брутфорс, но в рамках окна.
    
    2. Завести частотную мапу + статистику количества совпадений
       для окна и для строки t - показывать,
       сколько вхождений у каждого символа.
       Но этого недостаточно: нужно также учитывать,
       сколько корректных символов совпало.
    
       В итоге будет:

           Map от (t) =>
                  'a' : 2
                  'b' : 1
                  'c' : 3

           Map от (window) =>
                  'a' : 2
                  'b' : 1
                  'c' : 2

       количествоСовпадений: 5, нужно 6,
       чтобы продолжать убирать левые символы.
    
       При движении нужно менять количество совпадений
       в мапе окна и общее количество совпадений,
       на основе этого принимать решения.
    
    3. Отказаться от мапы, так как у нас ASCII-символы,
       и завести обычный массив символов
       с дополнительной статистикой.
       Она покажет, сколько символов совпало на текущий момент,
       и поможет понять, нужно ли переключаться
       с движения левого указателя на движение правого.
    
       Очевидно, что counts['a'] сработает не хуже мапы
       (только для ASCII).
    
    4. Завести статистику в саму мапу,
       так как движение не требует сохранения начального состояния t.
       Контролировать движение точкой отсечки - например, нулём в значении.
    
       То есть будет:

           Map to rule them all => на начальном состоянии равная (t) мапе
              'a' => 2
              'b' => 1
              'c' => 3

           после чего: нашли 'a'
               'a' => 2-1
               'b' => 1
               'c' => 3

          снова нашли 'a'
                'a' => 1-1 = 0 если нуль - значит соответствие на уровне 'a'

    Итак, далее.
    
    Мне кажется, это решение не слишком адекватное, поскольку урезает абстракции,
    выворачивает наизнанку логику. Но как оптимизация оно выглядит рабочим.
    
    Предлагаю кодировать второй вариант - с двумя мапами.
    
    Итак, что у нас будет:
    1. Окно.
    2. Левый и правый указатель.
    3. Правило для движения левого указателя (поскольку правый просто движется линейно).
    4. Учёт статистики.
    
    Гипотетически можно вынести дополнительно правила движения и методами задать
    само движение. Причина - очевидный провал в логике: она будет смешиваться
    на разных уровнях. При этом решение на верхнем уровне выглядит совсем просто,
    а добавление кода приведёт к достаточно большому уродству.
    
    public String minWindow(String s, String t) {
        WindowState state = new WindowState(t);
        WindowTracker tracker = new WindowTracker(state);
    
        while (tracker.canExpandRight(s)) {
            tracker.expandRight(s);
    
            while (tracker.isValid()) {
                tracker.updateMinWindow();
                tracker.shrinkLeft(s);
            }
        }
    
        return tracker.getMinWindow(s);
    }

(3) Порядок кодирования.

    С кодированием здесь, как и со сценариями, всё понятно.
    
    Семпл: axxxxxxbcda.
    
    Выглядит вполне адекватно для тестов и проверки эдж кейсов.
    Можно было бы добавить, наверное, ещё проверку на один символ,
    но мы этого делать не будем - код и так обещает быть грязным.
    
    Итак, что мы будем кодировать и в каком порядке?
    
    Для начала потребуются две мапы и статистика.
    При этом мапу для (t) сразу надо задать частотностями.
    
    Далее потребуется окно и его характеристики. Поскольку при решении задач
    не принято вводить дополнительные классы, придётся сделать это просто переменными.
    
    Также нужно отслеживать статистику возврата:
    - минимальное значение длины;
    - индексы (в одном из вариантов).
    
    Поскольку минимальная длина уже есть, достаточно лишь наличия левого индекса
    для этой минимальной длины - чтобы извлечь итоговую строчку и вернуть её.
    
    Далее потребуется общий линейный цикл для движения правого указателя.
    В нём мы будем:
    - двигать правый указатель;
    - в случае совпадения всех символов сохранять статистику.
    
    Если окно содержит все символы, входим в логику движения левого указателя.
    Его будем двигать до тех пор, пока статистика окна совпадает со статистикой
    того, что есть в мапе (t).
    
    Когда это нарушится, отметим это, уменьшив переменную-каунтер совпадений.
    Таким образом, выйдем из движения левого указателя и снова переместимся
    в логику движения правого.
    
    В конце надо будет вернуть либо пустую строку, либо найденную подстроку
    на базе нашей статистики.
    
    В итоге получаем:
    
        Время: O(|s| + |t|) - каждый символ s посещается максимум дважды
        (правым и левым указателем).
        Память: O(|t|) или O(1), если использовать массив фиксированного размера.
    
    Поскольку решение достаточно комплексное, давайте сначала сделаем его с комментариями,
    а потом без. (В реальности, конечно, так программировать нельзя - тут антипаттерн
    на антипаттерне.)

        public class SlidingWindow_MinimumWindowSubstring {

          public class Solution {

            public String minWindow(String s,String t) {

              // <- карта для определения вхождений в (t) строке
              //    и её инициализация количеством (частотностью) таких вхождений
              //
              Map<Character, Integer> tMap = new HashMap<>();
              for (char c : t.toCharArray()) {
                tMap.put(c,tMap.getOrDefault(c,0) + 1);
              }

              // <- характеристики окна
              //
              Map<Character, Integer> windowMap = new HashMap<>();
              int leftWindowIndex = 0;
              int requiredCharsInWindow = tMap.size();
              int formedCharsInwindow = 0;

              // <- отслеживание статистики возврата
              //
              int minLeftIndexWhichFormResult = 0;
              int minResultLength = Integer.MAX_VALUE;

              // <- цикл для правого укзателя
              //
              for (int right = 0; right < s.length(); right++) {
                char rightChar = s.charAt(right);
                windowMap.put(rightChar, windowMap.getOrDefault(rightChar, 0) + 1);

                // <- найдено вхождение в (s) равное имеющемуся в (t)
                //
                if (
                    tMap.containsKey(rightChar) &&
                    windowMap.get(rightChar).intValue() == tMap.get(rightChar).intValue()
                ) {
                  formedCharsInwindow++;
                }

                // <- цикл для левого указателя, оно же сжатие окна
                //
                //    abxxxxcxxabc
                //   [-------]      <- было
                //         [----]   <- стало (сжались иксы)
                //           [----] <- потом стало
                //
                //
                while (leftWindowIndex <= right && formedCharsInwindow == requiredCharsInWindow) {

                  // <- текущая длина в окне
                  //    исходя из того что левый указатель двигается вправо
                  //    здесь сам указатель мы еще не сдвинули - это произойдет снизу
                  //    цикла while
                  //
                  int currentLength = right - leftWindowIndex + 1;

                  // <- текущая длина в окне меньше статистической минимальной
                  //    обновляем статистику и индекс который начинает строку с этой статистикой
                  //    для возврата - возврат будет
                  //    minLeftIndexWhichFormResult - индекс начала подстроки
                  //    (minLeftIndexWhichFormResult + minResultLength) - конец подстроки
                  //
                  if (currentLength < minResultLength) {
                    minResultLength = currentLength;
                    minLeftIndexWhichFormResult = leftWindowIndex;
                  }

                  // <- левый указатель после сдвига вправо
                  //
                  char leftChar = s.charAt(leftWindowIndex);
                  windowMap.put(leftChar, windowMap.get(leftChar) - 1);

                  // <- символ из окна выбыл formedCharsInWindow уменьшилось
                  //    это повлечет выход из while движения левого указателя
                  //    (не очевидная кривая логика, но какая есть)
                  //
                  if (tMap.containsKey(leftChar) && windowMap.get(leftChar) < tMap.get(leftChar)) {
                    formedCharsInwindow--;
                  }

                  // <- левый указатель двигается вправо
                  //
                  leftWindowIndex++;
                }
              }

              // <- возвращаем или пустую строку - или
              //    вычисленную на базе result статистики
              //
              return minResultLength == Integer.MAX_VALUE
                     ? ""
                     : s.substring(
                         minLeftIndexWhichFormResult,
                         minLeftIndexWhichFormResult + minResultLength);
            }

          }

* */
public class SlidingWindow_MinimumWindowSubstring {

  public class SolutionA {

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

      return minResultLength == Integer.MAX_VALUE
             ? ""
             : s.substring(
                 minLeftIndexWhichFormResult,
                 minLeftIndexWhichFormResult + minResultLength);
    }

  }

  public class SolutionB {
    public String minWindow(String s, String t) {
      int[] need = new int[128];
      for (char c : t.toCharArray()) need[c]++;

      int missing = t.length();
      int left = 0, right = 0;
      int minStart = 0, minLen = Integer.MAX_VALUE;

      while (right < s.length()) {
        if (need[s.charAt(right++)]-- > 0) missing--;

        while (missing == 0) {
          if (right - left < minLen) {
            minLen = right - left;
            minStart = left;
          }
          if (need[s.charAt(left++)]++ == 0) missing++;
        }
      }

      return minLen == Integer.MAX_VALUE ? "" : s.substring(minStart, minStart + minLen);
    }
  }

  static void main() {
    var outer = new SlidingWindow_MinimumWindowSubstring();
    var solutionA = outer.new SolutionA();
    var solutionB = outer.new SolutionB();

    out("Test 1: " + solutionA.minWindow("ADOBECODEBANC", "ABC")); // "BANC"
    out("Test 2: " + solutionA.minWindow("a", "a")); // "a"
    out("Test 3: " + solutionA.minWindow("a", "aa")); // ""
    out("Test 4: " + solutionA.minWindow("abxxxxcxxabc", "abc")); // "abc"

    out("Test 1: " + solutionB.minWindow("ADOBECODEBANC", "ABC"));
    out("Test 2: " + solutionB.minWindow("a", "a"));
    out("Test 3: " + solutionB.minWindow("a", "aa"));
    out("Test 4: " + solutionB.minWindow("abxxxxcxxabc", "abc"));
  }

}//c:SlidingWindow_MinimumWindowSubstring

```
