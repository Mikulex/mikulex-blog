---
date: 2021-10-18T16:43:00Z
title: CoffeeTime and what I've learned
---

As I've stated on my first post, *CoffeeTime* is a project I've started for various reasons, one of them being practicing *Java* and learning about tools around it, or programming on projects in general instead of some *Python* or *Ruby* scripts.

I'm honestly not that happy with how "glued together" it feels, but I guess mistakes have to be made to actually learn. Also, *CoffeeTime* is by no means finished, some things are still missing, but at least I got so far to get a site up and running, which is pretty cool.

## Maven
*Java* was the language of my choice because thats what I've used most for projects. But this time around, I wanted to use one of the popular building tools to learn about it. So I've decided to use *Maven* to manage building *CoffeeTime* and to help me with setting up dependencies. I honestly don't know why I didn't do it for projects I did for lectures in university, because it is indeed way more comfortable to work with. :)  

For example, adding dependencies is as easy as putting
```xml
<dependency>
  <groupId>org.example</groupId>
  <artifactId>example</artifactId>
  <version>1.1.0</version>
</dependency>
```
into your `pom.xml`, the configuration file and core of maven.

## Java's Stream API
There are some smaller things about *Java* itself that I've tried out, for example the *Streams API* that was introduced in Java 8.  
For example, when I wanted to get all markdown files inside a directory defined in `postFolder` without using loops, but the more fancy streams, I did so by using following snippet: 
```java
    //Path postsFolder = ... 
    Path postfiles = Files.walk(postsFolder)
    .filter(Files::isRegularFile)
    .filter(file -> file.toString().toLowerCase().endsWith(".md"))
    .collect(Collectors.toCollection(ArrayList::new));
```
It walks through `postFolder` and all its subdirectories, 
+ gets all files which are indeed files and not directories,
+ gets from those all files with the Markdown file extension by checking its filename string and
+ basically converts the stream back into a collection, here an `ArrayList`
  
I think it's nicer to work with the Stream API instead with loops here, it makes it more readable, or at least explicit what I'm actually doing here.

## HTML + CSS

A side effect of starting your own blog is learning some HTML and CSS. If I would have ended up using an established SSG like *Hugo*, I probably would have ended up writing my own theme too instead of just using a premade one. Makes it a bit more personal :)

The general refresher was neat, but I also went to finally learn a bit about `display: flex` and (although sparingly) `display: grid`. Working with flex is pretty neat. Managing spacing between items with the various `justify` and `align` statements is great, as is wrapping items around for more responsive websites.
