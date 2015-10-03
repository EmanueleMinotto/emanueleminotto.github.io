---
layout: post
title: "Service Injection in Doctrine DBAL Type"
date: 2013-10-18 19:56:00
categories: symfony2 doctrine2 dbal
---

When you think of a Doctrine 2 DBAL Type you think of an atomic thing, but how can you work programmatically on this type without defining an event?

A DBAL Type doesn't allow access to the Symfony 2 service container, you must use a hack. But before this let me explain the classic way (using events), why you should use this hack and why you shouldn't.

The classic way is defined in the Symfony 2 Cookbook: [How to Register Event Listeners and Subscribers](http://symfony.com/doc/current/cookbook/doctrine/event_listeners_subscribers.html)

Doctrine 2 events unlike Symfony 2 events aren't defined by the developer, the developer can only attach listeners on them. Why? Because Doctrine 2 isn't a framework that you can use for everything, persistence is its only job.

**When should you use this hack?** When your stored object isn't a 1:1 representation of the PHP object and its elaboration can be memoizable or really fast.

I use this hack for browscaps: with the [BrowscapBundle](https://github.com/browscap/BrowscapBundle) I can convert from an user agent string to a `stdClass` object (like the [get_browser](http://it2.php.net/manual/en/function.get-browser.php) function).

Our object is

<script src="https://gist.github.com/EmanueleMinotto/1d53af69176eaf336c0c.js"></script>

As you can see there's nothign special: it's just an archive used to store `User-Agent` strings.

With the classic way what I should do is write an external event listener (to allow automatic regeneration of entities).

<script src="https://gist.github.com/EmanueleMinotto/a4eb545abf4b2e6b687a.js"></script>

<script src="https://gist.github.com/EmanueleMinotto/74b9578a8785305c0c29.js"></script>

With this code, everytime you will persist, update or extract a Agent entity from/to related storage system it'll be converted from string to object.

The problem is that these callbacks will be invoked everytime and numerous events aren't recommended for your application.

But with this hack I can write:

<script src="https://gist.github.com/EmanueleMinotto/fd666253dbf6f13d3e3c.js"></script>

Doctrine ignores this event but it exists and results attached!

<script src="https://gist.github.com/EmanueleMinotto/4a67a451e42df9c6de42.js"></script>

This listener seems useless, but it's the only way for this hack because Doctrine 2 DBAL Type doesn't allow direct access to the service container but allows access to events listeners.

<script src="https://gist.github.com/EmanueleMinotto/04eeba01d889a6365801.js"></script>

I use this hack to define only the events related to application flow (less events is better).

Now that you know when you can use this, you must read why **you shouldn't use it**.

Let me explain the reason with one simple example: imagine that one day PHP will allow external hooks in native classes constructor, how can you work without knowing what you're doing while initializing a new `stdClass`?

The same reason here: everytime you extract a value from the database you want extract it fast (hopefully you'll extract more than one records), but how can you be sure that extraction is fast if every attribute of a single record depends on external libraries and logics?

A note from [@Ocramius](https://twitter.com/Ocramius) (from the Doctrine Project core team):

> DBAL types are not designed for Dependency Injection.
>
> We explicitly avoided using DI for DBAL types because they have to stay simple.
>
> We’ve been asked many many times to change this behaviour, but doctrine believes that complex data manipulation should NOT happen within the very core of the persistence layer itself. That should be handled in your service layer.
