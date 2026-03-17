## Java Core Interview Questions & Answers

Focused, repeated core Java interview questions with clear, short answers.

---

## OOP concepts in Java – detailed with examples

---

### 0. Basic terms: class, object, constructor

- **Class**: Blueprint (template) that defines fields and methods.
- **Object**: Instance of a class (lives in heap).
- **Constructor**: Special method used to create and initialize objects.

```java
public class Product {
  private long id;
  private String name;

  public Product(long id, String name) { // constructor
    this.id = id;
    this.name = name;
  }

  public String getName() {   // method
    return name;
  }
}

public class Main {
  public static void main(String[] args) {
    Product p = new Product(1L, "Laptop"); // object
    System.out.println(p.getName());
  }
}
```

---

### 1. Encapsulation (data hiding + getters/setters)

**Concept:** Keep fields **private** and expose controlled access via **public methods** (getters/setters). Protects internal state and enforces invariants.

```java
public class BankAccount {
  private String owner;
  private double balance;   // cannot be accessed directly from outside

  public BankAccount(String owner, double initialBalance) {
    this.owner = owner;
    this.balance = initialBalance;
  }

  public double getBalance() {
    return balance;
  }

  public void deposit(double amount) {
    if (amount > 0) {
      balance += amount;
    }
  }

  public boolean withdraw(double amount) {
    if (amount > 0 && amount <= balance) {
      balance -= amount;
      return true;
    }
    return false;
  }
}
```

**Interview line:** “Encapsulation means hiding fields, exposing behavior. It lets me validate and protect object state.”

---

### 2. Inheritance (`extends`) vs Composition (has-a)

**Inheritance (is-a):** Child class **inherits** fields/methods of parent.

```java
public class Vehicle {
  protected String brand;
  public void start() {
    System.out.println("Vehicle starting");
  }
}

public class Car extends Vehicle {   // Car is-a Vehicle
  public void openTrunk() {
    System.out.println("Opening trunk");
  }
}
```

**Composition (has-a):** A class has references to other classes (preferred in many designs).

```java
public class Engine {
  public void run() {
    System.out.println("Engine running");
  }
}

public class Car2 {           // Car2 has-a Engine
  private Engine engine = new Engine();

  public void start() {
    engine.run();
  }
}
```

**Interview line:** “I use inheritance for real is-a relationships and composition (has-a) for code reuse and flexibility.”

---

### 3. Polymorphism – overloading and overriding

**Polymorphism** = many forms. In Java:

- **Compile-time polymorphism** – **method overloading** (same name, different parameter list).
- **Runtime polymorphism** – **method overriding** (subclass provides own implementation; call decided at runtime).

**Overloading example:**

```java
public class Calculator {
  public int add(int a, int b) {
    return a + b;
  }

  public double add(double a, double b) {   // overloaded
    return a + b;
  }
}
```

**Overriding example + dynamic dispatch:**

```java
class Animal {
  public void sound() {
    System.out.println("Some sound");
  }
}

class Dog extends Animal {
  @Override
  public void sound() {
    System.out.println("Woof");
  }
}

class Cat extends Animal {
  @Override
  public void sound() {
    System.out.println("Meow");
  }
}

public class MainPoly {
  public static void main(String[] args) {
    Animal a1 = new Dog();
    Animal a2 = new Cat();

    a1.sound();  // Woof
    a2.sound();  // Meow
  }
}
```

**Interview line:** “Overloading is same method name with different parameters; overriding is redefining a method in a subclass, and which version runs depends on the runtime type.”

---

### 4. Abstraction – abstract classes and interfaces

**Abstraction** = show **what** an object does, hide **how** it does it.

#### Abstract class

```java
public abstract class Payment {
  public abstract void pay(double amount);   // abstract method

  public void printReceipt(double amount) {  // concrete method
    System.out.println("Paid: " + amount);
  }
}

public class CreditCardPayment extends Payment {
  @Override
  public void pay(double amount) {
    System.out.println("Paying " + amount + " with credit card");
  }
}
```

