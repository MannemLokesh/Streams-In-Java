
# Java Streams Guide

Since you already know **Functional Interfaces** and **Method References / Lambdas**, learning **Streams** will be smooth.  
Streams are one of the most powerful features introduced in **Java 8** for processing collections of data in a **declarative** and **functional style**.

---

## 🔹 What is a Stream?
- A **Stream** is a sequence of elements supporting sequential and parallel operations.
- It does **not** store data; it just processes it.
- It is different from `java.io.InputStream/OutputStream` → those are for I/O.
- Stream API works mainly with **Collections**, **Arrays**, or **generators**.

---

## 🔹 Stream Operations
There are **3 types** of operations in Streams:

###  **1. Creation**
Ways to create streams:
```java
import java.util.*;
import java.util.stream.*;

public class StreamCreation {
    public static void main(String[] args) {
        // From List
        List<String> names = Arrays.asList("Lokesh", "Ravi", "Anu", "Ravi");
        Stream<String> stream1 = names.stream();

        // From Arrays
        Stream<Integer> stream2 = Arrays.stream(new Integer[]{1, 2, 3, 4});

        // Using Stream.of()
        Stream<String> stream3 = Stream.of("A", "B", "C");

        // Infinite Stream
        Stream<Integer> evenNumbers = Stream.iterate(0, n -> n + 2);
    }
}
```
---
### **2. Intermediate Operations (transform stream → new stream)**
These are lazy (only run when terminal op is called).
- **map(Function)** → transforms each element
- **filter(Predicate)** → filters based on condition
- **distinct()** → removes duplicates
- **sorted()** → natural or custom sorting
- **limit(n)** → take first n
- **skip(n)** → skip first n
- **flatMap(Function)** → flatten nested structures
Example:
```java
List<String> names = Arrays.asList("Lokesh", "Ravi", "Anu", "Ravi");

List<String> result = names.stream()
        .filter(s -> s.startsWith("R"))
        .map(String::toUpperCase)
        .distinct()
        .sorted()
        .collect(Collectors.toList());

System.out.println(result); // [RAVI]
```
---

### **3. Terminal Operations (end of pipeline, produce result)**
- **collect()** → convert stream to list, set, map
- **forEach()** → iterate over elements
- **reduce()*** → reduce elements to single value
- **count()** → number of elements
- **findFirst() / findAny()**
- **anyMatch(), allMatch(), noneMatch()**
- **min(), max()**
Example:
```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6);

// sum using reduce
int sum = numbers.stream()
        .reduce(0, (a, b) -> a + b);
System.out.println("Sum = " + sum); // 21

// collect into list
List<Integer> evenNumbers = numbers.stream()
        .filter(n -> n % 2 == 0)
        .collect(Collectors.toList());

System.out.println(evenNumbers); // [2, 4, 6]
```
---
---
---
---
---
# 🔹 Practice Code Examples
### Example 1: Count unique words
```java
List<String> words = Arrays.asList("apple", "banana", "apple", "orange");

long uniqueCount = words.stream()
        .distinct()
        .count();

System.out.println("Unique words = " + uniqueCount); // 3
```

### Example 2: Find max salary of employee
```java
import java.util.*;

class Employee {
    String name;
    int salary;
    Employee(String name, int salary) {
        this.name = name;
        this.salary = salary;
    }
    public String toString() { return name + " : " + salary; }
}

public class EmployeeStream {
    public static void main(String[] args) {
        List<Employee> employees = Arrays.asList(
            new Employee("Lokesh", 60000),
            new Employee("Ravi", 45000),
            new Employee("Anu", 70000)
        );

        employees.stream()
                 .max(Comparator.comparingInt(e -> e.salary))
                 .ifPresent(System.out::println); // Anu : 70000
    }
}
```

### Example 3: Grouping by
```java
import java.util.*;
import java.util.stream.Collectors;

public class GroupByExample {
    public static void main(String[] args) {
        List<String> names = Arrays.asList("apple", "banana", "cherry", "apricot", "blueberry");

        Map<Character, List<String>> grouped = names.stream()
                .collect(Collectors.groupingBy(s -> s.charAt(0)));

        System.out.println(grouped);
        // {a=[apple, apricot], b=[banana, blueberry], c=[cherry]}
    }
}
```



