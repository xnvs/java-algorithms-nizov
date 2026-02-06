| [Назад](ax013-data-structures-graphs-intro.md) | [Оглавление](README.md) | [Вперёд](ax015-algorithms-table-driven-testing.md) |

x014. Структура данных: Префиксные деревья. Интро.
===

Что мы здесь рассмотрим и как это сделаем:
---

В этой главе мы рассмотрим:
1. как обычно представляют префиксные деревья (trie);
2. графическую демонстрацию префиксного дерева;
3. эскалацию данных на листьях в дереве, общее trie и деревья Меркла.

О префиксных деревьях - а точнее, об алгоритмах на базе префиксных деревьев -
мы, как и с графами, поговорим в отдельной главе. Основная идея такого
дерева напрямую перетекает в алгоритмы, которые такую структуру используют.

В этой главе дадим небольшое интро относительно данного типа структуры данных.
Она не самая сложная. Покажем не самые очевидные механики и логику, скрытую
за ними.

Начнём, как обычно, с академического определения.

Как обычно представляют префиксные деревья:
---

Я буду писать «префиксные деревья», потому что Trie выглядит как «Tree»,
а читается как «трай», создавая максимум возможностей для того, чтобы запутаться.

Хотя история с названием в реальности имеет некоторый логический смысл:
оно произошло от re-trie-val, что переводится как «поиск через последовательную
выборку откуда‑то».

Префиксное дерево - это дерево, специально предназначенное для эффективного
хранения и поиска подобий строк. Речь идёт о логически развивающейся
последовательности чего‑то однородного в плане смысла - например, текста
(чем, собственно, «строка» и является по сути). Но это может быть и пример
химических или математических формул, цепочек ДНК или чего‑то подобного.

Каждый узел представляет собой символ, а путь от корня к этому узлу -
саму строку, которую мы непосредственно пытаемся искать.

В префиксе могут располагаться как отдельные символы, так и подстроки.

Ключевые особенности префиксных деревьев:
- быстрый поиск для строк O(n), где n - длина строки;
- максимальная оптимизация для автодополнения строк и поиска по префиксам;
- возможность эффективно хранить словари.

Типичная задача - проверка орфографии.
---

Описание общепринятое и достаточно понятное. Как я и сказал, структура данных -
одна из самых простых и, так сказать, линейно понятных.

Но давайте поговорим о ней чуть больше.

Графическая демонстрация префиксного дерева
---

Префиксные деревья максимально графичны.

Если у вас остались хоть какие‑то вопросы о том, что это такое и почему
оно так работает, они исчезнут после небольшой демонстрации.

Их работу проще ПОКАЗАТЬ, чем ОПИСЫВАТЬ. Например, если у нас есть
две строки: «мама мыла раму» и «мылом рама мылась», и мы хотим использовать
буквы, а не слова, то получим следующее:

```text
    (корень)
    ├── "м"
    │   ├── "а" → "м" → "а" ("мама")
    │   └── "ы"
    │       ├── "л"
    │       │   ├── "а" ("мыла")
    │       │   └── "о" → "м" ("мылом")
    │       └── "л" → "а" → "с" → "ь" ("мылась")
    └── "р"
        ├── "а" → "м" → "у" ("раму")
        └── "а" → "м" → "а" ("рама")

```

Используются общие префиксы слов, а сами слова формируются в процессе движения по дереву.
Очевидно, что при добавлении новой фразы нужно сначала (1) найти уже существующую часть
до уровня, где она есть. Например, при добавлении «мама мыла раму гвоздём» мы сначала
найдём полное вхождение «мама мыла раму», а затем добавим «гвоздём» в виде новой строки -
сначала так, как это происходит в строке «мылась». Если потом будет добавляться
«мама мыла раму гвоздикой», то на уровне «гвозд-» произойдёт раздробление и появятся две строки.

Важно: мы физически отмечаем КОНЦЫ слов.

Допустим, у нас было дерево для:
1) мама мыла раму
2) рама мылась мылом

