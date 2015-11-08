---
layout: post
title: "About Composer commands"
date: 2015-11-08 11:09:00
categories: development composer best-practice
---

Recently I'm considering a not-so-common Composer feature: commands (scripts).

Composer already provides some hooks, you can find the list of
provided hooks here: [getcomposer.org/doc/articles/scripts.md#event-names](https://getcomposer.org/doc/articles/scripts.md#event-names)

These hooks you see aren't the same commands I mean in this article, because can't
be invoked using `composer post-install-cmd`, I'm going to list some scripts
I found useful, but before let me explain some reasons why I think they should
be included in your composer.json.

## Questions

> People don't know how you do it.

To solve the problem about how it should be done, usually, OSS developers share
a CONTRIBUTING.md file containing instructions about the requirements of their
pull requests.
This is generally enough, but all the instructions listed are always the same
on every environment, so why don't encapsulate them in a single istruction?

> How should I test it?

PHPUnit, Behat, PhpSpec, [etc](https://github.com/ziadoz/awesome-php#testing)...
there are lot of libraries to execute tests out there, before submitting something
how should I test my changes?

> What should I do after installation?

Ok, I've installed a fork of `symfony/symfony-standard` including some test-only
changes, how can I execute these changes on my continuous integration system without
calling them also on development and production?

> Prevent ambiguity.

Do you know that a developer could have 3 PHPUnit versions on his/her environment?

Lets say he/she has:

* PHPUnit v3 at `/usr/local/bin/phpunit`
* PHPUnit v4 at `~/.composer/vendor/bin/phpunit`
* PHPUnit v5 at `[project]/vendor/bin/phpunit`

Which version should be used? Can these versions be used without problems or some
of them aren't compatible with my codebase?

The developer probably will use just the `phpunit` command, which of them will be
executed?

## Commands

### `composer test`

Already introduced by [github.com/thephpleague/skeleton](https://github.com/thephpleague/skeleton) [here](https://github.com/thephpleague/skeleton/commit/54f6cbc6064e56e92ab59db46f99f9ff815d055d), using this command you don't need to worry about which version of your testing tool is used nor where it is.

Indeed reading [getcomposer.org/doc/articles/scripts.md](https://getcomposer.org/doc/articles/scripts.md#writing-custom-commands) :

> Note: Composer's bin-dir is pushed on top of the PATH so that binaries of dependencies are easily accessible as CLI commands when writing scripts.

Do you need to execute a test before another (you can't write a dependency between phpunit and behat)?
For example if you don't want execute functional tests before having checked
that every unitary test is executed correctly, and before of all a PSR-2 coding
style is strictly required.

    {
        "scripts": {
            "test": [
                "phpcs src --standard=psr2 -spn",
                "phpunit",
                "behat"
            ]
        }
    }

What's the version used?

The priority is given to `[project]/vendor/bin/phpunit`, but if it's not included
(for example if you're testing the `prod` mode locally) than depending on your `$PATH`
`~/.composer/vendor/bin/phpunit` or `/usr/local/bin/phpunit` will have the priority
*without* changing your codebase!

### `composer compile`

Already introduced by Heroku [here](https://devcenter.heroku.com/articles/php-support#custom-compile-step)
this command allows you to execute a set of steps (or a single script) without
forcing these commands on every package install or update.

An example of its usage? Ok, let's say we are installing a bundle on a Symfony 2
app, adding a `app/console cache:clear` will always give an error if executed right
after the install or the update, so we can add a command like this:

    {
        "scripts": {
            "compile": [
                "app/console -n cache:clear",
                "app/console -n assets:install"
            ]
        }
    }

*The `-n` option is usually not required, but for security I always add it.*

Once you've finished the installation and configuration of your bundle, just run
the `composer compile` to be sure that's installed completely.

### `composer check-style`

A lot of projects started using coding standards during last years, but which tools?
And which options? The order of them?

As always a simple command can solve this doubt:

    {
        "scripts": {
            "check-style": [
                "php-cs-fixer fix --config-file=.php_cs",
                "php-formatter formatter:use:sort src"
            ]
        }
    }
