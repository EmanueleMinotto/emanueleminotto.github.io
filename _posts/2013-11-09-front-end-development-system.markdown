---
categories: development front-end symfony2
date: 2013-11-09 12:24:00
layout: post
title: "My front end development system"
---

<div style="text-align: center">
    <img src="/assets/2013-11-09-front-end-development-system/frontend.png">
</div>

Hi all, I'm preparing a long post in these days, so, to fill this time, I'll show you how I work with the front end side of a website using the Symfony 2 framework.


I'm not one of those guys who think the core logic behind a website should switch from the server to the client, and I'll ensure you it will never happen completely, this is why I'm using a PHP framework to explain a faster front end development system.

First of all: [Twig](http://twig.sensiolabs.org/). Twig is a template engine that compiles templates down to plain PHP files, as I said many times I think that a web designer shouldn't work with PHP, they could always try to work with some logics or variables, but they should only <span style="text-decoration: underline;">show</span> data, <span style="text-decoration: underline;">not use</span> them.

A naming convention is always a must if you wish to develop something maintainable for a long time, it doesn't matter what you're using, if you're using Twig as the template engine I suggest you to follow the [Symfony 2 official file extension system](http://symfony.com/doc/current/book/templating.html#template-suffix).

While you're developing a website or a big web application you need to separate roles and let them work indipendently from each other: web designers should work without waiting for the core logic to be ended or all the variables to be passed.

For this reason I suggest to use [Faker](https://github.com/fzaninotto/Faker) that generates fake data to fill spaces (for the images use [placehold.it](http://placehold.it/)). Why is it so useful? Because it can be used with Twig without excessive syntax and it always generates new and random data; for its usage in Symfony 2 add the [BazingaFakerBundle](https://github.com/willdurand/BazingaFakerBundle) and add the following lines to `app/config/config_dev.yml`

<script src="https://gist.github.com/EmanueleMinotto/fe5e768188c09dcd29ee.js"></script>

or if you wish to use it in [Silex](http://silex.sensiolabs.org/), try my [FakerServiceProvider](https://github.com/EmanueleMinotto/FakerServiceProvider) (and tell me if it's useful).

Before passing out from the server side I want to add some utilities:

* [Twig cache extension](https://github.com/asm89/twig-cache-extension): I use this extension with the [LiipDoctrineCacheBundle](https://github.com/liip/LiipDoctrineCacheBundle), so it's useless in the development environment, but in production the content is cached and doesn't need to be recalculated <script src="https://gist.github.com/EmanueleMinotto/6192d11250d4dd033f4a.js"></script>
* Twig Text extension: this is included in the symfony standard edition but, I don't know why, it's not included in the Twig extensions and then you should add it to your main bundle in the `Resources/config/services.yml` file <script src="https://gist.github.com/EmanueleMinotto/b865b264df5e0eae03b7.js"></script>
* [Twig data URI extension](https://github.com/romainneutron/TwigExtension-DataUri): if you wish to hide things like images path in the filesystem or the web URL you can use this extension <script src="https://gist.github.com/EmanueleMinotto/06bb15e60f315a88096e.js"></script>
* [MobileDetectBundle](https://github.com/suncat2000/MobileDetectBundle): I think it's useful if you wish to define something in mobile mode only or in desktop mode only, it adds some useful Twig functions like `is_mobile()` or `is_device('iPhone')`

After these utilities I suggest you to install two tools, the first one is called [FkrCssURLRewriteBundle](https://github.com/fkrauthan/FkrCssURLRewriteBundle) and it's used to fix relative paths in assets, the second one is [toopay/assetic-minifier](https://github.com/toopay/assetic-minifier) because I've seen that after [cssmin](https://code.google.com/p/cssmin/) minification, the output isn't always the same.

This isn't a bundle but an Assetic extension, so you have to add it from the configuration:

<script src="https://gist.github.com/EmanueleMinotto/c6dddf16fb99d38e628c.js"></script>

[Bower](http://bower.io/) is a powerful JavaScript package manager I use. Create a BowerBundle under your src directory and read this article: [blog.ronny.lt/blog/2012/11/05/managing-symfony2-web-assets-with-bower/](https://web.archive.org/web/20130813084112/http://blog.ronny.lt/blog/2012/11/05/managing-symfony2-web-assets-with-bower)

If you follow the article indications, you'll be able to use JavaScript libraries without worrying about the directories or a manual update, everything is maintained using Bower! Remember only to add it to the Assetic configuration under assetic > bundles.

[Assetic](https://github.com/kriswallsmith/assetic), as the name suggests, is used to work with assets, in particular to work with meta-languages and with minification, in fact I use it with this configuration

<script src="https://gist.github.com/EmanueleMinotto/f29c4b7655e019b67b38.js"></script>

and in Twig files with this code

<script src="https://gist.github.com/EmanueleMinotto/534e84031fd7dc914fb7.js"></script>

What's in this source code? The css_url_rewrite & toopay_cssmin filters are those we defined above, the "?closure" filter means that it will be used only in the production environment and not during development (hopefully, otherwise Google will kick you in few minutes!).

.less files will be elaborated and outputted as CSS in development too, but pay **attention**: files are not compressed in a single file and converted from less to css as a single file, they are treated individually.

The jshrink tag is useful if you want to compress an inline JavaScript code, you can't call the Google Closure service (remember that its usage is limited), but you can use this fabolous bundle called [SalvaJshrinkBundle](https://github.com/nibsirahsieu/SalvaJshrinkBundle) that will  compress and remove comments from your inline JavaScript, use this tag with the Twig cache extension (inside it) and you'll gain in performances too!

One last important thing: this entire article was made to encourage front end developers to use some tools and follow some rules while developing. The output isn't the only important thing. If you need to work with bundles paths in a JavaScript file try the [JmikolaJsAssetsHelperBundle](https://github.com/jmikola/JmikolaJsAssetsHelperBundle), if you receive some not so good variables don't work on them but request final variables, if you need to work with a release version of APIs, those aren't available yet, mockup them with [apiary.io](https://apiary.io/), don't try strange ways. Think before acting.