Теперь построим дерево для:
1) мама мыла раму
2) рама мылась мылом
3) мама мыла раму гвоздем


Здесь хорошо видно, что по окончанию предыдущего вхождения мы создаём новый УЗЕЛ,
в который помещаем остаток фразы.

```text
    (корень)
    ├── 'м'
    │   ├── 'а' → 'м' → 'а' (конец "мама")
    │   └── 'ы' → 'л'
    │       ├── 'а' (конец "мыла")
    │       ├── 'о' → 'м' (конец "мылом")
    │       └── 'а' → 'с' → 'ь' (конец "мылась")
    ├── 'р' → 'а' → 'м'
    │   ├── 'у' (конец "раму")
    │   └── 'а' (конец "рама")
    └── 'г' → 'в' → 'о' → 'з' → 'д' → 'е' → 'м' (конец "гвоздем")

```

и делаем последнюю модификацию:

дерево для:
1) мама мыла раму
2) рама мылась мылом
3) мама мыла раму гвоздем
4) мама мыла раму гвоздикой

```text
    (корень)
    ├── 'м'
    │   ├── 'а' → 'м' → 'а' (конец "мама")
    │   └── 'ы' → 'л'
    │       ├── 'а' (конец "мыла")
    │       ├── 'о' → 'м' (конец "мылом")
    │       └── 'а' → 'с' → 'ь' (конец "мылась")
    ├── 'р' → 'а' → 'м'
    │   ├── 'у' (конец "раму")
    │   └── 'а' (конец "рама")
    └── 'г' → 'в' → 'о' → 'з' → 'д'
        ├── 'е' → 'м' (конец "гвоздем")
        └── 'и' → 'к' → 'о' → 'й' (конец "гвоздикой")

```

Здесь наглядно видно, как работает автодополнение: на уровне «г» мы видим
два узла и хвосты этих узлов - автодополнение возникает само собой: это либо 1)
«ем», либо 2) «икой».

Очевидно, что при вводе буквы «м» автодополнение должно показывать только
«ама», а не другие варианты - если их слишком много. Хотя, в принципе, почему
бы и нет - всё зависит от изначальной задумки. Ещё раз напомню: о префиксных
деревьях и алгоритмах на них мы поговорим в отдельной главе - там же разберём
соответствующие задачи. А сейчас давайте обсудим неочевидные механики, чтобы
подвести вас к единственному практическому заданию в этом разделе.

Эскалация данных на листьях в дереве: общее для Trie и деревьев Меркла
---

Мы рассмотрим сравнение с деревьями Меркла. Если вы забыли, что это такое,
напомню: деревья Меркла нужны для быстрой валидации (проверки) того, является
ли элемент частью дерева. Это возможно потому, что каждый верхний уровень
зависит от элемента - в итоге мы можем сравнить корень дерева из оригинала
с тем, что получим после проверок относительно произвольного элемента (это
может быть транзакция перевода денег, коммит в Git и т. д.).

Итак, и за деревьями Меркла, и за префиксными деревьями стоит одна большая
идея: эскалировать какой‑то процесс при движении по дереву **от листьев к корню**.

Например, в дереве Меркла эскалируется вычисление хешей - при этом эскалация
идёт снизу вверх (снизу мало, а сверху, на базе того, что снизу, - много).

```text
      Root      <- максимально объединенный хеш
     /    \
    AB     CD   <- объединенные хеши
   / \    / \
  A   B  C   D  <- отдельные хеши

```

причем, если мы запрашиваем запрос для "C", мы получим проверку:

```text
(1) hash(C)     = собственно C
(2) hash(C+D)   = CD
(3) hash(AB+CD) = Root

```

Мы начинаем разгонять хеш от листьев к его корню, получая в процессе валидацию.
То есть процесс такого движения создаёт механику «валидации».

На префиксных деревьях мы тоже эскалируем данные, получая в итоге механику,
но не от листьев к корню, а от корня к листьям. То есть эскалация данных идёт
уже **сверху вниз**. Практически на уровне укрупнённой логики это очень похожая идея (!).

Она выглядит так:

Любой процесс, представленный через данные, который имеет что‑то общее с другими
процессами в пересекающихся его частях и который дробится или укрупняется по
движению от корня к низу или снизу к корню, может эффективно быть представлен
(запрограммирован) в виде дерева. Это позволит двигаться по такому процессу
(его частям) с максимальной скоростью, реализуя механики, свойственные этому процессу.

Ну что, переходим к практике. Мы дадим обзорный вид кода префиксного дерева -
в дополнение к коду дерева Меркла (который я вам рекомендую посмотреть
самостоятельно; я специально не дам к нему подсказок и комментариев).

Ещё раз повторюсь: про префиксные деревья и про алгоритмы на них у нас будет
отдельная глава.

x014.1. Структура данных: Префиксные деревья Интро. Практика.
===

файл: x01_SimpleTrie.java
---
```java
package ...;

import java.util.HashMap;
import java.util.Map;

// повторюсь, будем подробно разбирать в отдельной главе
// здесь только обзорно
public class x01_SimpleTrie {
  static class TrieNode {
    Map<Character, TrieNode> children = new HashMap<>();
    boolean isEndOfWord;
  }

  static class Trie {
    private TrieNode root = new TrieNode();

    public void insert(String word) {
      TrieNode current = root;
      for (char ch : word.toCharArray()) {
        current = current.children.computeIfAbsent(ch, k -> new TrieNode());
      }
      current.isEndOfWord = true;
    }

    public boolean search(String word) {
      TrieNode node = searchNode(word);
      return node != null && node.isEndOfWord;
    }

    private TrieNode searchNode(String word) {
      TrieNode current = root;
      for (char ch : word.toCharArray()) {
        if (!current.children.containsKey(ch)) {
          return null;
        }
        current = current.children.get(ch);
      }
      return current;
    }
  }

  public static void main(String[] args) {
    var tr = new Trie();
    tr.insert("apple");
    tr.insert("app");

    System.out.println(tr.search("apple")); // true
    System.out.println(tr.search("app"));   // true
    System.out.println(tr.search("ap"));    // false (не полное слово)
  }
}

```

