## Java Programs – Common Interview Questions

Repeated, must-know Java programs with short explanations.

---

### 1. Reverse a String

**Question:** Write a program to reverse a String.

```java
public class ReverseString {
  public static void main(String[] args) {
    String input = "hello";
    String reversed = new StringBuilder(input).reverse().toString();
    System.out.println(reversed);
  }
}
```

**Key points:** Uses `StringBuilder.reverse()`. Short and efficient.

---

### 2. Check if a String is a Palindrome

**Question:** Check if `"madam"` is palindrome or not.

```java
public class PalindromeCheck {
  public static boolean isPalindrome(String s) {
    int i = 0, j = s.length() - 1;
    while (i < j) {
      if (s.charAt(i) != s.charAt(j)) {
        return false;
      }
      i++;
      j--;
    }
    return true;
  }

  public static void main(String[] args) {
    System.out.println(isPalindrome("madam"));  // true
    System.out.println(isPalindrome("hello"));  // false
  }
}
```

**Key points:** Two-pointer technique, O(n) time, O(1) extra space.

---

### 3. Factorial of a Number (Iterative and Recursive)

```java
public class Factorial {
  static long factorialIterative(int n) {
    long result = 1;
    for (int i = 2; i <= n; i++) {
      result *= i;
    }
    return result;
  }

  static long factorialRecursive(int n) {
    if (n <= 1) return 1;
    return n * factorialRecursive(n - 1);
  }

  public static void main(String[] args) {
    System.out.println(factorialIterative(5));  // 120
    System.out.println(factorialRecursive(5));  // 120
  }
}
```

**Key points:** Show both iterative (no stack depth issue) and recursive (cleaner but risk of stack overflow for big n).

---

### 4. Fibonacci Series (up to N terms)

```java
public class Fibonacci {
  public static void main(String[] args) {
    int n = 10;
    int a = 0, b = 1;
    System.out.print(a + " " + b + " ");
    for (int i = 3; i <= n; i++) {
      int c = a + b;
      System.out.print(c + " ");
      a = b;
      b = c;
    }
  }
}
```

**Key points:** Iterative, O(n) time, O(1) space.

---

### 5. Reverse an Integer (e.g., 123 → 321)

```java
public class ReverseNumber {
  public static int reverse(int n) {
    int rev = 0;
    while (n != 0) {
      int digit = n % 10;
      rev = rev * 10 + digit;
      n /= 10;
    }
    return rev;
  }

  public static void main(String[] args) {
    System.out.println(reverse(1234)); // 4321
  }
}
```

**Key points:** Use modulo and division by 10.

---

### 6. Check if a Number is Prime

```java
public class PrimeCheck {
  public static boolean isPrime(int n) {
    if (n <= 1) return false;
    if (n <= 3) return true;
    if (n % 2 == 0 || n % 3 == 0) return false;
    for (int i = 5; i * i <= n; i += 6) {
      if (n % i == 0 || n % (i + 2) == 0) return false;
    }
    return true;
  }

  public static void main(String[] args) {
    System.out.println(isPrime(11)); // true
    System.out.println(isPrime(12)); // false
  }
}
```

**Key points:** Optimized prime check: up to √n, skipping even numbers.

---

### 7. Find Duplicate Elements in an Array

```java
import java.util.HashSet;
import java.util.Set;

public class FindDuplicates {
  public static void main(String[] args) {
    int[] arr = {1, 2, 3, 2, 4, 5, 1};
    Set<Integer> seen = new HashSet<>();
    Set<Integer> duplicates = new HashSet<>();

    for (int n : arr) {
      if (!seen.add(n)) {
        duplicates.add(n);
      }
    }

    System.out.println("Duplicates: " + duplicates);
  }
}
```

**Key points:** `HashSet` detects duplicates in O(n) time.

---

### 8. Sort an Array Without Using Built-in Sort

```java
public class BubbleSortExample {
  public static void bubbleSort(int[] arr) {
    int n = arr.length;
    for (int i = 0; i < n - 1; i++) {
      for (int j = 0; j < n - 1 - i; j++) {
        if (arr[j] > arr[j + 1]) {
          int temp = arr[j];
          arr[j] = arr[j + 1];
          arr[j + 1] = temp;
        }
      }
    }
  }

  public static void main(String[] args) {
    int[] arr = {5, 1, 4, 2, 8};
    bubbleSort(arr);
    for (int v : arr) {
      System.out.print(v + " ");
    }
  }
}
```

**Key points:** Simple bubble sort; good for demonstrating logic even if not efficient.

---

### 9. Count Characters in a String (Frequency)

