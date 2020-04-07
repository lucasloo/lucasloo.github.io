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

- an alternatice is the JavaBeans patterns. Using getter and setter will make it easy. But a JavaBean my be in an inconsistent sate partway through its construction. Precludes the possibility of making a class immutable and requires extra effort to ensure thread safety.

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