[Оглавление](README.md)

Решебник. LinkedListReversal_SwapNodesInPairs
===
```java
package ru.pragm;

import static ru.pragm.utils.Out.out;

/*
Задача:
LinkedListReversal
SwapNodesInPairs
---------------------------------
    Условие: дан односвязный список. Нужно поменять местами каждые два соседних узла
    и вернуть изменённый список.
    
    Эта задача - сильное упрощение задачи reverseLinkedList. Её структура позволяет
    не использовать циклы и выполнить все операции вручную.
    
    Решение простое и понятное: нужно двигаться по списку, обрабатывая по два элемента
    за раз (current = current.next.next), и вручную менять узлы местами. При этом
    важно корректно их связывать - не только друг с другом, но и с «хвостами».
    
    По той же причине, что и в задаче ReverseLinkedList, вводится dummy node. Она нужна,
    чтобы не отслеживать отдельно кейс, когда слева ничего нет - присоединение узлов
    будет учитывать это автоматически.
    
    Чтобы понять, как производится линковка, представьте такую схему:
    
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
    
    Важно чётко указывать, на что какой узел ссылается. Это поможет потом перенести логику
    в код, обезличив её. Если этого не делать, есть два варианта:
    - либо вы очень умный, и у вас всё получится;
    - либо вы запутаетесь, получите ошибки и будете мучиться, пытаясь понять,
      куда что должно вести и откуда берутся конструкции вроде second = current.next.next.
    
    Второй важный аспект - адекватные названия переменных. В приведённом ниже коде
    они названы не слишком понятно - это дань классическим решениям, которые вы встретите
    в других источниках. Но в своём коде вы вполне можете использовать длинные,
    читаемые имена переменных.
    
    public class Solution {
        public ListNode swapPairs(ListNode head) {
            ListNode dummy = new ListNode(0);
            dummy.next = head;
            ListNode current = dummy;
    
            while (current.next != null && current.next.next != null) {
    
                // <- Сохраняем ссылки на два узла для обмена
                ListNode first = current.next;
                ListNode second = current.next.next;
    
                // <- (1) Первый узел указывает на то, что было после второго
                first.next = second.next;
    
                // <- (2) Второй узел указывает на первый
                second.next = first;
    
                // <- (3) Предыдущий узел указывает на второй (новый первый)
                current.next = second;
    
                current = current.next.next;
            }
    
            return dummy.next;
        }
    }

* */
public class LinkedListReversal_SwapNodesInPairs {
  class ListNode {
    int val;
    ListNode next;
    ListNode() {}
    ListNode(int val) { this.val = val; }
    ListNode(int val, ListNode next) { this.val = val; this.next = next; }
  }

  public class Solution {
    public ListNode swapPairs(ListNode head) {
      ListNode dummy = new ListNode(0);
      dummy.next = head;
      ListNode current = dummy;
      while (current.next != null && current.next.next != null) {
        ListNode first = current.next;
        ListNode second = current.next.next;
        first.next = second.next;
        second.next = first;
        current.next = second;
        current = current.next.next;
      }
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
    var wrapper = new LinkedListReversal_SwapNodesInPairs();
    var solution = wrapper.new Solution();

    var z = wrapper.new ListNode(700, null);
    var y = wrapper.new ListNode(600, z);
    var x = wrapper.new ListNode(500, y);
    var w = wrapper.new ListNode(400, x);
    var v = wrapper.new ListNode(300, w);
    var u = wrapper.new ListNode(200, v);

    var t = solution.swapPairs(u);
    wrapper.printList(t);
  }

}//c:LinkedListReversal_SwapNodesInPairs

```