---
---
---
---
---
# Java Streams Collectors Guide

This document explains how to use the **`Collectors`** class in Java Streams with practical examples.  
Since you already know Functional Interfaces, Lambdas, and Method References, this guide focuses on `Collectors` methods.

---

## 1. What is `Collectors`?
- `Collectors` is a utility class (`java.util.stream.Collectors`) that provides **factory methods** to create `Collector` instances.
- A **Collector** is a strategy to reduce a stream into a result (like a `List`, `Set`, `Map`, or a single value).

👉 `Stream.collect()` signature:
```java
<R, A> R collect(Collector<? super T, A, R> collector)
```

### Where

- **T** → Type of stream elements  
  (The type of elements present in the input stream)

- **A** → Mutable accumulation type (intermediate container)  
  (The type of the object that accumulates results during the collection process)

- **R** → Final result type  
  (The type of the result after collection is finished)

---


## 2. Core Collectors (Most Commonly Used)
Let’s walk through major categories of collectors with examples.

### (A) Collecting into Collections
```java
List<String> names = List.of("Ram", "Shyam", "Geeta");

List<String> list = names.stream()
        .collect(Collectors.toList());

Set<String> set = names.stream()
        .collect(Collectors.toSet());

LinkedList<String> linkedList = names.stream()
        .collect(Collectors.toCollection(LinkedList::new));
```

- **toList()** → Collects into a List (not necessarily ArrayList before Java 16).

- **toSet()** → Collects into a Set (not necessarily HashSet).

- **toCollection(Supplier<C>)** → Collects into any specific collection type.
---

### (B) Joining Strings
```java
String result = names.stream()
        .collect(Collectors.joining(", ", "[", "]"));
System.out.println(result); // [Ram, Shyam, Geeta]
```
- **joining()** → concatenates strings.

- With delimiter + prefix + suffix.
---


### (C) Counting and Summarizing
```java
long count = names.stream()
        .collect(Collectors.counting());

int sumLength = names.stream()
        .collect(Collectors.summingInt(String::length));

DoubleSummaryStatistics stats = names.stream()
        .collect(Collectors.summarizingDouble(String::length));

```
- **counting()** → count elements.

- **summingInt/Long/Double()** → sum of mapped values.

- **summarizingInt/Long/Double()** → gives SummaryStatistics (count, sum, min, max, avg).
---

### (D) Averaging
```java
double avg = names.stream()
        .collect(Collectors.averagingInt(String::length));

```
- **averagingInt/Long/Double()** → average of mapped values.
---

### (E) Grouping
```java
Map<Integer, List<String>> byLength = names.stream()
        .collect(Collectors.groupingBy(String::length));

Map<Integer, Set<String>> byLengthSet = names.stream()
        .collect(Collectors.groupingBy(
            String::length, Collectors.toSet()));
```

- **groupingBy(classifier)** → groups elements into Map<key, List>.

- Can supply a downstream collector (like toSet(), counting()).

```java
Map<Integer, Long> countByLength = names.stream()
        .collect(Collectors.groupingBy(
            String::length, Collectors.counting()));
```
---

### (F) Partitioning
```java
Map<Boolean, List<String>> partitioned = names.stream()
        .collect(Collectors.partitioningBy(s -> s.length() > 3));
```
- Special case of grouping — only two groups (true and false).
---

### (G) Mapping
```java
List<Integer> lengths = names.stream()
        .collect(Collectors.mapping(String::length, Collectors.toList()));
```
- mapping(mapper, downstream) → apply mapping before collecting downstream.
---

### (H) Reducing
```java
Optional<String> longest = names.stream()
        .collect(Collectors.reducing((s1, s2) -> s1.length() > s2.length() ? s1 : s2));
```
- reducing(BinaryOperator) → reduces elements into one.

- Also has overloads with identity and mapper.

---

### (I) ToMap
```java
Map<String, Integer> nameLengthMap = names.stream()
        .collect(Collectors.toMap(
            name -> name,
            String::length
        ));
```

- **toMap(keyMapper, valueMapper)** → collects into map.

