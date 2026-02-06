[Оглавление](README.md)

Решебник. LinkedListReversal_ReverseLinkedListB
===
```java
package ru.pragm;

import static ru.pragm.utils.Out.out;

/*
Задача:
LinkedListReversal
ReverseLinkedListB
---------------------------------
Условие: развернуть часть односвязного списка от позиции left
до позиции right (1‑индексация).

    Эта задача не самостоятельная, а представляет собой усложнение
    задачи про простой разворот линкд‑листа
    (желательно её смотреть сразу за ней).
    
    public class Solution {
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
    
    Здесь интересно другое: как, изменяя код имеющегося решения,
    заставить его работать под новые условия.
    
    Проблем, появляющихся там, на самом деле несколько:
    1. Промотать начальные ноды до момента выхода на left‑часть.
    2. Контролировать null или правую часть при развороте.
    3. Поскольку разворот происходит сегмента (будем называть его сегментом),
       нам надо будет его подсоединить к нодам оригинального листа.
    
    Основная проблема тут именно такая:
    
    (1) -> (2) -> (3) -> (4) -> (5)
    
    Это оригинальный лист. Мы начинаем разворот нод
    начиная с (2) и заканчивая (4).
    
    В итоге мы должны получить:
    
    (1) -> (4) -> (3) -> (2) -> (5)
    
    Логика простая:
    - (1) мы просто пропускаем;
    - начинаем с (2).
    
    Сегмент (2)–(3)–(4) разворачивается по правилам
    разворота целиком листа. Во‑первых, произойдёт следующее:
    связь с (1) будет утеряна, и нам надо её сохранить -
    чтобы было куда присоединять.
    
    Нам надо сохранять нод, после которого мы будем
    делать разворот, чтобы эффективно к нему потом
    подсоединить новый head развёрнутого листа.
    
    Но это не всё. Возможен такой сценарий:
    нам надо развернуть сегмент (1)–(2)–(3)–(4).
    
    ? -> (1) -> (2) -> (3) -> (4) -> (5)
    
    Тут, как видим, сохранять нечего. Есть два варианта:
    - принципиально обрабатывать эту ситуацию
      (после разворота развёрнутый хвост станет head для всего листа);
    - ввести dummy head, который присоединить к листу слева,
      и начинать обработку с его наличием.
      Результат, который мы будем возвращать, при этом
      всегда будет dummyHead.next.
    
    (dummyHead) -> (1) -> (2) -> (3) -> (4) -> (5)
    
    Этот вариант обычно и используется.
    
    Последний момент - присоединение (2) к (5):
    
    (1) -> (4) -> (3) -> (2) -> (5)
    
    По сути, у нас получается из модификаций:
    1. Введение dummyHead, чтобы упростить код:
       он подсоединён слева, и возврат всегда стабильный - dummyHead.next.
    2. Промотка пропускаемого сегмента.
    3. Учёт логических sequenceHead / sequenceTail
       для переподсоединения хвостов с развёрнутым сиквенсом.
    4. Введение каунтера, чтобы разворот производился
       только на нужном участке.
    5. Пересоединение хвостов.
    6. Дополнительно можно ввести тест на корректность индексов,
       но мы этого не будем делать, так как к сути задачи это не относится.
    
    Избавляемся от while и делаем решение:
    
    public ListNode reverseBetween(ListNode head, int left, int right) {
    
      // <- быстрый возврат
      if (left == right) {
        return head;
      }
    
      // <- Создаём dummy node для упрощения обработки случая left = 1
      ListNode dummy = new ListNode(0);
      dummy.next = head;
    
      // <- Находим node перед left (prevNode)
      ListNode prevNode = dummy;
      for (int i = 1; i < left; i++) {
        prevNode = prevNode.next;
      }
    
      // <- Инициализация указателей для разворота
      ListNode current = prevNode.next;
      ListNode prev = null;
      ListNode nextTemp = null;
    
      // <- Разворачиваем часть списка от left до right
      for (int i = left; i <= right; i++) {
        nextTemp = current.next;
        current.next = prev;
        prev = current;
        current = nextTemp;
      }
    
      // <- Соединяем развёрнутую часть с остальным списком
      //    prevNode.next теперь должен указывать на новый head развёрнутой части
      //    конец развёрнутой части должен указывать на current (оставшийся список)
      //    для читабельности можно ввести две отдельные переменные,
      //    которые будут пересоединяться, но мы попытаемся воспользоваться тем,
      //    что у нас уже есть
      prevNode.next.next = current;     // <- конец развёрнутой части
      prevNode.next = prev;             // <- начало развёрнутой части
    
      return dummy.next;
    }
    
    вариант с вынесением хвостов пересоединения в отдельные переменные 
    выглядит значительно проще и читабельнее. однако в классических решениях
    обычно встречаются конструкции типа .next.next. если вы будете
    программировать самостоятельно, я крайне настоятельно рекомендую называть
    переменные так, чтобы всё было понятно, - и не стесняться вводить
    дополнительные промежуточные переменные, особенно если они являются
    логической частью решения.
    
    public ListNode reverseBetween(ListNode head, int left, int right) {
        if (left == right) return head;
    
        ListNode dummy = new ListNode(0);
        dummy.next = head;
    
        // 1. находим узел ПЕРЕД подсписком
        ListNode beforeReverse = dummy;
        for (int i = 1; i < left; i++) {
            beforeReverse = beforeReverse.next;
        }
    
        // 2. находим начало подсписка (станет концом после разворота)
        ListNode startReverse = beforeReverse.next;
    
        // 3. разворачиваем подсписок
        ListNode prev = null;
        ListNode current = startReverse;
        ListNode next = null;
    
        for (int i = left; i <= right; i++) {
            next = current.next;
            current.next = prev;
            prev = current;
            current = next;
        }
    
        // 4. явные переменные для переподключения
        ListNode reversedHead = prev;         // новая голова развернутого подсписка
        ListNode reversedTail = startReverse; // старый первый узел (теперь хвост)
        ListNode afterReverse = current;      // узел после подсписка
    
        // 5. переподключение (гораздо понятнее!)
        beforeReverse.next = reversedHead;  // часть ДО → голова развернутого
        reversedTail.next = afterReverse;   // хвост развернутого → часть ПОСЛЕ
    
        return dummy.next;
    }
    
    на выходе всё то же:
    время: O(n) - один проход по списку
    память: O(1) - дополнительная память константная

* */
public class LinkedListReversal_ReverseLinkedListB {
  class ListNode {
      int val;
      ListNode next;
      ListNode() {}
      ListNode(int val) { this.val = val; }
      ListNode(int val, ListNode next) { this.val = val; this.next = next; }
  }

  public class Solution {
    public ListNode reverseBetween(ListNode head, int left, int right) {
      if (left == right) {
        return head;
      }
      ListNode dummy = new ListNode(0);
      dummy.next = head;
      ListNode prevNode = dummy;
      for (int i = 1; i < left; i++) {
        prevNode = prevNode.next;
      }
      ListNode current = prevNode.next;
      ListNode prev = null;
      ListNode nextTemp = null;
      for (int i = left; i <= right; i++) {
        nextTemp = current.next;
        current.next = prev;
        prev = current;
        current = nextTemp;
      }
      prevNode.next.next = current;
      prevNode.next = prev;
      return dummy.next;
    }
  }

  void printList(ListNode head){

    out("распечатываем:");
    out("{");
    while(head != null) {
      out(head.val);
      head = head.next;
      if(head != null) out(",");
    }
    out("}");
  }

  static void main() {
    var wrapper = new LinkedListReversal_ReverseLinkedListB();
    var solution = wrapper.new Solution();

    var z = wrapper.new ListNode(700, null);
    var y = wrapper.new ListNode(600, z);
    var x = wrapper.new ListNode(500, y);
    var w = wrapper.new ListNode(400, x);
    var v = wrapper.new ListNode(300, w);
    var u = wrapper.new ListNode(200, v);
    var t = wrapper.new ListNode(100, u);

    wrapper.printList(t);

    solution.reverseBetween(t, 3,5);
    wrapper.printList(t);
  }

}//c:LinkedListReversal_ReverseLinkedListB

```