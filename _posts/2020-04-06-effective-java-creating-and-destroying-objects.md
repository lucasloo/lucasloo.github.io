## learning notes

## Item 1: Consider static factory methods instead of constructors

example

```java
public static Boolean valueOf(boolean b) {
  return b ? Boolean.TRUE : Boolean.FALSE;
}
```

instead of 

```java
public Boolean(boolean value) {
  this.value = value;
}
```

### pros

1. they have names.
2. do not need to return a new object each time they are invoked. reduce unnecessary duplicate objects.
3. allow return subtypes(interface-based).
4. return type can vary from call to call. eg `EnumSet` return `RegularEnumSet` if more than 64 elements, otherwise `JumboEnumSet` instead. 

### cons

1. without public or protected constructor, the class cannot be subclassed.

2. name hard to find.

   

## Item2: Consider a builder when faced with many constructor parameters

- the telescoping constructor pattern works, but it is hard to write client code when there are too many parameters.

  ```java
  public Constructor(Object A, Object B ...) {
  	... 
  }
  Constructor constructor = new Constructor(A, B ...);
  ```

- an alternative is the JavaBeans patterns. Using getter and setter will make it easy. But a JavaBean my be in an inconsistent sate partway through its construction. Precludes the possibility of making a class immutable and requires extra effort to ensure thread safety.

- Builder Pattern

  - easy to read

  - well suited to class hierarchies(with the help of a builder of ***generic type*** with a ***recursive type*** parameter, also known as the ***the simulated self-type*** idiom)

    - [ ] :warning: need to check generic type and this simulated self-type thing out, below is very confusing

    ```java
    public abstract class Pizza {
      public enum Topping {HAM, MUSHROOM, ONION};
      final Set<Topping> toppings;
      
      abstract static class Builder<T extends Builder<T>> {
        public T addTopping(Topping topping) {
          toppings.add(topping);
          return self();
        }
        abstract Pizza build();
        protected abstract T self();
      }
      
      Pizza(Builder<?> builder) {
        toppings = builder.toppings.clone();
      }
    }
    ```

    ```java
    public class NyPizza extends Pizza {
      public enum Size {LARGE, MID, SMALL};
      final Size size;
      
      public static class Builder extends Pizza.Builder<Builder> {
        private final Size size;
        
        public Builder(Size size) {
          this.size = size;
        }
        @Override
        public NyPizza build() {
          return new NyPizza(this);
        }
        @Override
        protected Builder self() {
          return this;
        }
      }
      
      private NyPizza(Builder builder) {
        super(this);
        size = builder.size;
      }
    }
    
    public class Calzone extends Pizza {
      private final boolean sauceInside;
      
      public static class Builder extends Pizza.Builder<Builder> {
        private final boolean sauceInside = false;
        
        public Builder sauceInside() {
          this.sauceInside = true;
          return this;
        }
        @Override
        public Calzone build() {
          return new Calzone(this);
        }
        @Override
        protected Builder self() {
          return this;
        }
      }
      
      private Calzone(Builder builder) {
        super(builder);
        sauceInside = builder.sauceInside;
      }
    }
    ```

    - [ ] :warning: different return type is consider override??

    ```java
    NyPizza pizza = new NyPizza.Builder(SMALL).addTopping(SAUSAGE).addTopping(ONION).build();
    Calzone calzone = new Calzone.Builder().addTopping(HAM).addTopping(ONION).sauceInside().build();
    ```

  - Cons

    - extra cost creating builder (in performance-critical situation)

## Item3: Enforce the singleton property with a private constructor or an enum type

check the design pattern learning singleton part.

## Item4: Enforce noninstantiability with a private constructor

A class with a grouping of static methods and static fields such as a utility class or a factory. Such classes were not designed to be instantiated. 

Attempting to enforce noninstantiability by making a class abstract does not work. Subclasses can be instantiated. And it mislead people into thinking the class was designed to inheritance.

Including a private constructor can make a class noninstantiable.

```java
public class UtilityClass {
    private UtilityClass() {
        throw new AssertionError();
    }
}
```

- **private constructor** can suppress default constructor, hence outside of the class will not be able to new it.
- **throw AssertionError** provides insurance in case the constructor is accidentally invoked from within the class.

- **good side effect**: this idiom prevents the class from being subclassed because there is no default constructor available in UtilityClass.

## Item5: Prefer dependency injection to hardwiring resources

Static utility classes and singletons are inappropriate for  classes whose behavior is parameterized by an underlying resource.

In order to support multiple instances of the class, a simple pattern called **Dependency Injection** can be used. It means passing the resource into the constructor when creating a new instance. **Dependency Injection** is applicable to **constructors**, **static factories**, and **builders**.

Methods above required passing resource directly. Another useful variant of the pattern is to pass a resource factory to the constructor. These factories embody the **Factory Method Pattern**.(check the `Supplier<T>` interface in JAVA8)

## Item6: Avoid creating unnecessary objects

1. cache the expensive **immutable** object

   ```java
   static boolean isRomanNumeral(String s) {
       return s.matches("...");
   }
   ```

   the problem is that `String.matches()` internally creates a `Pattern` instance every it is invoked.

   the right way should be:

   ```java
   public calss RomanNumerals {
       private static final Pattern ROMAN = Pattern.compile("...");
       static boolean isRomanNumeral(String s) {
           return ROMAN.matcher(s).matches();
       }
   }
   ```

   lazily initializing is not recommended. Since it would complicate the implementation with no measurable performance improvement.

2. cache the expensive **without states object**

   Consider **Adapter**. Because an adapter has no state beyond that of its backing object, there is no need to create more than one instance of a given adapter to a given object.

   For example, the `keySet()` method of the `map` interface returns a `Set` adapter of the `Map` object to show all the keys in the map. It do not need to create a set every time it is called because the set should be the same set and should always follow the states of the map itself.

3. autoboxing

   prefer primitives to boxed primitives, and watch out for unintentional autoboxing.

4. use a pool to maintain extremely heavyweight

   for example, database connection.

**VERY IMPORTANT**, small objects should always create than reuse. Creating additional objects is a good way to enhance the clarity, simplicity, or power of a program.