- Overloads allow merge functions and custom map suppliers.


#### Example with duplicates:
``` java
Map<Integer, String> map = names.stream()
        .collect(Collectors.toMap(
            String::length,
            s -> s,
            (s1, s2) -> s1 + "," + s2 // merge function
        ));
```
---

### (J) Collectors.collectingAndThen()
```java
List<String> unmodifiable = names.stream()
        .collect(Collectors.collectingAndThen(
            Collectors.toList(),
            Collections::unmodifiableList
        ));

```
- Post-processes the result of another collector.

---


## 3. Visual Summary (Cheat-Sheet)

- **toList(), toSet(), toCollection()** → Collections
- **joining()** → String
- **counting(), summingX, averagingX, summarizingX** → Numbers & Stats
- **groupingBy()** → Map (grouping logic)
- **partitioningBy()** → Map<Boolean, List>
- **mapping()** → transform before downstream
- **reducing()** → single reduced value
- **toMap()** → Map with custom keys/values/merge
- **collectingAndThen()** → wrap the result (e.g., make unmodifiable)

---
---
---
---
---

# Practice coding questions
##### **1. Given a list of integers, find out all the even numbers that exist in the list using Stream functions?**
```java
import java.util.*;
import java.util.stream.*;

public class EvenNumber{
    public static void main(String args[]) {
      List<Integer> list = Arrays.asList(10,15,8,49,25,98,32);
            list.stream()
                .filter(n -> n%2 == 0)
                .forEach(System.out::println);

    /* or can also try below method */

/* When numbers are given as Array int[] arr = {10,15,8,49,25,98,32}; */

    Map<Boolean, List<Integer>> list = Arrays.stream(arr).boxed()
    .collect(Collectors.partitioningBy(num -> num % 2 == 0));
    System.out.println(list);
       }
   }


Output: 
10, 8, 98, 32
```

##### **2. Given a list of integers, find out all the numbers starting with 1 using Stream functions?**
```java
import java.util.*;
import java.util.stream.*;

public class NumberStartingWithOne{
    public static void main(String args[]) {
            List<Integer> myList = Arrays.asList(10,15,8,49,25,98,32);
            myList.stream()
                  .map(s -> s + "") // Convert integer to String
                  .filter(s -> s.startsWith("1"))
                  .forEach(System.out::println);

/* or can also try below method */

/* When numbers are given as Array int[] arr = {10,15,8,49,25,98,32}; */
      List<String> list = Arrays.stream(arr).boxed()
                                .map(s -> s + "")
                                .filter(s -> s.startsWith("1"))
                                .collect(Collectors.toList());

    System.out.println(list);
    }
}

Output:
10, 15
```

##### **3. How to find duplicate elements in a given integers list in java using Stream functions?**
```java
import java.util.*;
import java.util.stream.*;

/* This will give the output of all repeated number */
public class DuplicateElements {
  public static void main(String args[]) {
          List<Integer> myList = Arrays.asList(10,15,8,49,25,98,98,32,15);
          Set<Integer> set = new HashSet();
          myList.stream()
                .filter(n -> !set.add(n))
                .forEach(System.out::println);
  }
}

Output:
98, 15 /* Example 98 and 15 are the only repeated values in the list */



/* Way 1 - Gives list of all distinct/unique values */

public static void getDataWithoutDuplicates() {
     List<Integer> myList = Arrays.asList(1, 1, 85, 6, 2, 3, 65, 6, 45, 45, 5662, 2582, 2, 2, 266, 666, 656);
     myList.stream().distinct().forEach(noDuplicateData -> System.out.println(noDuplicateData));
 }

Output : 1 85 6 2 3 65 45 5662 2582 266 666 656



/* Way 2 -  Gives list of all distinct/unique values */ 

public static void getDataWithoutDuplicates() {
      List<Integer> myList = Arrays.asList(1, 1, 85, 6, 2, 3, 65, 6, 45, 45, 5662, 2582, 2, 2, 266, 666, 656);
      Set<Integer> set = new HashSet<>(myList);
        
      // Convert the set back to a list if needed
      List<Integer> uniqueData = set.stream().collect(Collectors.toList());
        
      // Print the unique elements
      uniqueData.forEach(System.out::println);
  }

Output : 1 65 2 3 6 266 45 656 85 2582 666 5662

/* Way 3 - Gives list of all distinct/unique values */

/* When numbers are given as Array int[] arr = {10,15,8,49,25,98,98,32,15}; */

List<Integer> list = Arrays.stream(arr).boxed().distinct()
.collect(Collectors.toList());
```

