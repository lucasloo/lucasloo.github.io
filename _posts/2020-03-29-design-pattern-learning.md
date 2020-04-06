# Design Patterns

Learning notes based on https://java-design-patterns.com/ & https://design-patterns.readthedocs.io/zh_CN/latest/index.html

Design patterns are the best formalized practices a programmer can use to avoid common problems.

## Creational Pattern

### Singleton

Ensures that only one object of a particular class is ever created.

- Non-thread-safe lazy-loading

  - ```java
    public class SingletonDemo {
        private static SingletonDemo instance;
        private SingletonDemo(){
        }
        public static SingletonDemo getInstance(){
            if(instance == null){
                instance = new SingletonDemo();
            }
            return instance;
        }
    }
    ```

- Thread-safe lazy-loading with synchronized 

  - ```java
    public class SingletonDemo {
        private static SingletonDemo instance;
        private SingletonDemo(){
    
        }
        public static synchronized SingletonDemo getInstance(){
            if(instance == null){
                instance = new SingletonDemo();
            }
            return instance;
        }
    }
    ```

  - thread safe but the whole ` getInstance()` method is synchronized.  Every time you try to get the instance, the method will be locked.

- Thread-safe lazy-loading with double checking

  - ```java
    public class SingletonDemo {
        private static SingletonDemo instance;
        private SingletonDemo(){
        }
        public static SingletonDemo getInstance(){
            if(instance == null){
                synchronized (SingletonDemo.class){
                    if(instance == null){
                        instance = new SingletonDemo();
                    }
                }
            }
            return instance;
        }
    }
    ```

  - thread safe, and only the initiation is synchronized. After initiation, the method will be never locked again.

- Thread-safe non-lazy-loading

  - ```java
    public class SingletonDemo {
        private static SingletonDemo instance = new SingletonDemo();
        private SingletonDemo(){
    
        }
        public static SingletonDemo getInstance(){
            return instance;
        }
    }
    ```

  - initialize at the when class is loaded

- Static inner class (Thread-safe lazy-loading)

  - ```java
    public class SingletonDemo {
        private static class SingletonHolder{
            private static SingletonDemo instance=new SingletonDemo();
        }
        private SingletonDemo(){
            System.out.println("Singleton has loaded");
        }
        public static SingletonDemo getInstance(){
            return SingletonHolder.instance;
        }
    }
    ```

  - Would not initialize when class is loaded