файл: x02_SimpleMerkle.java
---
```java
package ...;

import java.security.MessageDigest;
import java.security.NoSuchAlgorithmException;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

import static java.lang.System.*;

/*
    Для примера я приведу исходный код дерева Меркла.
    Попробуйте разобраться, что здесь происходит,
    используя навыки, которые вы получили в главе про структуры данных.
    
    Здесь есть и собственно дерево с нодами,
    и хеши, и движение по дереву, и листы, и эскалация данных.
    
    Если не слишком получается - не расстраивайтесь.
    Это специализированная структура, и вам вряд ли придётся
    такое программировать. Вы будете брать готовое решение
    и просто использовать его.
    
    НО при этом уже понимая, что это такое и почему оно работает.
    
    Попробуйте просто понять принципы, на базе которых
    строится эта структура данных, - исходя из тех принципов,
    которые вы уже знаете. В том числе постарайтесь понять
    идею того, как эскалируются данные от узлов к корню.
* */

public class x02_SimpleMerkle {
  static class MerkleTree {
    private static class Node {
      String hash;
      Node left;
      Node right;

      Node(String hash) {
        this.hash = hash;
      }

      Node(String hash, Node left, Node right) {
        this.hash = hash;
        this.left = left;
        this.right = right;
      }
    }

    private Node root;
    private List<String> transactions;
    private final MessageDigest digest;

    public MerkleTree(List<String> transactions) throws NoSuchAlgorithmException {
      this.transactions = new ArrayList<>(transactions);
      this.digest = MessageDigest.getInstance("SHA-256");
      this.root = buildTree(transactions);
    }

    private Node buildTree(List<String> data) {
      List<Node> nodes = new ArrayList<>();

      for (String item : data) {
        nodes.add(new Node(hash(item)));
      }

      while (nodes.size() > 1) {
        List<Node> newLevel = new ArrayList<>();

        for (int i = 0; i < nodes.size(); i += 2) {
          Node left = nodes.get(i);
          Node right = (i + 1 < nodes.size()) ? nodes.get(i + 1) : left;

          String combinedHash = hash(left.hash + right.hash);
          newLevel.add(new Node(combinedHash, left, right));
        }

        nodes = newLevel;
      }

      return nodes.get(0);
    }

    private String hash(String input) {
      byte[] hashBytes = digest.digest(input.getBytes());
      StringBuilder hexString = new StringBuilder();

      for (byte b : hashBytes) {
        String hex = Integer.toHexString(0xff & b);
        if (hex.length() == 1) hexString.append('0');
        hexString.append(hex);
      }

      return hexString.toString();
    }

    public String getRootHash() {
      return root != null ? root.hash : "";
    }

    public List<String> getProof(String transaction) {
      List<String> proof = new ArrayList<>();
      String targetHash = hash(transaction);
      buildProof(root, targetHash, proof);
      return proof.isEmpty() ? null : proof;
    }

    private boolean buildProof(Node node, String targetHash, List<String> proof) {
      if (node == null) return false;

      if (node.left == null && node.right == null) {
        return node.hash.equals(targetHash);
      }

      if (buildProof(node.left, targetHash, proof)) {
        if (node.right != null) {
          proof.add(node.right.hash);
        }
        return true;
      }

      if (buildProof(node.right, targetHash, proof)) {
        proof.add(node.left.hash);
        return true;
      }

      return false;
    }

    public static boolean verifyProof(String transaction, List<String> proof, String rootHash)
        throws NoSuchAlgorithmException {
      if (proof == null) return false;

      MessageDigest digest = MessageDigest.getInstance("SHA-256");
      String currentHash = hash(transaction, digest);

      for (String proofItem : proof) {
        String combined = currentHash.compareTo(proofItem) < 0
                          ? currentHash + proofItem
                          : proofItem + currentHash;

        currentHash = hash(combined, digest);
      }

      return currentHash.equals(rootHash);
    }

    private static String hash(String input, MessageDigest digest) {
      byte[] hashBytes = digest.digest(input.getBytes());
      StringBuilder hexString = new StringBuilder();

      for (byte b : hashBytes) {
        String hex = Integer.toHexString(0xff & b);
        if (hex.length() == 1) hexString.append('0');
        hexString.append(hex);
      }

      return hexString.toString();
    }

    public void printTree() {
      printTree(root, 0);
    }

    private void printTree(Node node, int level) {
      if (node == null) return;

      printTree(node.right, level + 1);

      for (int i = 0; i < level; i++) {
        out.print("    ");
      }
      out.println(node.hash.substring(0, 8) + "...");

      printTree(node.left, level + 1);
    }
  }

  public static void main(String[] args) throws NoSuchAlgorithmException {
    List<String> transactions = Arrays.asList(
        "Tx1: Alice sends 5 BTC to Bob",
        "Tx2: Bob sends 3 BTC to Charlie",
        "Tx3: Charlie sends 2 BTC to Alice",
        "Tx4: Dave sends 10 BTC to Eve"
    );

    var merkleTree = new MerkleTree(transactions);

    out.println("структура дерева:");
    out.println(merkleTree.getRootHash());
    merkleTree.printTree();

    for (String tx : transactions) {
      out.println("\nдля транзакции: " + tx);

      var proof = merkleTree.getProof(tx);
      out.println("пруф: " + proof);

      var isValid = MerkleTree.verifyProof(tx, proof, merkleTree.getRootHash());
      out.println("корректность пруфа: " + isValid);

      if (!isValid) {
        err.println("ERROR: " + tx);
      }
    }

    var fakeTx = "произвольный текст";
    out.println("\nдля тестовой фейковой транзакции: " + fakeTx);
    var fakeProof = merkleTree.getProof(fakeTx);
    out.println("пруф: " + fakeProof); // null
  }
}
```
| [Назад](ax013-data-structures-graphs-intro.md) | [Оглавление](README.md) | [Вперёд](ax015-algorithms-table-driven-testing.md) |