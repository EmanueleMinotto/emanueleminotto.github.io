---
categories: symfony2
date: 2013-10-06 19:54:00
layout: post
title: "I'm not afraid of Symfony 2 performances"
---

It's impossible to write an absolute benchmark for a PHP framework:

* project code performances
* project code type (with very much elaboration or a single echo)
* vendor code size and performances
* framework overhead
* network time
* number of requests per second
* cache type and usage
* hardware time
* Memory & CPU
* DBMS
* number and type of operations on the database
* etc...

These are only some of the aspects you need to consider in writing and reading a valid benchmark test: it's impossible to make the right choice if the first thing you consider are performances.

When I hear things like "PHP is slow" or "framework X is slow" I always think "Why are you using PHP? Use C, C++ or Assembly!" or "Why do you want to use a framework? Native PHP really has a lot of things.", the answer is one: because PHP is abstract from boring things like platform and with some extensions and libraries is simpler and faster to be written than other languages.

Don't you believe me? Ask yourself why you're still using PHP (and write down here the answer :)). Really, I can't imagine other reasons to continue with a language if it's "the wrong solution".

It doesn't matter if you're still learning or are already experienced: you know PHP and you write fast with it.

This is my opinion about performances with PHP, now Symfony 2.

I approached to Symfony 2 time ago starting from Silex (yes, I come from the microframework world) and the only thing I can say is that it's a great solution for problems from [blog](http://tutorial.symblog.co.uk/), to an [ecommerce](http://sylius.com/) and an enterprise problem.

But this article is not about the development with Symfony 2, it's for the production, in fact everything I'll write here is for the production moment, usually after the deploy step.