```java
import java.util.HashMap;
import java.util.Map;

public class CharFrequency {
  public static void main(String[] args) {
    String s = "hello world";
    Map<Character, Integer> freq = new HashMap<>();

    for (char c : s.toCharArray()) {
      if (c == ' ') continue;
      freq.put(c, freq.getOrDefault(c, 0) + 1);
    }

    System.out.println(freq);
  }
}
```

**Key points:** Use `Map` and `getOrDefault`.

---

### 10. Swap Two Numbers Without a Third Variable

```java
public class SwapNumbers {
  public static void main(String[] args) {
    int a = 5, b = 3;
    a = a + b; // 8
    b = a - b; // 5
    a = a - b; // 3
    System.out.println("a = " + a + ", b = " + b);
  }
}
```

**Key points:** Classic trick; mention risk of overflow for large values; temp-variable version is safer in real projects.

---

### 11. Remove Duplicates from an Array (Preserve Order)

```java
import java.util.LinkedHashSet;
import java.util.Set;

public class RemoveDuplicates {
  public static void main(String[] args) {
    int[] arr = {1, 2, 3, 2, 4, 5, 1};
    Set<Integer> set = new LinkedHashSet<>();
    for (int n : arr) {
      set.add(n);
    }
    System.out.println(set); // [1, 2, 3, 4, 5]
  }
}
```

**Key points:** `LinkedHashSet` preserves insertion order.

---

### 12. Reverse words in a String with spaces/tabs

**Question:** Given a String with spaces and tab characters, reverse the **order of words** (not characters).

```java
public class ReverseWords {
  public static void main(String[] args) {
    String input = "  hello\tworld   from\tjava  ";
    // Split on any whitespace (space, tab, etc.) and ignore empty parts
    String[] words = input.trim().split("\\s+");

    StringBuilder sb = new StringBuilder();
    for (int i = words.length - 1; i >= 0; i--) {
      sb.append(words[i]);
      if (i > 0) {
        sb.append(" ");
      }
    }

    System.out.println(sb.toString()); // "java from world hello"
  }
}
```

**Key points:** Use `trim()` + `split("\\s+")` to handle spaces/tabs; reverse array indices.

---

### 13. Two-sum: find pairs whose sum equals target (List)

**Question:** Given a list of integers and a target, print all pairs whose sum = target.

```java
import java.util.HashSet;
import java.util.List;
import java.util.Set;

public class TwoSum {
  public static void main(String[] args) {
    List<Integer> nums = List.of(2, 7, 11, 15, 3, 6, 4, 5);
    int target = 9;
    printPairs(nums, target);
  }

  static void printPairs(List<Integer> nums, int target) {
    Set<Integer> seen = new HashSet<>();
    for (int n : nums) {
      int complement = target - n;
      if (seen.contains(complement)) {
        System.out.println(complement + " + " + n + " = " + target);
      }
      seen.add(n);
    }
  }
}
```

**Key points:** `O(n)` using a `Set` instead of nested loops.

---

### 14. Common Java Stream examples (repeated interview patterns)

```java
import java.util.Comparator;
import java.util.List;
import java.util.Map;
import java.util.stream.Collectors;

public class StreamExamples {
  public static void main(String[] args) {
    List<Integer> nums = List.of(1, 2, 3, 4, 5, 6);

    // 1) Filter even numbers
    List<Integer> evens = nums.stream()
      .filter(n -> n % 2 == 0)
      .collect(Collectors.toList());

    // 2) Square each number
    List<Integer> squares = nums.stream()
      .map(n -> n * n)
      .collect(Collectors.toList());

    // 3) Sum all numbers
    int sum = nums.stream()
      .mapToInt(Integer::intValue)
      .sum();

    // 4) Find max element
    int max = nums.stream()
      .max(Comparator.naturalOrder())
      .orElseThrow();

    // 5) Group strings by length
    List<String> words = List.of("java", "spring", "boot", "kafka");
    Map<Integer, List<String>> byLength = words.stream()
      .collect(Collectors.groupingBy(String::length));

    System.out.println("Evens: " + evens);
    System.out.println("Squares: " + squares);
    System.out.println("Sum: " + sum);
    System.out.println("Max: " + max);
    System.out.println("By length: " + byLength);
  }
}
```

**Key points:** Show `filter`, `map`, `collect`, `mapToInt`, `max`, `groupingBy` – typical interview tasks.

---

### 15. Selection Sort (ascending)

```java
public class SelectionSort {
  public static void selectionSort(int[] arr) {
    int n = arr.length;
    for (int i = 0; i < n - 1; i++) {
      int minIdx = i;
      for (int j = i + 1; j < n; j++) {
        if (arr[j] < arr[minIdx]) {
          minIdx = j;
        }
      }
      int temp = arr[i];
      arr[i] = arr[minIdx];
      arr[minIdx] = temp;
    }
  }

  public static void main(String[] args) {
    int[] arr = {64, 25, 12, 22, 11};
    selectionSort(arr);
    for (int v : arr) {
      System.out.print(v + " ");
    }
  }
}
```

