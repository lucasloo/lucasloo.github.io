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