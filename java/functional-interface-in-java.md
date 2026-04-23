Function interfaces refer to interfaces that have exactly one abstract method. They are a key feature of Java's functional programming capabilities, introduced in Java 8. Functional interfaces are designed to be used with lambda expressions, method references, and constructor references to enable cleaner and more concise code.

<!--more-->

## Key Characteristics of Functional Interfaces:
1. **Single Abstract Method (SAM):** They have exactly one abstract method. This is why they are also called SAM interfaces.
2. **@FunctionalInterface Annotation (Optional):** While not mandatory, using the @FunctionalInterface annotation ensures that the interface has only one abstract method. If additional abstract methods are added, the compiler will throw an error.
3. **Integration with Lambdas:** Functional interfaces allow you to pass behavior as an argument using lambda expressions.
4. A functional interface is an interface with a single abstract method (SAM) but may also include:
   - Default methods.
   - Static methods.
   - Methods inherited from Object (e.g., toString, equals, hashCode).

## Functional Interface Rules

1. @FunctionalInterface Annotation (Optional but Recommended):

   - Ensures the interface remains a functional interface.
   - Prevents accidental addition of more abstract methods.

2. Inheritance Rules:
   - A functional interface can extend another interface if the result still has only one abstract method.

## Built-in Functional Interfaces
| Interface               | Description                                      |
|------------------------|--------------------------------------------------|
| Function<T, R>         | Transforms an input of type T to type R.         |
| BiFunction<T, U, R>    | Transforms two inputs (T, U) to type R.          |
| Consumer<T>            | Performs an action on T without returning.       |
| BiConsumer<T, U>       | Performs an action on two inputs.                |
| Supplier<T>            | Supplies a value of type T.                      |
| Predicate<T>           | Tests a condition on T (returns boolean).        |
| BiPredicate<T, U>      | Tests a condition on two inputs.                 |
| UnaryOperator<T>       | A specialized Function for transformations.      |
| BinaryOperator<T>      | A specialized BiFunction for combining.          |

## Advantages of Functional Interfaces
- **Concise Code:** Lambdas and method references to reduce boilerplate code.

- **Higher-Order Functions:** Enable passing functions as arguments.

- **Enhanced Readability:** Code becomes more declarative and expressive.

- **Functional Programming Paradigm:** Enables concepts like immutability and stateless computations.

