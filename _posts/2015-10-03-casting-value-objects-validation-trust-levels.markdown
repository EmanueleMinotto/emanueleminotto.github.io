---
categories: php validation tips
date: 2015-10-03 16:23:00
layout: post
title: "Casting: value objects, validation, trust levels & debugging"
---

A practice I'm testing in these days is to cast PHP primitives right after parameters declaration.


I was used to think that casting was something really wrong, like the `atoi` and `itoa` C functions to convert integers from/to array of chars (strings).

Without talking too much this is what I'm doing:

    /**
     * @param string $bar
     */
    public function foo($bar) {
        $bar = (string) $bar;

        // ...
    }

Like I wrote in the title, there are mainly four reasons behind this test...

## Value objects

If we consider the magic method `__toString` as the common method used to represent value objects in PHP than we'll have that our class will already accept them!

> A value could be an integer too, not only a string, so the `__toString` isn't enough because forces to return a string.

For example:

    /**
     * @param string $url
     */
    public function getSchemeLess($url) {
        $url = (string) $url;

        return preg_replace('/^(https?:)?(\/\/.*)/i', "$2", $url);
    }

What's happening here? Given an URL like *http://www.example.com*, this method will return the URL without the scheme, so *//www.example.com*. Pretty simple.

The interesting thing is at the first implementation line, because if the method receive an object instance implementing the `__toString` method, something like a `Zend\Uri\UriInterface`, than it will be implicitly converted and our method still works correctly.

**Attention:** this doesn't mean that all the objects implementing that method are value objects!

## Validation

Let me take the example above again:

    /**
     * @param string $url
     */
    public function getSchemeLess($url) {
        $url = (string) $url;

        return preg_replace('/^(https?:)?(\/\/.*)/i', "$2", $url);
    }

What happens if we pass an instance of a `stdClass` as `$url`? The execution will be blocked at the first implementation line.

_In my opinion this could be considered a small and partial validation system, but still a validation system._

Going more in depth someone could complain about the last implementation line, the execution will be stopped there too if `$url = new stdClass`: yes, but we are just moving the concept.

    /**
     * @param string $url
     */
    public function getSchemeLess($url) {
        return $this->provider->getSchemeLess($url);
    }

In this example we don't know if the provider is enough smart to validate `$url` (or to understand that it's an URL, see above).

Based on what we know about it, it probably will destroy the world if we don't give it a real string, and there is where our `$url = (string) $url` help us!

In fact if `$url` can't be casted to string, than the execution will be blocked before reaching the provider method.

## Trust levels

Ok, now try to think if you are the provider instead of the class that's using the provider... as we said before: this provider could destroy the world if `$url` isn't a real string!

If we talk only using contracts it's pretty simple, nothing can break them, but if we want provide a more permissive interface for the developers experience, than we must be sure that our parameters are safe and not generic `mixed`.

**Don't trust anything outside your class**, because only the class knows what there's really inside itself, also for internally used instances (has-a) and for inherited methods (also these could be redefined!).

## Debugging

This is more an utility:

    function a1($a1) { $a2 = a2((string) $a1); /* business logic */ return $a2; }
    function a2($a2) { $a3 = a3((string) $a2); /* business logic */ return $a3; }
    function a3($a3) { $a4 = (string) $a3; /* business logic */ return $a4; }

In this example, if we call `a1(new stdClass)` our stacktrace will have only one error, removing the string casting from a1 and a2 than everything until `a3` will be included in the stacktrace.

If you are afraid for the performances than don't worry, casting is [only one opcode](https://3v4l.org/UIWWv/vld#tabs), is doesn't cost really anything!