#### Interface

```java
public interface Notifier {
  void send(String message);
}

public class EmailNotifier implements Notifier {
  @Override
  public void send(String message) {
    System.out.println("Sending EMAIL: " + message);
  }
}

public class SmsNotifier implements Notifier {
  @Override
  public void send(String message) {
    System.out.println("Sending SMS: " + message);
  }
}
```

Using abstraction in code:

```java
public class NotificationService {
  private final Notifier notifier;   // depends on abstraction

  public NotificationService(Notifier notifier) {
    this.notifier = notifier;
  }

  public void notifyUser(String msg) {
    notifier.send(msg);
  }
}
```

**Interview line:** “I depend on interfaces/abstract classes so I can switch implementations (e.g., Email vs SMS) without changing business logic.”

---

### 5. Simple OOP program example (Product + Order)

```java
class Product {
  private final long id;
  private final String name;
  private final double price;

  public Product(long id, String name, double price) {
    this.id = id;
    this.name = name;
    this.price = price;
  }

  public double getPrice() {
    return price;
  }

  public String getName() {
    return name;
  }
}

class OrderItem {
  private final Product product;
  private final int quantity;

  public OrderItem(Product product, int quantity) {
    this.product = product;
    this.quantity = quantity;
  }

  public double getLineTotal() {
    return product.getPrice() * quantity;
  }
}

class Order {
  private final List<OrderItem> items = new ArrayList<>();

  public void addItem(Product product, int qty) {
    items.add(new OrderItem(product, qty));
  }

  public double getTotal() {
    double sum = 0;
    for (OrderItem item : items) {
      sum += item.getLineTotal();
    }
    return sum;
  }
}

public class OrderDemo {
  public static void main(String[] args) {
    Product laptop = new Product(1L, "Laptop", 1000.0);
    Product mouse = new Product(2L, "Mouse", 25.0);

    Order order = new Order();
    order.addItem(laptop, 1);
    order.addItem(mouse, 2);

    System.out.println("Total: " + order.getTotal());
  }
}
```

**Concepts shown:** encapsulation (private fields), composition (Order has OrderItems has Products), basic abstraction (only methods exposed).

---

### 1. What is the difference between JDK, JRE, and JVM?

**Answer:**
- **JVM (Java Virtual Machine)**: Runs compiled `.class` bytecode. It is platform-dependent.
- **JRE (Java Runtime Environment)**: JVM + core libraries (rt.jar) + basic tools to run Java programs.
- **JDK (Java Development Kit)**: JRE + compilers (`javac`) + development tools (debugger, javadoc). Used to **develop** Java applications.

**Interview note:** “To run Java you need JRE, to develop you need JDK, JVM is the runtime engine inside JRE.”

---

### 2. What is the difference between `==` and `.equals()` in Java?

**Answer:**
- `==` compares **references** for objects (same memory address) and **values** for primitives.
- `.equals()` compares **content / logical equality** (when properly overridden).

**Example:**

```java
String a = new String("hello");
String b = new String("hello");

System.out.println(a == b);        // false (different objects)
System.out.println(a.equals(b));   // true (same content)
```

---

### 3. What is String immutability? Why is `String` immutable?

**Answer:** Once a `String` is created, its value **cannot change**. Any modification creates a **new** `String`.

**Reasons:**
- **Security**: Strings used in class names, file paths, URLs, etc., should not change.
- **Caching**: String pool works because Strings are immutable.
- **Thread-safety**: Immutable objects are naturally thread-safe.

**Example:**

```java
String s = "abc";
s.concat("d");         // returns new String, does NOT change s
System.out.println(s); // abc
```

---

### 4. What is a String pool?

**Answer:** A memory area inside the JVM heap where **String literals** are stored. When you write `"abc"` multiple times, they can refer to the **same object** in the pool.

**Example:**

```java
String s1 = "abc";
String s2 = "abc";
System.out.println(s1 == s2); // true (same pooled literal)
```

---