##### **4. Given the list of integers, find the first element of the list using Stream functions?**
```java
import java.util.*;
import java.util.stream.*;

public class FindFirstElement{
  public static void main(String args[]) {
          List<Integer> myList = Arrays.asList(10,15,8,49,25,98,98,32,15);
          myList.stream()
                .findFirst()
                .ifPresent(System.out::println);

      /* or can also try below single line code */
      /* When numbers are given as Array int[] arr = {10,15,8,49,25,98,98,32,15}; */
      Arrays.stream(arr).boxed().findFirst().ifPresent(System.out::print);
  }
}

Output:
10
```


##### **5. Given a list of integers, find the total number of elements present in the list using Stream functions?**
```java
import java.util.*;
import java.util.stream.*;

public class FindTheTotalNumberOfElements{
  public static void main(String args[]) {
          List<Integer> myList = Arrays.asList(10,15,8,49,25,98,98,32,15);
          long count =  myList.stream()
                              .count();
          System.out.println(count);   

/* or can also try below line code */
/* When numbers are given as Array int[] arr = {10,15,8,49,25,98,98,32,15}; */
      Arrays.stream(arr).boxed().count();             
  }
}

Output:
9
```

##### **6. Given a list of integers, find the maximum value element present in it using Stream functions?**
```java
import java.util.*;
import java.util.stream.*;

public class FindMaxElement{
  public static void main(String args[]) {
          List<Integer> myList = Arrays.asList(10,15,8,49,25,98,98,32,15);
          int max =  myList.stream()
                           .max(Integer::compare)
                           .get();
          System.out.println(max);   

/* or we can try using below way */
/* When numbers are given as Array int[] arr = {10,15,8,49,25,98,98,32,15}; */

        int maxdata = Arrays.stream(arr).boxed()
                            .max(Comparator.naturalOrder()).get(); 

        System.out.println(maxdata);              
  }
}

Output:
98
```


##### **7. Given a String, find the first non-repeated character in it using Stream functions?**
```java
import java.util.*;
import java.util.stream.*;
import java.util.function.Function;

public class FirstNonRepeated{
  public static void main(String args[]) {
    String input = "Java articles are Awesome";
    
    Character result = input.chars() // Stream of String       
            .mapToObj(s -> Character.toLowerCase(Character.valueOf((char) s))) // First convert to Character object and then to lowercase         
            .collect(Collectors.groupingBy(Function.identity(), LinkedHashMap::new, Collectors.counting())) //Store the chars in map with count 
            .entrySet()
            .stream()
            .filter(entry -> entry.getValue() == 1L)
            .map(entry -> entry.getKey())
            .findFirst()
            .get();
    System.out.println(result); 

    /* or can also try using */  
    
  input.chars().mapToObj(c -> (char) c)
               .filter(ch -> input.indexOf(ch) == input.lastIndexOf(ch))
               .findFirst().orElse(null);                 
    }
}

Output:
j
```


##### **8. Given a String, find the first repeated character in it using Stream functions?**
```java
import java.util.*;
import java.util.stream.*;
import java.util.function.Function;

public class FirstRepeated{
  public static void main(String args[]) {
          String input = "Java Articles are Awesome";

          Character result = input.chars() // Stream of String       
                                  .mapToObj(s -> Character.toLowerCase(Character.valueOf((char) s))) // First convert to Character object and then to lowercase         
                                  .collect(Collectors.groupingBy(Function.identity(), LinkedHashMap::new, Collectors.counting())) //Store the chars in map with count 
                                  .entrySet()
                                  .stream()
                                  .filter(entry -> entry.getValue() > 1L)
                                  .map(entry -> entry.getKey())
                                  .findFirst()
                                  .get();
          System.out.println(result);    

    /* or can also try */

        Set<Character> seenCharacters = new HashSet<>();

        return input.chars()
                    .mapToObj(c -> (char) c)
                    .filter(c -> !seenCharacters.add(c))
                    .findFirst()
                    .orElse(null);                
  }
}  


Output:
a
```

