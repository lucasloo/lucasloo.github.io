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
- Hard to scale up. Every time you create a new products, you have to change the logic in the facotory.