- Enum (Thread-safe non-lazy-loading)

  - ```java
    enum SingletonDemo{
        INSTANCE;
        public void otherMethods(){
            System.out.println("Something");
        }
    }
    
    
    public class Hello {
        public static void main(String[] args){
            SingletonDemo.INSTANCE.otherMethods();
        }
    }
    
    
    ```

  - **Different from above**, this method has no serialized reserialized problems (serialized object of the singleton will become another object after deserialization. Can be resolved by providing a `readResolve()` method. see https://codepumpkin.com/serialization-singleton-design-pattern/).

  - > A single-element enum type is the best way to implement a singleton

    according to Joshua Bloch, Effective Java 2nd Edition p.18



### Simple Factory Pattern

Simple factory pattern, aka static factory method, represents a pattern that a static factory creates different products based on different parameters.

![../_images/SimpleFactory.jpg](https://design-patterns.readthedocs.io/zh_CN/latest/_images/SimpleFactory.jpg)

- Product: An abstract product which describes the common behavior of all actual products.

  - ```java
    public interface Product {
      public void use();
    }
    ```

- ConcreteProductA: An actual product

  - ```java
    public class ConcreteProductA implements Product {
      @Override
      public void use() {
        // behavior A
      }
    }
    ```

- ConcreteProductB: An actual product

  - ```java
    public class ConcreteProductB implements Product {
      @Override
      public void use() {
        // behavior B
      }
    }
    ```

- Factory: in charge of creating all products

  - ```java
    public class Factory {
      public static Product getProduct(String productName) {
        if ("A".equals(productName)) {
          return new ConcreteProductA();
        }
        if ("B".equals(productName)) {
          return new ConcreteProductB();
        }
        return null;
      }
    }
    ```

#### Pros

- Users do not need to know how to create the products.
- Users do not need to know the exact class they are creating, given the right parameters, the product with the expected behavior will be delivered to the user.

#### Cons

- Factory has too much responsibility. The complexity of the factory will increase with the growth of product amount.
- Hard to scale up. Every time you create a different type of product, you have to change the logic in the factory.



### Factory method pattern

Define an interface for creating **an** object, but let the subclasses decide which class to instantiate.

Differs from Simple factory pattern, different products are created by different factories.

![../_images/FactoryMethod.jpg](https://design-patterns.readthedocs.io/zh_CN/latest/_images/FactoryMethod.jpg)

- Product: An abstract product which describes the common behavior of all actual products.

  - ```java
    public interface Product {
      public void use();
    }
    ```

- ConcreteProduct: An actual product

  - ```java
    public class ConcreteProduct implements Product {
      @Override
      public void use() {
        // behavior
      }
    }
    ```

- Factory: An abstract factory

  - ```java
    public interface Factory {
      public Product factoryMethod();
    }
    ```

- ConcreteFactory: An actual factory

  - ```java
    public class ConcreteFactory implements Factory {
      @Override
      public Product factoryMethod() {
        return new ConcreteProduct();
      }
    }
    ```

#### Pros

- All the pros for **Simple Factory Pattern**.
- Complexity of the Factory is spread through the concrete factories.
- Easy to scale up, just need to add new concrete products and concrete factories which implement the same interface.

#### Cons

- More classes for the system to process, increase the complexity of the system.
- System will be more abstract and harder to understand.



### Abstract Factory Pattern

Provide an interface for creating **families** of related or dependent objects without specifying their concrete classes.

![../_images/AbatractFactory.jpg](https://design-patterns.readthedocs.io/zh_CN/latest/_images/AbatractFactory.jpg)

- Differences between Factory Method and Abstract Factory (https://dzone.com/articles/factory-method-vs-abstract)
  - **One product vs Set of many**
  - **Inheritance vs Composition** (CONFUSING)
  - **Method vs full class (object)**
    - Factory Method is *just a method* while Abstract Factory is an *object*. The *purpose* of a Class having factory method is *not just create* objects, it does other work also, *only a method is responsible* for *creating* object. In Abstract Factory, the *whole purpose* of the class is to *create family of objects*.
  - **Level of abstraction**
    - Abstract Factory is one level *higher in abstraction* than Factory Method. Factory Method *abstracts the way objects are created*, while Abstract Factory also *abstracts the way factories are created* which in turn abstracts the way objects are created.

### Builder

Allows you to create different flavors of an object while avoiding **constructor pollution**(too many parameters).

```java
public Hero(Profession profession, String name, HairType hairType, HairColor hairColor, Armor armor, Weapon weapon) {
}
```

 ![../_images/Builder.jpg](https://design-patterns.readthedocs.io/zh_CN/latest/_images/Builder.jpg)

- Product:

  - ```java
    public class Product {
      private Object partA;
      private Object partB;
    }
    ```

- Builder: Abstract builder

  - ```java
    public interface Builder {
      private Product product;
      public void buildPartA(Object paramA);
      public void buildPartB(Object paramB);
      public Product getResult();
    }
    ```

- ConcreteBuilder: Actual builder

  - ```java
    public class ConcreteBuilder implements Builder {
      private Product product = new Product();
      public void buildPartA(Object paramA) {
        prodcut.setPartA(paramA);
      }
      public void buildPartB(Object paramB) {
        product.setPartB(paramB);
      }
      public Product getResult() {
        return product;
      }
    }
    ```

- Director: 

  - ```java
    public class Director {
      private Builder builder;
      public Director (Builder builder) {
        this.builder = builder;
      }
      public void setBuilder(Builder builder) {
        this.builder = builder;
      }
      public Product construct() {
        return builder.getResult();
      }
    }
    ```

- Usage:

  - ```java
    Builder builderA = new ConcreteBuilder();
    builderA.buildPartA(new Object());
    Builder builderB = new ConcreteBuilder();
    builderB.buildPartB(new Object());
    Director director = new Director();
    // use builderA to construct productA
    director.setBuilder(builderA);
    Product productA = director.construct();
    // use builderB to construct productB
    director.setBuilder(builderB);
    Product productB = director.construct();
    ```



## Structural Patterns

### Adapter

Adapter pattern lets you wrap an otherwise incompatible object in an adapter to make it compatible with another class.



