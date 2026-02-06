[Оглавление](README.md)

LinkedListReversal_ReverseLinkedListA
===
```java
package ru.pragm;

import static ru.pragm.utils.Out.*;
/*
Задача:
LinkedListReversal
ReverseLinkedListA
---------------------------------
    Условие: дан односвязный список.
    Развернуть список и вернуть новый head.
    
    При том, что вид ноды следующий:
    
    public class ListNode {
        int val;
        ListNode next;
        ListNode() {}
        ListNode(int val) { this.val = val; }
        ListNode(int val, ListNode next) { this.val = val; this.next = next; }
    }

(1) Формализация задачи и её суть

    Задача максимально понятна.
    По сути, это задача на смену соединений.
    Там, где от головы соединения идут в сторону направления движения листа,
    мы будем двигаться по одной ноде за раз и менять их так,
    чтобы next указывал в ту сторону, откуда мы идём.
    
    Когда мы дойдём до хвоста - это и будет новый head,
    который мы и вернём.
    
    По сути:
    
    Было:
    
    nodeA{
        next = nodeB
    }
    nodeB{
        next = nodeC
    }
    nodeC{
        next = null   <- хвост
    }
    
    Мы находимся в nodeA:
    1. Сохраняем значение соединения, чтобы не потерять nodeB.
    2. next устанавливаем в null.
    3. Двигаемся в сторону сохранённой nodeB.
    
    Мы находимся в nodeB:
    1. Сохраняем значение nodeC, чтобы не потерять.
    2. next устанавливаем, указывающим на nodeA.
    
    И так далее. В итоге, когда доходим до хвоста, его и возвращаем.
    
    Стало:
    
    nodeA{
        next = null
    }
    nodeB{
        next = nodeA
    }
    nodeC{
        next = nodeB   <- теперь голова
    }
    
    Заметьте: нам потребуется учёт одной ноды - previousNode в виде переменной.
    Чтобы, когда, например, мы сместились на nodeB, было откуда взять nodeA.
    Это принципиальный момент, так как список указывает только вперёд,
    и двигаться по нему мы будем, меняя ссылки от nodeA в сторону nodeC.

(2) Идея решения

    Отчего рождается такое движение?
    Очень просто: список однонаправленный.
    То есть мы не можем просто создать новый head
    и двигаться от конца к началу - так как, находясь в конечном ноде,
    всё, что мы видим, это лишь null в качестве следующего указателя.
    
    Напомню:
    
    public class ListNode {
        int val;
        ListNode next;
        ListNode() {}
        ListNode(int val) { this.val = val; }
        ListNode(int val, ListNode next) { this.val = val; this.next = next; }
    }
    
    Отчего создание новых переменных в виде temp и последующая перемена указателей -
    единственный вариант, который нам в принципе остаётся.
    
    По сути, у нас будет:
    1. while-цикл, в котором мы будем идти до конца списка.
    2. Три рабочих указателя: prev/current/nextTemp.
    3. Разворот связи: current.next = prev.
    4. Сдвиг указателей для движения дальше через temp:
       prev = current / current = nextTemp
       (это и есть причина, по которой нам temp нужен).
    5. Возврат нового head, который будет представлен последним в списке.
    
    Решение по сути O(n).

(3) Порядок кодирования

    Кодируем цикл, затем сохранение temp, после чего делаем замену связей.
    Решение очень примитивное - иначе сделать из‑за того, что список
    не двусвязный, не выйдет. Был бы двусвязный - мы бы просто задали
    новый head и, двигаясь по списку, меняли бы next/prev местами.
    
    На самом деле, здесь по опыту могу сказать, что реальная боль возникает
    в оперировании названиями prev/next/current,
    так как во время цикла они становятся относительными друг к другу.
    Их проще изобразить сначала в виде того, что пришло:
    
    while(current != null){
    
        current = nodeB
    
        Было:
        nodeB{
            next = nodeC
        }
    
        Стало:
        nodeB{
            next = nodeA
        }
    
    }
    
    И потом пытаться менять указатели.
    В примере ниже это всё ещё и усугубляется тем, что prev/current вынесены
    вне цикла. То есть обязательно сначала нарисуйте A/B/C, при условии,
    что в цикле current - это (B), и потом пытайтесь это скодировать,
    иначе запутаетесь обязательно.
    
    Это относится к программированию любых списков,
    так как ваш мозг просто не вывозит то, что происходит в цикле,
    и легко оперирует конкретными объектами.
    После чего эти операции очень легко перенести уже в цикл - по одной операции.
    
    Второй вариант - убрать когнитивный шум, нормально назвав переменные:
    
    public ListNode reverseList(ListNode head) {
        ListNode previousNode = null;                       // <- для организации связи
                                                            //    мы же сдвинемся от неё
                                                            //    внутри цикла
        ListNode currentNode = head;                        // <- то, чем оперируем
    
        while (currentNode != null) {
            ListNode nextNodeToProcess = currentNode.next;  // <- ЧТО сохраняем
            currentNode.next = previousNode;                // <- ЧТО меняем
            previousNode = currentNode;                     // <- КУДА двигаем previous
            currentNode = nextNodeToProcess;                // <- КУДА двигаем current
        }
    
        return previousNode;
    }    
 */
public class LinkedListReversal_ReverseLinkedListA {
  public class ListNode {
    int val;
    ListNode next;
    ListNode() {}
    ListNode(int val) { this.val = val; }
    ListNode(int val, ListNode next) { this.val = val; this.next = next; }
  }

  public class Solution {
    public ListNode reverseList(ListNode head) {
      ListNode prev = null;
      ListNode current = head;
      while (current != null) {
        ListNode nextTemp = current.next;     // 1. сохраняем current.next
        current.next = prev;                  // 2. следующая стала предыдущей
        prev = current;                       // 3. а предыдущая текущей
        current = nextTemp;                   // 4. двигаемся к следующей
      }
      return prev;
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
    var wrapper = new LinkedListReversal_ReverseLinkedListA();
    var solution = wrapper.new Solution();

    var end = wrapper.new ListNode(300, null);
    var middle = wrapper.new ListNode(200, end);
    var head = wrapper.new ListNode(100, middle);

    wrapper.printList(head);

    var newHead = solution.reverseList(head);

    wrapper.printList(newHead);
  }

}//c:LinkedListReversal_ReverseLinkedListA

```