### 5. What is the difference between `String`, `StringBuilder`, and `StringBuffer`?

**Answer:**
- `String`: Immutable. Every change creates a new object.
- `StringBuilder`: Mutable, **not synchronized**, faster for single-threaded code.
- `StringBuffer`: Mutable, **synchronized**, slower, old API.

**Example:**

```java
StringBuilder sb = new StringBuilder("Hi");
sb.append(" Java");  // modifies same object
```

---

### 6. What is a Wrapper class? Why do we need it?

**Answer:** Wrapper classes (`Integer`, `Long`, `Double`, `Boolean`, etc.) wrap primitive types into objects.

**Why:**
- Collections like `List` work with **objects only**, not primitives.
- Provide utility methods (`Integer.parseInt("10")`).
- Support features like **autoboxing/unboxing**.

---

### 7. What is autoboxing and unboxing?

**Answer:**
- **Autoboxing:** Java automatically converts primitive to wrapper type.
- **Unboxing:** Java automatically converts wrapper to primitive.

**Example:**

```java
Integer x = 10;   // autoboxing: int -> Integer
int y = x;        // unboxing: Integer -> int
```

---

### 8. What is the difference between `ArrayList` and `LinkedList`?

**Answer:**
- `ArrayList`: Backed by a dynamic **array**, fast random access (get/set), slower inserts/deletes in middle.
- `LinkedList`: Doubly-linked list, fast **insert/delete** in middle, slower random access.

**Interview line:** “Use `ArrayList` when you read more, `LinkedList` when you insert/delete more in the middle.”

---

### 9. What is `HashMap`? How does it work internally?

**Answer:** `HashMap<K, V>` stores key-value pairs.

- Uses **hashing**: `hashCode()` to find bucket index.
- Handles collisions with a **linked list** or **balanced tree** (Java 8+) in the same bucket.
- Allows **one null key** and multiple null values.

**Key points:**
- Always override `equals()` and `hashCode()` together for custom key classes.
- Good **average** time complexity: O(1) for get/put.

---

### 10. Difference between `HashMap` and `Hashtable`?

**Answer:**
- `HashMap`: Not synchronized, allows null key/value, faster, modern use.
- `Hashtable`: Synchronized, no null keys/values, legacy.

Use `HashMap` + external synchronization if you need thread safety, or use `ConcurrentHashMap`.

---

### 11. What is `ConcurrentHashMap`? Why use it?

**Answer:** Thread-safe map with better performance than `Hashtable`.

- Uses internal **segmenting / finer-grained locking** (and CAS in newer versions).
- Multiple threads can read/write concurrently without global lock on the entire map.

Use when many threads read/write a shared map.

---

### 12. What is `final` in Java? (variable, method, class)

**Answer:**
- `final` variable: value cannot change (constant).
- `final` method: cannot be overridden.
- `final` class: cannot be extended.

**Example:**

```java
final int x = 10;      // x cannot change
final class A {}       // cannot extend A
```

---

### 13. What is `static` in Java?

**Answer:**
- Belongs to the **class**, not to any object.
- Shared by all instances.
- Can be accessed with `ClassName.member`.

**Example:**

```java
class Counter {
  static int count = 0;
  Counter() { count++; }
}
```

---

### 14. What is the difference between `throw` and `throws`?

**Answer:**
- `throw`: Actually **throws** an exception from a method or block.
- `throws`: Declares that the method **may throw** an exception (caller must handle or rethrow).

**Example:**

```java
void m() throws IOException {  // declaration
  throw new IOException();     // actually throwing
}
```

---

### 15. What is the difference between checked and unchecked exceptions?

**Answer:**
- **Checked**: Subclasses of `Exception` (excluding `RuntimeException`). Must be handled or declared with `throws`.
- **Unchecked**: `RuntimeException` and its subclasses. Not required to catch or declare.

**Example:** `IOException` (checked), `NullPointerException` (unchecked).

---

## Java 17 features – what they are and how to use them

Java 17 is an **LTS (Long Term Support)** release. Key features interviewers often ask about:

---

### 1. Records (final data carriers)

**What:** A compact way to define **immutable** data classes. Compiler generates constructor, getters, `equals()`, `hashCode()`, `toString()`.

**How to use:**

```java
// Instead of a class with boilerplate
public record Product(long id, String name, BigDecimal price) {}

// Use it
Product p = new Product(1L, "Laptop", new BigDecimal("999.99"));
System.out.println(p.name());   // getter is name(), not getName()
System.out.println(p);           // Product[id=1, name=Laptop, price=999.99]
```

**When:** DTOs, API request/response objects, configuration holders, anything that is “data only.”

---

### 2. Sealed classes and interfaces

**What:** Restrict **which classes can extend** a class or **implement** an interface. You list the permitted subclasses.

**How to use:**

```java
public sealed interface Shape permits Circle, Rectangle, Square {}

public final class Circle implements Shape {}
public final class Rectangle implements Shape {}
public record Square(double side) implements Shape {}
```

**Why:** Better modeling of fixed hierarchies (e.g., payment types, result types); enables exhaustive switch/pattern matching.

---

### 3. Pattern matching for `instanceof`

**What:** Use `instanceof` and cast in one step; the variable is in scope only in the `true` branch.

**How to use:**

```java
// Old way
if (obj instanceof String) {
  String s = (String) obj;
  System.out.println(s.toUpperCase());
}

// Java 17 way
if (obj instanceof String s) {
  System.out.println(s.toUpperCase());
}
```

**When:** After checking type, you often cast and use – pattern matching avoids repetition and errors.

---

### 4. Switch expressions

**What:** `switch` can be an **expression** that returns a value. Use `->` and no `break`; use `yield` for block bodies.

**How to use:**

```java
String result = switch (day) {
  case "MON", "TUE", "WED", "THU", "FRI" -> "Weekday";
  case "SAT", "SUN" -> "Weekend";
  default -> throw new IllegalArgumentException("Unknown: " + day);
};

// With block
int len = switch (s) {
  case null -> 0;
  case String str -> {
    int n = str.length();
    yield n > 10 ? 10 : n;
  }
};
```

**When:** Replacing long if-else or old switch when you need a single value from multiple cases.

---

### 5. Text blocks (multi-line strings)

**What:** Multi-line string literals with **three quotes** `"""`. Avoids escape sequences and concatenation.

**How to use:**

```java
String json = """
    {
      "name": "Java",
      "version": 17
    }
    """;

String sql = """
    SELECT id, name
    FROM users
    WHERE active = true
    """;
```

**When:** JSON, XML, SQL, HTML snippets, any multi-line string in code.

---

### 6. Helpful NullPointerException messages

**What:** From Java 14+, the JVM can tell you **which variable was null** in an expression (e.g., “Cannot invoke method because the return value of ‘foo.getBar()’ is null”).

**How to use:** No code change; enable with JVM flag if needed: `-XX:+ShowCodeDetailsInExceptionMessages` (default in many setups).

**When:** Debugging NPE in long chains like `a.getB().getC()`.

---

### 7. New and updated APIs (Java 17)

- **`Random`**: New factory `Random.of(String seed)` for reproducible streams.
- **`Stream.toList()`**: Direct way to get an unmodifiable list (instead of `collect(Collectors.toList())`).
- **`Optional.isEmpty()`**: Clearer than `!optional.isPresent()`.

**How to use:**

```java
List<String> list = stream.filter(Objects::nonNull).toList();   // unmodifiable

if (optional.isEmpty()) { ... }
```

---

### Quick reference (interview)

| Feature | Use when |
|--------|----------|
| **Records** | Immutable DTOs, config, simple data holders |
| **Sealed classes** | Fixed type hierarchies (payments, shapes, results) |
| **instanceof pattern** | After type check, use the variable without manual cast |
| **Switch expressions** | One value from many cases; exhaustive switches |
| **Text blocks** | JSON, SQL, HTML, multi-line strings |
| **toList()** | Get unmodifiable list from stream |