##### **9. Given a list of integers, sort all the values present in it using Stream functions?**
```java
import java.util.*;
import java.util.stream.*;
import java.util.function.Function;

public class SortValues{
  public static void main(String args[]) {
          List<Integer> myList = Arrays.asList(10,15,8,49,25,98,98,32,15);

           myList.stream()
                 .sorted()
                 .forEach(System.out::println);

        /* Or can also try below way */
      /* When numbers are given as Array int[] arr = {10,15,8,49,25,98,98,32,15}; */

        Arrays.stream(arr).boxed().sorted().collect(Collectors.toList())
  }
}

Output:
 8
10
15
15
25
32
49
98
98
```


##### **10. Given a list of integers, sort all the values present in it in descending order using Stream functions?**
```java
import java.util.*;
import java.util.stream.*;
import java.util.function.Function;

public class SortDescending{
  public static void main(String args[]) {
          List<Integer> myList = Arrays.asList(10,15,8,49,25,98,98,32,15);

           myList.stream()
                 .sorted(Collections.reverseOrder())
                 .forEach(System.out::println);
  }
}

Output:
98
98
49
32
25
15
15
10
8
```

##### **11. Given an integer array nums, return true if any value appears at least twice in the array, and return false if every element is distinct.**
```java
public boolean containsDuplicate(int[] nums) {
    List<Integer> list = Arrays.stream(nums)
                               .boxed()
                               .collect(Collectors.toList());
    Set<Integer> set = new HashSet<>(list);
     if(set.size() == list.size()) {
       return false;
   } 
      return true;

/* or can also try below way */ 
    Set<Integer> setData = new HashSet<>();
        return Arrays.stream(nums)
                     .anyMatch(num -> !setData.add(num));
  }

Input: nums = [1,2,3,1]
Output: true

Input: nums = [1,2,3,4]
Output: false

```

##### **12. How will you get the current date and time using Java 8 Date and Time API?**
```java
class Java8 {
    public static void main(String[] args) {
        System.out.println("Current Local Date: " + java.time.LocalDate.now());
        //Used LocalDate API to get the date
        System.out.println("Current Local Time: " + java.time.LocalTime.now());
        //Used LocalTime API to get the time
        System.out.println("Current Local Date and Time: " + java.time.LocalDateTime.now());
        //Used LocalDateTime API to get both date and time
    }
}
```


##### **13. Write a Java 8 program to concatenate two Streams?**
```java
import java.util.Arrays;
import java.util.List;
import java.util.stream.Stream;
 
public class Java8 {
    public static void main(String[] args) {
 
        List<String> list1 = Arrays.asList("Java", "8");
        List<String> list2 = Arrays.asList("explained", "through", "programs");
 
        Stream<String> concatStream = Stream.concat(list1.stream(), list2.stream());
         
        // Concatenated the list1 and list2 by converting them into Stream
 
        concatStream.forEach(str -> System.out.print(str + " "));
         
        // Printed the Concatenated Stream
         
    }
}
```

##### **14. Java 8 program to perform cube on list elements and filter numbers greater than 50.**
```java
import java.util.*;

public class Main {
    public static void main(String[] args) {
       List<Integer> integerList = Arrays.asList(4,5,6,7,1,2,3);
       integerList.stream()
                  .map(i -> i*i*i)
                  .filter(i -> i>50)
                  .forEach(System.out::println);
    }
}  

Output:
64
125
216
343
```

##### **15. Write a Java 8 program to sort an array and then convert the sorted array into Stream?**
```java
import java.util.Arrays;
 
public class Java8 {
 
    public static void main(String[] args) {
        int arr[] = { 99, 55, 203, 99, 4, 91 };
        Arrays.parallelSort(arr);
        // Sorted the Array using parallelSort()
         
        Arrays.stream(arr).forEach(n > System.out.print(n + " "));
        /* Converted it into Stream and then
           printed using forEach */
    }
}
```

