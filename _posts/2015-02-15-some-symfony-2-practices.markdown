---
layout: post
title: "Some Symfony 2 practices"
date: 2015-02-15 03:29:00
categories: symfony2 best-practice
---

In this post I want share some personal development practices used working with the Symfony 2 framework.

I'm not sure them are great or almost good practices, but I find them useful and if you want suggest me some cons about using them (or some pros using other practices), then thank you. :) 

### Configuration directories

Usually the configuration for bundles are included in the `config.yml` or `config_*.yml` files, I think this isn't great because force the developer to maintain something that could become really big, imagine [this configuration](https://github.com/lsmith77/symfony-rest-edition/blob/2.2/app/config/config.yml) expanded for your needs.

A practice I'm using is to contain the configuration of every third-party bundle in a single YAML file, named with the root of the configuration, in the `app/config/vendor` directory and including them in the `config.yml` file (or `app/config/vendor_dev` in the `config_dev.yml` if the bundle is used only during development).

Something like this:

<pre>/app
  /config
    /vendor
     fos_user.yml
     dotrine_cache.yml
     fos_rest.yml
     jms_serializer.yml
    /vendor_dev
     jms_translation.yml
    ...
   config.yml
  ...
...
</pre>

### Configuration over parameters

Before starting some open source bundles, I saw some advantages in working with parameters instead of a complex configuration, for example in the [DoctrineCacheBundle](https://github.com/doctrine/DoctrineCacheBundle) the setup of a server host has to be repeated over every provider.

I imagine this was done to allow different hosts, but when I develop there are only two hosts: one for development (on Vagrant my dear localhost) and on production an external host.

The effort of define every provider can be replaced simply using `memcached: ~` and setting the `doctrine_cache.memcached.host` parameter, this will be used by every provider of type memcached.

Experimenting this example, I started defining more or less every configuration in my bundles as a parameter (for the naming strategy of these parameters see below) allowing to redefine them in two ways:

1. if the user override the parameter, than that's used
2. if the configuration is explicit, than the configuration override the parameter value (redefine it)

### Tests directory

I must admit that since few month ago I wasn't really familiar with unit/functional tests, but when I started learning them immediately one thing captured my attention: the bootstrap.

Looking some open source projects, all those have a tests/bootstrap.php which doesn't do anything else than preparing the namespaces and classes autoload, and I thought "but there's already Composer for this!"

Then the solution was: move the phpunit.xml.dist in the project root, setting the bootstrap attribute to vendor/autoload.php and setting

<pre><testsuite name="Project Test Suite">
    <directory>./Tests</directory>
</testsuite>
</pre>

Tests instead of tests to follow the PSR-1 standard, and that's all: no autoload to define, no tests/bootstrap.php and all the tests can be based on namespace just adding this to the composer.json:

<pre>{
  "autoload": {
    "psr-4": {
      "Vendor\Project": "."
    }
  }
}
</pre>

of course your tests will be under a `VendorProjectTests*` namespace.

If you use the PSR-0 standard and src and tests directories, the approach is better:

<pre>{
  "autoload": {
    "psr-0": {
      "Vendor\Project": "src"
    }
  },
  "autoload-dev": {
    "psr-0": {
      "Vendor\Project\Tests": "tests"
    }
  }
}
</pre>

Why am I talking about this considering that Symfony 2 already uses this approach? Because I use it for [Symfony 2 bundles](https://github.com/EmanueleMinotto/TwigCacheBundle), [Silex service providers](https://github.com/EmanueleMinotto/FakerServiceProvider) and [standalone libraries](https://github.com/EmanueleMinotto/PlaceholdItProvider).

### Tests kernel

When I work on tests sometimes I use an AppKernel created only for tests, like [this](https://github.com/EmanueleMinotto/TwigCacheBundle/blob/master/Tests/AppKernel.php). There isn't very much to say about this system, it is like a reproduction of the application. I think it's useful for two reasons:

1. instead of mocking the whole world, it already reproduces the sub-application with only the features required
2. [more different configurations](https://github.com/EmanueleMinotto/TwigCacheBundle/blob/master/Tests/DependencyInjection/TwigCacheExtensionTest.php#L23-L24) can be tested

### @inheritdoc all the things!

Documentation is important, but somethimes I really don't know what to say about a class or a method, often small method are just a concatenation of native PHP functions. Then I always use the {@inheritdoc}, just to allow an inherited doc from parent class or method (for example supported by [apigen](http://www.apigen.org/)) while thinking what I can say about them hehe.

### Naming strategies

My naming strategies are different and depend on what I should identify.

If a class is used as a service then the identifier is the full namespace lowercase, with and underscore separating uppercase letters and a dot instead of the backslash, so for example `VendorBundle\Package\LibraryController` become `vendor_bundle.package.library_controller`

Database tables are always lowercase (setting `doctrine.orm.naming_strategy: doctrine.orm.naming_strategy.underscore`) and when possible have a small prefix.

If something must refer to a configuration, I try to use the full configuration tree, for example:

<pre>doctrine_cache:
  providers:
    doctrine.orm.metadata_cache_driver:
      type: apc

doctrine:
  orm:
    metadata_cache_driver:
      id: doctrine_cache.providers.doctrine.orm.metadata_cache_driver
      type: service
</pre>

These are my conventions, now my speed of development and assimilation of Symfony 2 best practices are good but, considering the universe behind this framework, I'm always looking for other best practices or suggestions. :)
