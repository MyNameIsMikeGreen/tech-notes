# Functional Interfaces

In Java 8 syntax, functions are anonymous methods. They are typically represented using lambda syntax, although can be built using constructors.

`Function` is the main interface, representing any input type and producing any output type. The other interfaces are typically specialisations of `Function` taking different numbers of inputs or of different types.

A Functional Interface, at it's core, is simply an interface containing just a single method and is typically annotated with `@FunctionalInterface`. This means that is is possible to create a new one that defines what the input and output types should be by using the form:

```
@FunctionalInterface
public interface ShortToByteFunction {
    byte applyAsByte(short s);
}
```

The default functional interfaces are similar to the example above, but use generics. N.B: The built-in interfaces don't necessarily follow the definition of contianing only a single method as mentioned above.

## Primary Interfaces
| Functional Interface | Inputs (Type) | Outputs (Type)             | Use Cases |
|----------------------|---------------|----------------------------|-----------|
| `Function`           | 1 (Any)       | 1 (Any)                    | Mapping one value to another |
| `Supplier`           | 0             | 1 (Any)                    | Generating values based on data the supplier itself can fetch e.g. Using the local time |
| `Consumer`           | 1 (Any)       | 0                          | "Voidy" actions e.g. printing |
| `Predicate`          | 1 (Any)       | 1 (boolean - primative)    | Determining whether to filter a value |

## Primative Specialisations
As primitives cannot be used in generics, it is not possible to pass them as types for the typical interfaces. To combat this, specialised versions of these interfaces exist for each of the primary interfaces.

There exist interfaces where:
* The input is the named primative and the output is any class.
  * `IntFunction`
  * `LondFunction`
  * `DoubleFunction`
* The input is any class and the output is the named primative.
  * `ToIntFunction`
  * `ToLondFunction`
  * `ToDoubleFunction`
* The input is one of the named primatives and the output is the other named primative. Not every combination of primitives exist, only the most common.
  * `DoubleToIntFunction`
  * `DoubleToLongFunction`
  * `IntToDoubleFunction`

# Streams
Java 8 Streams are a way of performing actions in a functional manner on a group of similar elements. The elements are loaded into the stream, some number of operations are performed on the values, the stream is then terminated using one of several terminating methods. Streams are typically used for transformation of data, data filtering, or as a way to write existing loops in a more functional way.

## Creating a Stream
There are several ways to create a stream depending on where the source data is coming from.

**Adding values individually:**
```
Stream<String> stringStream = Stream.of("value1", "value2", "value3");
```

**From a collection (List, Set, etc):**
```
Collection<String> stringCollection = List.of("value1", "value2", "value3");
Stream<String> stringStream = myCollection.stream();
```

**From an array:**
```
String[] stringArray = {"value1", "value2", "value3"};
Stream<String> stringStream = Arrays.stream(stringArray);
```

**[IntStream] From a String:**
```
String string = "This is my String";
IntStream intStream = string.codePoints();
```

This creates a specialised implementation of a Stream that supports the `int` primitive. Each int element the Stream contains is the integer-representation of a char. IntStreams contain several specialised functions that apply only to this (and similar) specialised numerical-primive streams for basic data analysis (averages, min/max, etc).

**[IntStream] From a range:**
```
IntStream intStream = IntStream.range(0, 10);
```

## Non-Terminal Operations
These methods take in a single element from the stream, do *something*, then return a stream. In many cases, the stream that is returned contains a different group of elements, but it could reasonably return the exact same stream (for example, if it only did "voidy" stuff on the element).

* `map()`
  * Takes in a single element, returns a single element (perhaps of a different type).
  * e.g. `.map(string -> Integer.parseInt(string))`
* `mapToObj()`
  * Same as `.map()`, but for use in primative-streams (e.g. `IntStream`) to convert the element to an Object type.
* `mapToInt()`, `mapToLong()`, `mapToDouble()`
  * Same as `.mapToObj()`, but for use in object-streams to convert the element to the corresponding specialised primitve stream.
* `flatMap()`
  * Similar to `map()`, but instead of returning a single element, returns a Stream. Instead of the stream being inserted into the outer stream as a single object, the returned stream is unpacked - its elements are placed within the outer stream.
  * This is useful when some operation could reasonably return 0, 1, or many objects. In the case that it returns 0, an empty stream is returned and, as there is nothing to unpack, no nulls or empty objects polute the stream. The ability to return a stream with many elements provides the capability for the number of elements in the stream to grow.
  * e.g. `.flatMap(optional -> optional.stream())`
    * Returns a stream containing the unpacked optional value if present, else returns an empty stream.
* `filter()`
  * Takes in a single element, returns a boolean representing whether or not to keep the value in the stream.
  * e.g. `.filter(string -> string.startsWith("Donkey"))`
* `boxed()`
  * Converts a primitive stream (e.g. IntStream) to a boxed version.
  * e.g. `Stream<Integer> integerStream = IntStream.range(0, 10).boxed()`
* `distinct()`
  * Removes non-unique values from primitive streams.
  * e.g. `Arrays.stream(new int[]{1, 1, 2, 2, 3, 4}).distinct()`
    * Would return an IntStream with elements `1`, `2`, `3`, and `4`.

## Terminal Operations
These operations occur at the end of the list of operations as they do not return a stream. They either return a single value or they are voidy.

* `collect()`
  * Goes through the stream and collects them into some other container objects. Typically, this will be a list, a set, or some other collection object.
  * e.g. `.collect(Collectors.toList())`
  * e.g. `.collect(Collectors.joining(" "))`
    * A collector to concatenate CharSequence (e.g. String) elements together.
  * e.g. `.collect(StringBuilder::new, StringBuilder::appendCodePoint, StringBuilder::append)`
    * A way to use StringBuilder to form a string from something that otherwide wouldn't concatenate (e.g. ints).
* `min()`, `max()`
  * In primative streams, returns a single element from the stream that is the largest or smallest numerically.
    * e.g. `int min = IntStream.range(0, 10).min();`
  * In object streams, determines the largest/smallest element according the the Comparator passed to the method.
    * e.g. `String shortestString = Stream.of("myString", "myOtherString").min((string1, string2) -> string1.length() - string2.length());`
* `forEach()`
  * For each element in the stream, performs some voidy actions. Returns no value.
  * e.g. `Stream.of("myString", "myOtherString").forEach(string -> System.out.println(string));`
* `reduce()`
* `findFirst()`, `findAny()`

# Resources
* https://www.baeldung.com/java-8-functional-interfaces