##### **16. How to use map to convert object into Uppercase in Java 8?**
```java
public class Java8 {
 
    public static void main(String[] args) {
        List<String> nameLst = names.stream()
                                    .map(String::toUpperCase)
                                    .collect(Collectors.toList());
        System.out.println(nameLst);
    }
}

output:
AA, BB, CC, DD
```

##### **17. How to convert a List of objects into a Map by considering duplicated keys and store them in sorted order?**
```java
public class TestNotes {

    public static void main(String[] args) {

    List<Notes> noteLst = new ArrayList<>();
    noteLst.add(new Notes(1, "note1", 11));
    noteLst.add(new Notes(2, "note2", 22));
    noteLst.add(new Notes(3, "note3", 33));
    noteLst.add(new Notes(4, "note4", 44));
    noteLst.add(new Notes(5, "note5", 55));

    noteLst.add(new Notes(6, "note4", 66));


    Map<String, Long> notesRecords = noteLst.stream()
                                            .sorted(Comparator
                                            .comparingLong(Notes::getTagId)
                                            .reversed()) // sorting is based on TagId 55,44,33,22,11
                                            .collect(Collectors.toMap
                                            (Notes::getTagName, Notes::getTagId,
                                            (oldValue, newValue) -> oldValue,LinkedHashMap::new));
// consider old value 44 for dupilcate key
// it keeps order
        System.out.println("Notes : " + notesRecords);
    }
}
```



##### **18. How to count each element/word from the String ArrayList in Java8?**
```java
public class TestNotes {

    public static void main(String[] args) {
        List<String> names = Arrays.asList("AA", "BB", "AA", "CC");
        Map<String,Long> namesCount = names
                                .stream()
                                .collect(
                                 Collectors.groupingBy(
                                  Function.identity(), Collectors.counting()));
        System.out.println(namesCount);
  }
}

Output:
{CC=1, BB=1, AA=2}
```

##### **19. How to find only duplicate elements with its count from the String ArrayList in Java8?**
```java
public class TestNotes {

    public static void main(String[] args) 
      List<String> names = Arrays.asList("AA", "BB", "AA", "CC");
      Map<String,Long> namesCount = names
                                   .stream()
                       .filter(x->Collections.frequency(names, x)>1)
                       .collect(Collectors.groupingBy
                       (Function.identity(), Collectors.counting()));
      System.out.println(namesCount);

/*or you can also try using  */

Map<String, Long> namesCount = names.stream()
                .collect(Collectors.groupingBy(Function.identity(), Collectors.counting()))
                .entrySet()
                .stream()
                .filter(entry -> entry.getValue() > 1)
                .collect(Collectors.toMap(Map.Entry::getKey, Map.Entry::getValue));
  }
}

Output:
{AA=2}
```


##### **20. How to check if list is empty in Java 8 using Optional, if not null iterate through the list and print the object?**
```java
Optional.ofNullable(noteLst)
            .orElseGet(Collections::emptyList) // creates empty immutable list: [] in case noteLst is null
            .stream().filter(Objects::nonNull) //loop throgh each object and consider non null objects
            .map(note -> Notes::getTagName) // method reference, consider only tag name
            .forEach(System.out::println); // it will print tag names
```


##### **21. Write a Program to find the Maximum element in an array?**
```java
public static int findMaxElement(int[] arr) {
  return Arrays.stream(arr).max().getAsInt();
}

Input: 12,19,20,88,00,9
output: 88
```



##### **22. Write a program to print the count of each character in a String?**
```java
public static void findCountOfChars(String s) {
Map<String, Long> map = Arrays.stream(s.split(""))
                              .map(String::toLowerCase)
                              .collect(Collectors
                              .groupingBy(str -> str, 
                                LinkedHashMap::new, Collectors.counting()));

// or you can also try using Function.identify() instead of LinkedHashMap

Map<String, Long> mapObject = Arrays.stream(s.split(""))
                                    .map(String::toLowerCase)
   .collect(Collectors.groupingBy(Function.identity(), Collectors.counting()));


}

Input: String s = "string data to count each character";
Output: {s=1, t=5, r=3, i=1, n=2, g=1,  =5, d=1, a=5, o=2, c=4, u=1, e=2, h=2}
```



