---
date: 2022-11-09T23:20:42Z
title: A Glance Into The Mirror - The Reflection API
---

Recently in a project I'm involved in, I had to work on some barely working piece of software that was supposed to be a custom translation management tool.
That tool was coupled to a CMS addon of a web shop.
When parts of that web shop were in need of an update, you could go to the translation management tool to download a list of strings of all components embedded to the page you want to translate. 

In order to get the strings you want to internationalize, the tool needs to scan all the components that are used on a page and then get all the strings of each component.
Those Components are represented as POJOs, in which the getter of a String may take a `Locale` as a Parameter.
```java
public String getText(Locale locale) { ... }
```

Getting all components of a page is no problem, but the part about collecting strings comes with a caveat.
Different components may hold a different amount of string fields. 
A component for a simple paragraph for example may hold a single string field, while a component representing an accordeon may hold two.
There is also an additional layer of complexity because some Strings in those POJOs are not related for the frontend at all, for example fields that relate to type information for the database.

That leads to following questions: How can we get the Strings of different components with different amount of string fields and with some of those string fields not being relevant for the frontend side of things?

# Enter: Reflections!
The tool used the so-called _Java Reflections API_ located in the `java.util.reflect` package.
With the help of the API a Java program is basically able to look into its own implementation, and can even manipulate itself.
Not only does it enable to look into the declared methods and its signature, it can even run those methods, and all of that in runtime!

To show some examples, let's say that we defined the following class somewhere in our program:
```java
public class SomeRandomClass {
    private int count = 5;
    private String text = "Yo";
    
    public String printSomething(String suffix) {
        return text + suffix;
    }
}
```

Then we could use following snippets to introspect that Class in runtime:

```java
public class MainClass {
    public static void main(String[] args) {
        Class<SomeRandomClass> test = SomeRandomClass.class;
        Method method = test.getDeclaredMethod("printSomething");
        Class<?> returnType = method.getReturnType();
        Class<?>[] parameterTypes = method.getParameterTypes();
        Field[] fields = test.getFields();
    }
}
```
This way, you can get a method by passing in a String, you can look on its return types or parameters and look on defined fields.
You can even invoke those methods, and look into more things of the class like superclasses or Annotations.
Thinking about the possibilities made me realize that I took frameworks like Spring for granted.
Spring needs information about Objects to later inject into other Objects from somewhere after all. 
Another Example is debugging and the other helpful tools IDEs like IntelliJ provide.

# Something about Power and Responsibility
Ok, I talked a bit about reflections, that's cool and all. 
How was that helpful when trying to collect string fields to localize?
The translation tool used the API to get a list of methods, filtered those who weren't getters and didn't return Strings and then invoked those methods.
After fixing bugs the previous implementation in our software project had, it looked something like this:
```java
public class MainClass {
    public List<String> collectStrings(AbstractComponent component, Locale locale) {
        List<String> list = new ArrayList<>();
        
        Class<AbstractComponent> clazz = AbstractComponent.class;
        Method[] methods = clazz.getDeclaredMethods();
        
        for(var method : methods) {
            boolean isLocalizedMethod = method.getName().startsWith('get')
                    && method.getReturnType().equals(String.class)
                    && method.getParameterCount() == 1
                    && method.getParameterTypes()[0].equals(Locale.class);

            try {
                String string = (String) method.invoke(component, locale);
                list.add(string);
            } catch (IllegalAccessException | InvocationTargetException e) {
                throw new RuntimeException(e);
            }
        }
        return list;
    }
}
```
That's pretty cool, because you don't need to care about what kind of component you are looking at right now.
It just looks at what methods are defined in there and collects those that you need.

I was talking about the previous implementation being error-prone. 'Why?' you may ask.
The problem was that it also looked for subcomponents this way.
See, the previous solution looked at the return type of getter methods and filtered for Lists of `AbstractComponents`.
Turns out, not only did components hold a List of subcomponents, but also a List of parent components.
This way, you were collecting Strings for components on all kinds of pages, massively bloating up the resulting file of strings to translate.

How did we fix this bug? 
A proposed amendment was to ignore methods with the name `getParent` since this was actually a method available on the `AbstractComponent` level.
Instead, we didn't use reflections at all and just reused a visitor service that already existed for the purpose of collecting subcomponents.

The reflections API is very powerful, but something you do need to be a bit careful around.
Debugging can be painful with extremely long stack traces (even for Java standards)!
Just know when it makes sense and when you are overcomplicating things.