**Key points:** Simple \(O(n^2)\) algorithm; good for explaining logic.

---

### 16. Insertion Sort (ascending)

```java
public class InsertionSort {
  public static void insertionSort(int[] arr) {
    for (int i = 1; i < arr.length; i++) {
      int key = arr[i];
      int j = i - 1;
      while (j >= 0 && arr[j] > key) {
        arr[j + 1] = arr[j];
        j--;
      }
      arr[j + 1] = key;
    }
  }

  public static void main(String[] args) {
    int[] arr = {5, 2, 9, 1, 5, 6};
    insertionSort(arr);
    for (int v : arr) {
      System.out.print(v + \" \");
    }
  }
}
```

**Key points:** Good for nearly sorted arrays; still \(O(n^2)\) worst case.

---

### 17. Merge Sort (ascending)

```java
public class MergeSort {

  public static void mergeSort(int[] arr, int left, int right) {
    if (left >= right) return;
    int mid = left + (right - left) / 2;
    mergeSort(arr, left, mid);
    mergeSort(arr, mid + 1, right);
    merge(arr, left, mid, right);
  }

  private static void merge(int[] arr, int left, int mid, int right) {
    int n1 = mid - left + 1;
    int n2 = right - mid;
    int[] L = new int[n1];
    int[] R = new int[n2];
    System.arraycopy(arr, left, L, 0, n1);
    System.arraycopy(arr, mid + 1, R, 0, n2);

    int i = 0, j = 0, k = left;
    while (i < n1 && j < n2) {
      if (L[i] <= R[j]) {
        arr[k++] = L[i++];
      } else {
        arr[k++] = R[j++];
      }
    }
    while (i < n1) arr[k++] = L[i++];
    while (j < n2) arr[k++] = R[j++];
  }

  public static void main(String[] args) {
    int[] arr = {5, 2, 4, 7, 1, 3, 2, 6};
    mergeSort(arr, 0, arr.length - 1);
    for (int v : arr) {
      System.out.print(v + " ");
    }
  }
}
```

**Key points:** Divide-and-conquer, stable, \(O(n \log n)\) time, \(O(n)\) extra space.

---

### 18. Quick Sort (ascending, simple implementation)

```java
public class QuickSort {

  public static void quickSort(int[] arr, int low, int high) {
    if (low < high) {
      int p = partition(arr, low, high);
      quickSort(arr, low, p - 1);
      quickSort(arr, p + 1, high);
    }
  }

  private static int partition(int[] arr, int low, int high) {
    int pivot = arr[high];
    int i = low - 1;
    for (int j = low; j < high; j++) {
      if (arr[j] <= pivot) {
        i++;
        int tmp = arr[i];
        arr[i] = arr[j];
        arr[j] = tmp;
      }
    }
    int tmp = arr[i + 1];
    arr[i + 1] = arr[high];
    arr[high] = tmp;
    return i + 1;
  }

  public static void main(String[] args) {
    int[] arr = {10, 7, 8, 9, 1, 5};
    quickSort(arr, 0, arr.length - 1);
    for (int v : arr) {
      System.out.print(v + " ");
    }
  }
}
```

**Key points:** Average \(O(n \log n)\), worst \(O(n^2)\). In-place but not stable.

---

## Same programs using Java 8 features (Streams, lambdas – no plain loops)

Below are **Java 8 style** versions of several programs above: use **Streams**, **lambdas**, **method references**, and **Collectors** instead of plain array/collection iteration.

---

### 19. Reverse a String (Java 8)

```java
import java.util.stream.Collectors;

public class ReverseStringJava8 {
  public static void main(String[] args) {
    String input = "hello";
    String reversed = input.chars()
        .mapToObj(c -> (char) c)
        .reduce("", (s, c) -> c + s, (a, b) -> b + a);
    System.out.println(reversed);  // olleh
  }
}
```

**Alternative (using StringBuilder with collect):**

```java
String reversed = input.chars()
    .collect(StringBuilder::new, (sb, c) -> sb.insert(0, (char) c), StringBuilder::append)
    .toString();
```

---

### 20. Check Palindrome (Java 8 – IntStream)

```java
import java.util.stream.IntStream;

public class PalindromeJava8 {
  public static boolean isPalindrome(String s) {
    return IntStream.range(0, s.length() / 2)
        .allMatch(i -> s.charAt(i) == s.charAt(s.length() - 1 - i));
  }

  public static void main(String[] args) {
    System.out.println(isPalindrome("madam"));   // true
    System.out.println(isPalindrome("hello"));   // false
  }
}
```

