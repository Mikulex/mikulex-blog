---
date: 2021-11-16T13:12Z
title: Using Java Optionals
---

I stumbled upon something interesting and, in my opinion, pretty cool while learning more about Java and especially Java 8.

Something annoying you sometimes have to do is checking variables for being potentially `null`.
```java
MyType thing = null;

if(thing == null){
    // do something
} else {
    // do something else 
}
```
Having to check for null can get annoying, especially if you have to do it often, or if it results in nested if statements. Since Java 8 (and supplemented with more methods in Java 9) you can use a class called `Optional` which was built for handling objects that may end up being null.

I will go over various methods of the Optional class and talk a bit about what I think about how one could or should use it. But I recommend to also check the [docs](https://docs.oracle.com/javase/8/docs/api/java/util/Optional.html).

# Creating Optionals

The `Optional` class has static methods which enable you to create objects.

```java
Optional<String> stringOpt = Optional.of("My cool string");
Optional<Integer> intOpt = Optional.of(2);
Optional<String> emptyStringOpt = Optional.empty();
```
You can immediatly see that Optionals use generics which show the type it should represent. As for instantiating them, you use `Optional.of()` and pass an argument into it. Calling `Optional.empty()` will create an empty Optional, obviously, and would represent an object being null. Be careful about the `of()` method, since you can't pass `null` into it. Doing so will throw an Exception. But there's another method which covers this case called `ofNullable()`, in which you also pass an argument that may be null.
```java
MyClass thing = null;
Optional<MyClass> myOpt = Optional.ofNullable(thing);
```

# Using Optionals

If you have an Optional, you can do various things with it. You can check if your Optional actually holds a value or if it is empty, by calling the selfexplanatory methods `isEmpty()` and `isPresent()` on them.
```java
Optional<String> myString = Optional.empty();
if(myString.isEmpty()) {
    //doSomething
}
```
You could go ahead and work like that, but your Optional instances hold a few other methods which makes your code a bit shorter.
## orElse()
`orElse()` either returns the value inside your variable, or uses the argument you pass into it if your optional is empty.
```java
Optional<String> myString = Optional.empty();
String output = myString.orElse("Empty String!");
```

## orElseGet()
`orElseGet()` expects a `Supplier` which will be invoked lazily if your Optional is empty. In other words, your `Supplier` function will *only* be invoked *if and only if* your Optional is empty. That can be useful if creating an alternative object in case a value is missing is too costly to just call everytime.
```java
Optional<String> myString = Optional.empty();
String output = myString.orElseGet(() -> "Empty String!");
```

## orElseThrow()
Should be obvious. If it's empty, throw an Exception. Again a supplier is expected as an Argument.
```java
Optional<String> myString = Optional.empty();
String output = myString.orElseThrow(SomeException::new);
```


## or()
A bit different. Here you won't get the value itself returned, but another Optional containing the value.
```java
Optional<String> myString = Optional.empty();
Optional<String> anotherString = myString.or(() -> "Empty String!");
```

## ifPresentOrElse()
Here you won't get something returned. Instead, you can define code which should be run for when your Optional is either empty or not. `ifPresentOrElse()` expects two arguments. The first one is a `Consumer`, the second one a `Runnable`.
```java
Optional<String> myString = Optional.empty();
myString.ifPresentOrElse(
    (sentence) -> {
        System.out.println(sentence);
        },
    () -> {
        System.out.println("Empty String!");
        };
);
```

# Using Optionals properly
Optionals are good and all, but I think there are a few things that one should note before using them.

## Dont set Optionals to null
```java
Optional<String> myString = null;
```
Doing stuff like this is going against what we actually wanted to achieve by using Optionals. Remember, we wanted to go away from manually checking for null values. You are defeating the purpose of that if you need to check for potentional null Optionals. There are `ofNullable()` and `empty()` if you need to work with empty Optionals.

## Don't overuse it
Not every potentional null value needs to be an Optional. Being flooded with them in a larger program is probably something you want to avoid. 
Optionals are meant to be used as return types for methods. Using it as an argument for a method or as a field in your class would overcomplicate things.

# Final Thoughts
I think that Optionals are a great way for one thing, which is preventing `NullPointerExceptions` caused by methods returning null. `null` always had the problem of not being clear about what it is representing. You don't immediately know if null was returned because something was absent, if something could not be calculated or because of any other reason. Optionals don't help with that clarity, but it helps showing that something may return null at all. This makes Optionals a great return type for methods if they end up needing to return null.

But don't overuse them. Sometimes, it probably makes more sense to throw an Exception instead of returning null. 