##### **23. Find the longest string in a list of strings using Java streams:**
```java
List<String> strings = Arrays
              .asList("apple", "banana", "cherry", "date", "grapefruit");
Optional<String> longestString = strings
              .stream()
              .max(Comparator.comparingInt(String::length));
```

##### **24. Calculate the average age of a list of Person objects using Java streams:**
```java
List<Person> persons = Arrays.asList(
    new Person("Alice", 25),
    new Person("Bob", 30),
    new Person("Charlie", 35)
);
double averageAge = persons.stream()
                          .mapToInt(Person::getAge)
                          .average()
                          .orElse(0);
```

##### **25. Check if a list of integers contains a prime number using Java streams:**
```java
public boolean isPrime(int number) {
  if (number <= 1) {
    return false;
  }
  for (int i = 2; i <= Math.sqrt(number); i++) {
    if (number % i == 0) {
        return false;
    }
  }
  return true;
}
 
private void printPrime() {
  List<Integer> numbers = Arrays.asList(2, 4, 6, 8, 10, 11, 12, 13, 14, 15);
  boolean containsPrime = numbers.stream()
                                 .anyMatch(this::isPrime);
  System.out.println("List contains a prime number: " + containsPrime);

 }
```

##### **26. Merge two sorted lists into a single sorted list using Java streams:**
```java
List<Integer> list1 = Arrays.asList(1, 3, 5, 7, 9);
List<Integer> list2 = Arrays.asList(2, 4, 6, 8, 10);
List<Integer> mergedList = Stream.concat(list1.stream(), list2.stream())
                                .sorted()
                                .collect(Collectors.toList());
```



##### **27. Find the intersection of two lists using Java streams:**
```java
List<Integer> list1 = Arrays.asList(1, 2, 3, 4, 5);
List<Integer> list2 = Arrays.asList(3, 4, 5, 6, 7);
List<Integer> intersection = list1.stream()
                                  .filter(list2::contains)
                                  .collect(Collectors.toList());
```

##### **28. Remove duplicates from a list while preserving the order using Java streams:**
```java
List<Integer> numbersWithDuplicates = Arrays.asList(1, 2, 3, 2, 4, 1, 5, 6, 5);
List<Integer> uniqueNumbers = numbersWithDuplicates
                                       .stream()
                                       .distinct()
                                       .collect(Collectors.toList());
```



##### **29. Given a list of transactions, find the sum of transaction amounts for each day using Java streams:**
```java
List<Transaction> transactions = Arrays.asList(
    new Transaction("2022-01-01", 100),
    new Transaction("2022-01-01", 200),
    new Transaction("2022-01-02", 300),
    new Transaction("2022-01-02", 400),
    new Transaction("2022-01-03", 500)
);

Map<String, Integer> sumByDay = transactions
                        .stream()
                        .collect(Collectors.groupingBy(Transaction::getDate,
                               Collectors.summingInt(Transaction::getAmount)));
```

##### **30. Find the kth smallest element in an array using Java streams:**
```java
int[] array = {4, 2, 7, 1, 5, 3, 6};
int k = 3; // Find the 3rd smallest element
int kthSmallest = Arrays.stream(array)
                       .sorted()
                       .skip(k - 1)
                       .findFirst()
                       .orElse(-1);
```


##### **31. Given a list of strings, find the frequency of each word using Java streams:**
```java
List<String> words = Arrays.asList("apple", "banana", "apple", "cherry", 
                                    "banana", "apple");
Map<String, Long> wordFrequency = words
              .stream()
              .collect(Collectors
                    .groupingBy(Function.identity(), Collectors.counting())
                );
```

##### **32. Implement a method to partition a list into two groups based on a predicate using Java streams:**
```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9);
Map<Boolean, List<Integer>> partitioned = numbers
                        .stream()
                        .collect(Collectors.partitioningBy(n -> n % 2 == 0));
List<Integer> evenNumbers = partitioned.get(true);
List<Integer> oddNumbers = partitioned.get(false);
System.out.println("Even numbers: " + evenNumbers);
System.out.println("Odd numbers: " + oddNumbers);
```

