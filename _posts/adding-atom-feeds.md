---
date: 2022-03-20T17:16:34Z
title: Adding Atom Feeds
---

I finally decided to work a bit on *CoffeeTime* again after a break. After a bit of refactoring I remembered that I wanted to add some kind of news feed a while ago. It might be not something useful if you don't actually write that many posts, but at least it's something you can implement in an afternoon. And I have another reason to actually write a post again :)

Now I have an [Atom feed](/mikulex-atom.xml) which you can add in your favourite RSS reader.

Generating a feed actually isn't that hard. At the end of the day, it is just an XML file which holds some metadata about your page itself and links to your entries. An example might look like this:
```xml
<?xml version="1.0" encoding="utf-8"?>
<feed xmlns="http://www.w3.org/2005/Atom">

  <title>My website!</title>
  <link href="http://mywebsite.org/"/>
  <updated>2022-03-20T17:16:34Z</updated>
  <author>
    <name>Me</name>
  </author>
  <id>urn:uuid:60a76c80-d399-11d9-b93C-0003939e0af6</id>

  <entry>
    <title>My first post</title>
    <link href="http://mywebsite.org/my-first-post.html"/>
    <id>urn:uuid:1225c695-cfb8-4ebb-aaaa-80da344efa6a</id>
    <updated>2022-03-20T17:16:34Z</updated>
    <summary>Some text.</summary>
  </entry>

</feed>
```
You can check the [docs](https://validator.w3.org/feed/docs/atom.html#person) at the W3 website for the full specification. They also have an [validator](https://validator.w3.org/feed/) to check if your feed conforms to their standard. 

As you can see, it isn't that hard to implement yourself. As for *CoffeeTime*, you now have to add an `rssAuthor` and `rssDesription` option to the `config.yml` in order to generate a working Atom XML. Those two fields fill the `Author` and the `subtitle` tags respectively. The `entry` tags get automatically generated for each post you have, pages get ignored. The XML file will be generated together with your html files when you run *CoffeeTime* with the `build` option.