---

### 21. Find Duplicates in a List (Java 8 – Stream + groupingBy)

```java
import java.util.*;
import java.util.stream.Collectors;

public class FindDuplicatesJava8 {
  public static void main(String[] args) {
    List<Integer> list = Arrays.asList(1, 2, 3, 2, 4, 5, 1);

    Set<Integer> duplicates = list.stream()
        .filter(n -> Collections.frequency(list, n) > 1)
        .collect(Collectors.toSet());

    System.out.println("Duplicates: " + duplicates);  // [1, 2]
  }
}
```

**Efficient one-pass style (still Java 8, using Set):**

```java
Set<Integer> seen = new HashSet<>();
Set<Integer> duplicates = list.stream()
    .filter(n -> !seen.add(n))
    .collect(Collectors.toSet());
```

---

### 22. Character frequency (Java 8 – Stream of chars)

```java
import java.util.Map;
import java.util.stream.Collectors;

public class CharFrequencyJava8 {
  public static void main(String[] args) {
    String s = "hello world";

    Map<Character, Long> freq = s.chars()
        .filter(c -> c != ' ')
        .mapToObj(c -> (char) c)
        .collect(Collectors.groupingBy(c -> c, Collectors.counting()));

    System.out.println(freq);  // {r=1, d=1, e=1, w=1, h=1, l=3, o=2}
  }
}
```

---

### 23. Remove duplicates, preserve order (Java 8 – distinct)

```java
import java.util.Arrays;
import java.util.List;
import java.util.stream.Collectors;

public class RemoveDuplicatesJava8 {
  public static void main(String[] args) {
    List<Integer> list = Arrays.asList(1, 2, 3, 2, 4, 5, 1);

    List<Integer> distinct = list.stream()
        .distinct()
        .collect(Collectors.toList());

    System.out.println(distinct);  // [1, 2, 3, 4, 5]
  }
}
```

**Note:** For insertion order with streams, use ordered stream (e.g. from list); `distinct()` preserves encounter order.

---

### 24. Reverse words in a String (Java 8 – Stream)

```java
import java.util.Arrays;
import java.util.Collections;
import java.util.stream.Collectors;

public class ReverseWordsJava8 {
  public static void main(String[] args) {
    String input = "  hello   world from java  ";
    String reversed = Arrays.stream(input.trim().split("\\s+"))
        .collect(Collectors.collectingAndThen(
            Collectors.toList(),
            list -> {
              Collections.reverse(list);
              return String.join(" ", list);
            }));
    System.out.println(reversed);  // java from world hello
  }
}
```

---

### 25. Sort an array (Java 8 – Arrays.stream + sorted)

```java
import java.util.Arrays;

public class SortArrayJava8 {
  public static void main(String[] args) {
    int[] arr = {5, 1, 4, 2, 8};

    int[] sorted = Arrays.stream(arr)
        .sorted()
        .toArray();

    Arrays.stream(sorted).forEach(n -> System.out.print(n + " "));  // 1 2 4 5 8
  }
}
```

---

### 26. Sum, max, filter (Java 8 – one place)

```java
import java.util.Arrays;
import java.util.OptionalInt;

public class SumMaxFilterJava8 {
  public static void main(String[] args) {
    int[] arr = {3, 1, 4, 1, 5, 9, 2, 6};

    int sum = Arrays.stream(arr).sum();
    OptionalInt maxOpt = Arrays.stream(arr).max();
    int[] evens = Arrays.stream(arr).filter(n -> n % 2 == 0).toArray();

    System.out.println("Sum: " + sum);
    System.out.println("Max: " + maxOpt.orElse(0));
    System.out.println("Evens: " + Arrays.toString(evens));
  }
}
```

---

### Quick reference – Java 8 used here

| Goal              | Java 8 approach                                      |
|-------------------|--------------------------------------------------------|
| Reverse string    | `chars()` + `reduce` or `StringBuilder::insert`       |
| Palindrome        | `IntStream.range().allMatch()`                        |
| Duplicates        | `stream().filter(frequency > 1)` or `filter(!seen.add)` |
| Char frequency    | `chars().mapToObj().groupingBy(..., counting())`       |
| Distinct          | `stream().distinct().collect(toList())`               |
| Reverse words     | `Arrays.stream(split).collect(collectingAndThen(toList(), reverse + join))` |
| Sort array        | `Arrays.stream(arr).sorted().toArray()`               |
| Sum / max / filter| `Arrays.stream().sum()`, `.max()`, `.filter().toArray()` |