![](http://www.websequencediagrams.com/cgi-bin/cdraw?lz=cGFydGljaXBhbnQgRGV2ZWxvcG1lbnQKAAwMSW5jdWJhdGlvbgAKDVByb2R1YwASBQoAMAstPgAmCjogZGVwbG95CgA5Ci0-AC4KOiBwdWJsaXNoaW5n&s=napkin)

While developing (there's a [checklist](http://symfony2-checklist.com/) here) you, the developer, are the most important subject, everything should be done in function of you, not users; in the incubation process (and only in this case!) you should check that optimizations work as you want and then you can publish your fast application, but never optimize things before the Incubation problem, remember:

> Premature optimization is the root of all evil.

Before starting a generic Symfony 2 application on a common LAMP (Linux, Apache 2, MySQL, PHP), I add (or check for) these tools:

## [JMSJobQueueBundle](https://github.com/schmittjoh/JMSJobQueueBundle)

This is the first thing you should install, this bundle allows you to schedule Symfony2 console commands as server-side jobs. My suggestion is to move every non constant operation and every procedure (that doesn't produce output) to the console because often problems of elaboration aren't `$i++` or `++$i` but how many things you do while users are waiting for the page.

For example a set of callbacks those invoke some URLs (with network time):

![](http://www.websequencediagrams.com/cgi-bin/cdraw?lz=cGFydGljaXBhbnQgQ2xpZW50CgAHDFNlcnZlcgAGDVF1ZXVlABgNRGFlbW9uCgoAOgYtPgAvBjogcmVxdWVzdAphY3RpdmF0ZQBGCAoAUAYtPgBGBTogbmV3IGpvYgoKAEMGAA8JY2hlY2sAFghzABUKAGoGOiBDYWxsYmFjayBVUkwgMQBNCgCBQgY6IHJlc3BvbnNlCmRlAHIRAC8dMgBNHjMAbB4uLi4&s=napkin)

As you can see client doesn't wait for every response by the n calls.

## [LiipDoctrineCacheBundle](https://github.com/liip/LiipDoctrineCacheBundle)

This bundle is important because you can choose where to send types of cache by defining services. It's good because Symfony 2 doesn't allow every type of cache in all the standard version of the framework.

If some values you calculated are needed for every request you can store them in Memcache, if them are required only 1 every 5 requests you can store it in the file system.

## [KnpGaufretteBundle](https://github.com/KnpLabs/KnpGaufretteBundle)

This bundle is used to abstract from the filesystem (very useful sometimes), but it is in this article because it can cache requests to the filesystem.

![](http://www.websequencediagrams.com/cgi-bin/cdraw?lz=cGFydGljaXBhbnQgU29mdHdhcmUKAAkMQ2FjaAAEDkZpbGVzeXN0ZW0KCgArCC0-AAwKOiAjMSByZXF1ZXN0CgAjCi0-AFgIOiByZXNwb25zZSB3aXRoIGNvbnRlbnQAQgwAcQU6ICMyAEAJAIEDBQAjIQ&s=napkin)

A huge number of accesses to the local filesystem may slow down your application and the remote filesystems too, so cache them.

## [APC](https://en.wikipedia.org/wiki/List_of_PHP_accelerators#Alternative_PHP_Cache_.28APC.29)

**It's time to use it**, but don't worry if you have problems with it... it'll be [included](http://derickrethans.nl/files/meeting-notes.html#add-an-opcode-cache-to-the-distribution-apc) in the PHP core from PHP 6! :) But if you are using PHP 5.5+ you have to use the [Zend Optimizer+](https://wiki.php.net/rfc/optimizerplus) and [APCu](https://github.com/krakjoe/apcu) because APC opcode side will be removed. Quoting the RFC:

**Advantages of Optimizer+ over APC**

1. Performance. Zend Optimizer+ has a consistent performance edge over APC, which, depending on the code, can range between 5 and 20% in terms of requests/second. See Benchmarks section below.

2. Availability for new <acronym title="Hypertext Preprocessor">PHP</acronym> versions. Optimizer+ is typically fully compatible with <acronym title="Hypertext Preprocessor">PHP</acronym> releases even before they come out; While this advantage was rarely realized because of the closed-source nature of the component, once open-source, both Zend and the community will help ensure that it’s always fully compatible with every element of the <acronym title="Hypertext Preprocessor">PHP</acronym> language, avoiding any lags.

3. Reliability. Optimizer+ has optional corruption detection capabilities that can prevent a server-wide crash in case of data corruption (e.g. from a faulty implementation of a <acronym title="Hypertext Preprocessor">PHP</acronym> function in C). This handles one of the very few downsides of using a shared-memory-based-opcode-cache - introducing a shared resource that - if corrupted - could bring down an entire server.

4. Better compatibility. We strived to make Optimizer+ work with any and all constructs supported by <acronym title="Hypertext Preprocessor">PHP</acronym>, in exactly the same way they’d behave without it.

Why should you use opcode? Because opcode lets you bypass some steps an interpreted language like PHP have to do, working directly with the opcode, if you update your sources the opcode is updated automatically.

![opcode](/assets/2013-10-03-im-not-afraid-symfony-2-performances/opcode.png)

## [Memcache](http://php.net/manual/en/book.memcache.php)

Main advantage of Memcache is that it's distributed. This tool is not required, but I think it could be useful. If you're building an enterprise software, you'll see the advantage later.

I use this tool as main key => value cache system, if something must be extracted on every request the item is stored here, else if must be called 1 every 5 requests it'll be stored in APC, else it'll be stored on file system.

## [twig-cache-extension](https://github.com/asm89/twig-cache-extension)

Twig is a powerful tool that I (but not only me) use as a template engine, now: some people will say that PHP itself is a template mmm... I don't think so, web designers shouldn't work with PHP.

This extension (framework agnostic) is a very elegant solution to cache loops in templates.

**Update 02/22/2015**: I've created the [TwigCacheBundle](https://github.com/EmanueleMinotto/TwigCacheBundle) too, if you want a more integrated solution.

## [CSSmin](https://code.google.com/p/cssmin/) with Google Closure

I use these resources with Assetic, they are very useful because in development I have all my sources clean and separated but while deploying everything is minified and becomes static.

## [DoctrineExtensions](https://github.com/l3pp4rd/DoctrineExtensions)

Somebody may think this is stupid, but in these extensions there's something about [references](https://github.com/l3pp4rd/DoctrineExtensions/blob/master/doc/references.md) between NoSQL solutions and RDBMS, you can use it to store part of data in a place and other in another place. Could it be an idea?

## [Varnish](https://www.varnish-cache.org/)

This is a caching HTTP reverse proxy, it's useful because gateway caches are a great way to make your website perform better but they have one limitation: they can only cache whole pages. If you can't cache whole pages or if parts of a page have "more" dynamic parts, you are out of luck. Try this tool before the standard HTTP cache.

The most interesting slides I found are these by [@liuggio](https://twitter.com/liuggio), I really recommend them!

<iframe src="//www.slideshare.net/slideshow/embed_code/key/wHBMjieyoiVxfM" width="595" height="485" frameborder="0" marginwidth="0" marginheight="0" scrolling="no" style="border:1px solid #ccc;border-width:1px;margin-bottom:5px;max-width:100%" allowfullscreen></iframe>

> "My application is still slow!"

If your application is still slow you can scale your hardware vertically (bigger and faster hardware) or horizontally (many small servers) but IMHO if you need to optimize again after all these you are on top 50 of Alexa ranking or you really need to start reengineering!

After all these enterprise problems I want to say: if you need to write an Hello World! simply type `<?php echo 'Hello World!";` in your index.php, if you only want to write in a blog then install WordPress, if you need an ecommerce use Sylius, but if you need something bigger or maintainable in the future, think well before refusing Symfony 2.

I believe in Symfony 2, so if you don't know how to optimize your application then [contact me](mailto:minottoemanuele@gmail.com). and together we'll find a solution. :)