##### **33. Find Product with Maximum Quantity Given a Map<String, Integer> where keys are product names and values are quantities, find the product with the maximum quantity.**
```java
import java.util.*;

public class MaxProduct {
    public static void main(String[] args) {
        Map<String, Integer> products = Map.of(
                "Laptop", 50,
                "Phone", 120,
                "Tablet", 80
        );

        Map.Entry<String, Integer> maxProduct =
                products.entrySet()
                        .stream()
                        .max(Map.Entry.comparingByValue())
                        .orElseThrow();

        System.out.println("Max Product: " + maxProduct.getKey() +
                           " -> " + maxProduct.getValue());
    }
}
```


##### **34. Filter Employees by Salary.  From a Map<String, Double> of employee → salary, filter employees earning more than 50,000.**
```java
import java.util.*;
import java.util.stream.Collectors;

public class HighSalary {
    public static void main(String[] args) {
        Map<String, Double> employees = Map.of(
                "Alice", 48000.0,
                "Bob", 52000.0,
                "Charlie", 70000.0
        );

        Map<String, Double> highEarners =
                employees.entrySet()
                        .stream()
                        .filter(e -> e.getValue() > 50000)
                        .collect(Collectors.toMap(
                                Map.Entry::getKey,
                                Map.Entry::getValue
                        ));

        System.out.println(highEarners);
    }
}
```



##### **35. Top 3 Most Populated Cities.  Given a Map<String, Integer> of city → population, find the top 3 most populated cities.**
```java
import java.util.*;
import java.util.stream.Collectors;

public class TopCities {
    public static void main(String[] args) {
        Map<String, Integer> cityPopulation = Map.of(
                "Delhi", 30000000,
                "Mumbai", 20000000,
                "Bangalore", 15000000,
                "Chennai", 12000000,
                "Kolkata", 14000000
        );

        List<Map.Entry<String, Integer>> top3 =
                cityPopulation.entrySet()
                        .stream()
                        .sorted(Map.Entry.<String, Integer>comparingByValue().reversed())
                        .limit(3)
                        .collect(Collectors.toList());

        System.out.println("Top 3 Cities: " + top3);
    }
}
```


##### **36. Convert Prices from USD to INR.  Given a Map<String, Integer> of item → price in USD, convert all prices to INR (1 USD = 85 INR).**
```java
import java.util.*;
import java.util.stream.Collectors;

public class PriceConvert {
    public static void main(String[] args) {
        Map<String, Integer> pricesUSD = Map.of(
                "Book", 10,
                "Pen", 2,
                "Laptop", 800
        );

        Map<String, Integer> pricesINR =
                pricesUSD.entrySet()
                        .stream()
                        .collect(Collectors.toMap(
                                Map.Entry::getKey,
                                e -> e.getValue() * 85
                        ));

        System.out.println(pricesINR);
    }
}

```


##### **37. Sort Map by Values. Sort a Map<String, Integer> (student → marks) in descending order of marks.**
```java
import java.util.*;
import java.util.stream.Collectors;

public class SortMap {
    public static void main(String[] args) {
        Map<String, Integer> marks = Map.of(
                "Ravi", 88,
                "Priya", 95,
                "Anil", 72
        );

        LinkedHashMap<String, Integer> sorted =
                marks.entrySet()
                        .stream()
                        .sorted(Map.Entry.<String, Integer>comparingByValue().reversed())
                        .collect(Collectors.toMap(
                                Map.Entry::getKey,
                                Map.Entry::getValue,
                                (e1, e2) -> e1,
                                LinkedHashMap::new
                        ));

        System.out.println(sorted);
    }
}
```

##### **38. Total Units Sold. Given a Map<String, Integer> of product → units sold, find the total units sold.**

```java
import java.util.*;

public class TotalUnits {
    public static void main(String[] args) {
        Map<String, Integer> sales = Map.of(
                "Shoes", 30,
                "Bags", 20,
                "Shirts", 50
        );

        int total = sales.values()
                         .stream()
                         .mapToInt(Integer::intValue)
                         .sum();

        System.out.println("Total Units Sold = " + total);
    }
}
```





