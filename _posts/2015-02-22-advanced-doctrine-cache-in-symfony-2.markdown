---
layout: post
title: "Advanced Doctrine cache in Symfony 2"
date: 2015-02-22 13:50:00
categories: doctrine2 symfony2
---

This post is essentially just a tip, but considering the great usage of the [doctrine/cache](https://github.com/doctrine/cache) library (now included in `symfony/symfony-standard`!) and the number features added by [CEikermann/advcache](https://github.com/CEikermann/advcache), I think it could be really useful.

The tip is: <script src="https://gist.github.com/EmanueleMinotto/262baed0af21612f640b.js"></script>

`doctrine_cache.providers.test` is a generic implementation of a doctrine cache, its name is based the predefined [DoctrineCacheBundle](https://github.com/doctrine/DoctrineCacheBundle) naming system.

As you can see it's pretty simple, but how does it work? What does it allow us to do? Which are pro and cons?

The concept is based on [services decoration](http://symfony.com/doc/current/components/dependency_injection/advanced.html#decorating-services), that's a possibility allowed by the Symfony DependencyInjection component.

It's not a simple override of the old service, it works wrapping the old cache service in a new `AdvCache\AdvCache` instance passing it as a constructor argument, renaming the old service in a new, not public, service and replacing the old service with the new advanced one.

Why isn't it public? Because the decorating service should be as much as possible similar to the decorated service, in this case the new service is based on the `Doctrine\Common\Cache\Cache` interface so it could be used for other services need the common interface for their services.

What does it allow us to do? Two great things: default values and tags.

Tags are great, but my favourite features are default values, with them you can do things like this: <script src="https://gist.github.com/EmanueleMinotto/e3dbe0a2838ece99c796.js"></script>
and this is [another](//emanueleminotto.github.io/blog/im-not-afraid-symfony-2-performances/) improvement for your app performances.

For completeness some cons should be considered, but for completeness only, because most of times them can be ignored.

1.  The decorating service will lose every information about the original implementation, so the [`instanceof` operator](http://php.net/manual/en/language.operators.type.php) can be used only with the common interface
2.  If the decorated service has other methods apart from those methods defined in the interface, them will not be available in the decorating service, because it implements the interface only, this can be solved extending the `AdvCache\AdvCache` class, considering that the original implementation is in a [protected `$cache` attribute](https://github.com/CEikermann/advcache/blob/master/src/AdvCache/AdvCache.php#L26), writing your extension it should be easy to redefine them too
