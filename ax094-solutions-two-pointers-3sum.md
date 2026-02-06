[Оглавление](README.md)

Решебник. TwoPointers_ThreeSum
===
```java
package ru.pragm;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

import static ru.pragm.utils.Out.*;

/*
Задача:
TwoPointers
3Sum
---------------------------------
    Логически не существует отдельного решения 3sum, базирующегося на
    каком-либо алгоритмическом паттерне, но есть решение 2sum,
    которое можно объединить с обычным перебором.
    
    Такой перебор для первого элемента и решение для остальных 2sum
    называется «фиксацией».
    
    Что в итоге мы и получим, если решения будут объединены:
    (здесь подразумевается, что массив не отсортирован - чтобы использовать 2sum,
    мы сначала его дополнительно сортируем; а также что массив может содержать
    дубликаты - мы их пропускаем).
* */
public class TwoPointers_ThreeSum {

  public class SolutionA {
    public List<List<Integer>> threeSum(int[] nums) {
      Arrays.sort(nums);
      List<List<Integer>> result = new ArrayList<>();
      for (int i = 0; i < nums.length - 2; i++) {         // <- фиксация
        if (i > 0 && nums[i] == nums[i - 1]) {            // <- дубликаты
          continue;
        }

        int left = i + 1;                                 // <- начинается twoSum
        int right = nums.length - 1;
        int target = -nums[i];
        while (left < right) {
          int currentSum = nums[left] + nums[right];
          if (currentSum == target) {
            result.add(Arrays.asList(nums[i], nums[left], nums[right]));
            while (left < right && nums[left] == nums[left + 1]) {
              left++;
            }
            while (left < right && nums[right] == nums[right - 1]) {
              right--;
            }
            left++;
            right--;
          } else if (currentSum < target) {
            left++;
          } else {
            right--;
          }
        }
      }
      return result;
    }
  }

  /*
  решение, где 2sum выделено отдельно,
  не эффективно из за постоянного копирования массива,
  но максимально показательно:
  * */
  public class SolutionB {

    public List<int[]> twoSumAllPairs(int[] numbers, int target) {
      List<int[]> pairs = new ArrayList<>();
      int left = 0;
      int right = numbers.length - 1;

      while (left < right) {
        int currentSum = numbers[left] + numbers[right];
        if (currentSum == target) {
          pairs.add(new int[]{numbers[left], numbers[right]});

          while (left < right && numbers[left] == numbers[left + 1]) {
            left++;
          }
          while (left < right && numbers[right] == numbers[right - 1]) {
            right--;
          }
          left++;
          right--;
        } else if (currentSum < target) {
          left++;
        } else {
          right--;
        }
      }
      return pairs;
    }

    public List<List<Integer>> threeSum(int[] nums) {
      Arrays.sort(nums);
      List<List<Integer>> result = new ArrayList<>();

      for (int i = 0; i < nums.length - 2; i++) {
        if (i > 0 && nums[i] == nums[i - 1]) {
          continue;
        }

        int target = -nums[i];
        // <- подмассив для twoSum
        //
        int[] subarray = Arrays.copyOfRange(nums, i + 1, nums.length);
        List<int[]> pairs = twoSumAllPairs(subarray, target);

        for (int[] pair : pairs) {
          result.add(Arrays.asList(nums[i], pair[0], pair[1]));
        }
      }

      return result;
    }
  }

  /*
  версия без копирования и на базе ArrayList:
  * */
  public class SolutionC {

    public List<List<Integer>> twoSum(int[] numbers, int target, int start) {
      List<List<Integer>> result = new ArrayList<>();
      int left = start;
      int right = numbers.length - 1;

      while (left < right) {
        int currentSum = numbers[left] + numbers[right];
        if (currentSum == target) {
          result.add(Arrays.asList(numbers[left], numbers[right]));

          while (left < right && numbers[left] == numbers[left + 1]) {
            left++;
          }
          while (left < right && numbers[right] == numbers[right - 1]) {
            right--;
          }
          left++;
          right--;
        } else if (currentSum < target) {
          left++;
        } else {
          right--;
        }
      }
      return result;
    }

    public List<List<Integer>> threeSum(int[] nums) {
      Arrays.sort(nums);
      List<List<Integer>> result = new ArrayList<>();

      for (int i = 0; i < nums.length - 2; i++) {
        if (i > 0 && nums[i] == nums[i - 1]) {
          continue;
        }

        int target = -nums[i];
        List<List<Integer>> twoSumResults = twoSum(nums, target, i + 1);

        for (List<Integer> pair : twoSumResults) {
          result.add(Arrays.asList(nums[i], pair.get(0), pair.get(1)));
        }
      }

      return result;
    }
  }

  static void main() {
    var wrapper = new TwoPointers_ThreeSum();
    var solutionA = wrapper.new SolutionA();
    var solutionB = wrapper.new SolutionB();
    var solutionC = wrapper.new SolutionC();

    int[] testData1 = {-1, 0, 1, 2, -1, -4};
    int[] testData2 = {0, 0, 0, 0};
    String expectedA = " : ожидается: [[-1, -1, 2], [-1, 0, 1]]";
    String expectedB = " : ожидается: [[0, 0, 0]]";

    out("SolutionA результат: " + solutionA.threeSum(testData1) + expectedA);
    out("SolutionB результат: " + solutionB.threeSum(testData1) + expectedA);
    out("SolutionC результат: " + solutionC.threeSum(testData1) + expectedA);

    out("");

    out("SolutionA результат: " + solutionA.threeSum(testData2) + expectedB);
    out("SolutionB результат: " + solutionB.threeSum(testData2) + expectedB);
    out("SolutionC результат: " + solutionC.threeSum(testData2) + expectedB);
  }

}//c:TwoPointers_ThreeSum

```