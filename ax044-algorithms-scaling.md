| [Назад](ax043-algorithms-simulation.md) | [Оглавление](README.md) | [Вперёд](ax045-algorithms-permutations-combinations-probability-monte-carlo.md) |

Приложение: Масштабирование алгоритмических решений
===

Очень часто на собеседованиях, после того как вы решаете задачу, вас будут
спрашивать: как масштабировать ваше решение?

На самом деле здесь нет ничего сложного: масштабируется не решение,
а его компоненты.

Отдельный компонент извлекается из алгоритма и становится чем-то вроде
класса, метода или лямбды. После этого сборка алгоритма происходит
не из конкретных частей, а из массивов произвольных объектов
(классов или лямбд).

Это легко продемонстрировать на примере всем известной задачи про FizzBuzz.
Условия задачи звучат так:

Для чисел от 1 до 100:
- если число делится на 3 - вывести «Fizz»;
- если число делится на 5 - вывести «Buzz»;
- если делится и на 3, и на 5 - вывести «FizzBuzz»;
- иначе - вывести само число.

Задача примитивна и проверяет базовые навыки программирования. Её скрытая
механика в том, что нужно начинать решение с наиболее общего случая
и постепенно переходить к менее общим. Например:

```java
if (i % 15 == 0) "FizzBuzz"
else if (i % 3 == 0) "Fizz"
else if (i % 5 == 0) "Buzz"
else i.toString()
```

Если перепутать порядок и поставить условие `if (i % 3 == 0) "Fizz"`
выше условия `if (i % 15 == 0) "FizzBuzz"`, решение даст неверный результат.

Но нас это интересует меньше всего. Главное - понять, как это масштабировать.

Всё просто: компонентом здесь будет правило. В решении их три,
и каждое соответствует своей ветке if/else.

Эти правила можно представить:
- в виде метода (менее гибко);
- в виде объекта (более декларативно - выберем этот путь);
- в виде лямбды (это будет вашим упражнением).

В итоге код из четырёх строк приобретёт «энтерпрайз-флейв»,
но станет масштабируемым и поддающимся усложнению в любом направлении.

Может показаться, что это абсурд, но взгляните внимательно:
полученное решение - полное, а изначальный алгоритм - лишь его частный случай:

```java
if (i % 15 == 0) "FizzBuzz"
else if (i % 3 == 0) "Fizz"
else if (i % 5 == 0) "Buzz"
else i.toString()
```

Замечу: даже в этом примере масштабирование выполнено не полностью.
Для правил следовало бы ввести сервис-менеджер, а мы добавляем их вручную.
Кроме того, мы опираемся на неявную логику структуры данных,
что с архитектурной точки зрения неидеально.

Тем не менее в целом решение уже выглядит масштабируемым.


```java
package ...;

import java.util.ArrayList;
import java.util.function.Predicate;

import static java.lang.System.*;

interface Rule<R, T> {

  R getValue();

  Predicate<T> getRule();

  default boolean isDefaultRule() {

    return false;
  }

}

class DefaultRule implements Rule<String, Integer> {

  private final String DEFAULT_VALUE = "default";

  @Override
  public String getValue() {

    return DEFAULT_VALUE;
  }

  @Override
  public Predicate<Integer> getRule() {

    return (_) -> {
      return true;
    };
  }

  @Override
  public boolean isDefaultRule() {

    return true;
  }

}

class FuzzBuzzRule implements Rule<String, Integer> {

  @Override
  public String getValue() {

    return "FuzzBuzz";
  }

  @Override
  public Predicate<Integer> getRule() {

    return (value) -> {
      if ((value % 3) == 0 && (value % 5) == 0)
        return true;
      return false;
    };
  }

}

class FuzzRule implements Rule<String, Integer> {

  @Override
  public String getValue() {

    return "Fuzz";
  }

  @Override
  public Predicate<Integer> getRule() {

    return (value) -> {
      if ((value % 3) == 0)
        return true;
      return false;
    };
  }

}

class BuzzRule implements Rule<String, Integer> {

  @Override
  public String getValue() {

    return "Buzz";
  }

  @Override
  public Predicate<Integer> getRule() {

    return (value) -> {
      if ((value % 5) == 0)
        return true;
      return false;
    };
  }

}

public class x02_FizzBuzz {
  static void main() {

    var testValues = new Integer[]{15,
                                   3,
                                   5,
                                   7};

    // <- сначала сложные условия потом простые
    //    ArrayList сам контролирует порядок
    //    вообще по хорошему должно быть внутри RuleManager-а
    //
    ArrayList<Rule<String, Integer>> rules = new ArrayList<>();
    rules.add(new FuzzBuzzRule());
    rules.add(new FuzzRule());
    rules.add(new BuzzRule());
    // rules.add(new DefaultRule());   // <- иной дифолт

    for (Integer value : testValues) {
      String result = rules.stream()
                           .filter(rule -> rule.getRule()
                                               .test(value))
                           .findFirst()
                           .map(Rule::getValue)
                           .orElse(value.toString());
      out.println(result);
    }
  }
}

```

| [Назад](ax043-algorithms-simulation.md) | [Оглавление](README.md) | [Вперёд](ax045-algorithms-permutations-combinations-probability-monte-carlo.md) |
