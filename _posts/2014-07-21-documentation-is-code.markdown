---
categories: development php annotations
date: 2014-07-21 18:37:00
layout: post
title: "Documentation is code"
---

Finally I found a new argument, after months and months, and it's the documentation.

In my priority list there's always an initial point: the documentation, if it's missing then the library isn't so good as it seems. Why? Simply because we aren't in the mind of the developer that works on that library. I know you understand and watch for it too.

A question I always asked myself is: while a developer is working on a source code, is the documentation the first thing he writes? I must admit it, not for me, and the reasons are many: focus on the logic, times, deadlines, etc... and as always the documentation is the last thing to do, if I have time.

That's really sad, because when I restart to code the day later I always forgot some things and I know that this is important, so while talking to other developers I always say "documentation is really important!" but when I look my code there's always something that's not described.

But today I think there's a solution for this problem, if we consider the principle:

> a good code should self-describe itself

of course we can't always follow this principle, it should be used only in small pieces of code (mainly logic)!

To do this experiment I'll use Symfony 2, because ehm... I love this framework ^^'.

Let's start with models, if I write this piece of code:

<script src="https://gist.github.com/EmanueleMinotto/49fc3e049a841e26e011.js"></script>

there's still nothing of what I'll talk about but we already have a good description of our entity:

* we are talking about the table posts, that has four columns
* this table will be used to contain a set of Post objects

Not so much you think? Wait to work with a big database schema or a legacy project, then you'll understand the importance of these few lines.

We can improve it very much, the first improvement we can do is the features we are missing:

<script src="https://gist.github.com/EmanueleMinotto/28c8f02f6cf0cec6fa17.js"></script>

As you can see we added some things to our post, things like a slug, a reference to the author, a password, etc... but some of you could still say "there's no documentation", and that's not true, because we can see the nullable attribute in the PHPDoc and the attributes names, if the updated attribute can be nullable, than that attribute can be null and so still not updated (only created), the same thing for the password attribute, if it's null than that post hasn't a password.

Now the main question: who maintains all these attributes? Ok for the features, but this means that I have to write more code, I have DEADLINES. Here the documentation can help us!

<script src="https://gist.github.com/EmanueleMinotto/cf096605d139a249ffc1.js"></script>

Now everything that's related to our post it's not our problem, we can continue to focus on the post logic and have a more documented code, that's what we wanted.

Honestly I don't think this entity needs to be explained and the "Hello World!" level was already passed, we are building something really cool.

That's not enough so we still have some works to do on our entity: validation, filtering, security, visibility and a thing we forgot initially, the database.

<script src="https://gist.github.com/EmanueleMinotto/cfe07f5b15e08a9f9392.js"></script>

It's not black magic, our code is documented just describing the entity, and that's our code, because the annotations will be read to empower the entity. That's the annotations' power: we are reaching a **documentation driven development**.

Ops... forgot the database. Using doctrine 2 migrations we can auto-generate the SQL needed to migrate the database schema: [docs.doctrine-project.org/projects/doctrine-migrations/en/latest/reference/generating_migrations.html](docs.doctrine-project.org/projects/doctrine-migrations/en/latest/reference/generating_migrations.html)

It's a great tool, but I'm not here to talk about doctrine, the only thing I want say is: that tool will read your annotations! Yes, your documentation is already used.

In a common "MVC developer" this example should be already enough to start using annotations, but I'm not satisfied and I want talk about the Controller section. For this section I want try another approach:

<script src="https://gist.github.com/EmanueleMinotto/0e506eac4a6858d18029.js"></script>

It seems a well documented controller, while there are just few lines of code, there's much more documentation, but what's the problem? Now you have to configure the .htaccess, in the action logic there's a control of the HTTP method (that's not the logic, it's a prerequisite), you have to throw and display an exception if the request is not GET (it's still not the logic), you have to display the view (ok, it's MVC, but it's still not the logic), etc... it should be simple what is described at lines 22-23: retrieve the list of posts from the database and pass them to the view.

It's well described, but it's time lost too, because the time we used to describe the class, the method, the code, we could use it to write the code. This is a great example to explain what I mean, look this transformation.

<script src="https://gist.github.com/EmanueleMinotto/847bb5dc886f3f4fa6c1.js"></script>

You'll immediately see that there are less lines of documentation, then why did I do this example? Because now there are only the lines we wanted, everything else shouldn't be in the method code, because them are not in what the method was thought to do.

If you look these last two example you'll see that everything in the first snippet documentation is in the second too: what does this method do? When it's invoked? On which conditions it's invoked?

These are only two examples of what we can do in the documentation, we can add [breadcrumbs](https://github.com/Abhoryo/APYBreadcrumbTrailBundle) defining a hierarchy, [describe](https://github.com/nelmio/NelmioApiDocBundle) the actions of a [RESTful system](https://github.com/FriendsOfSymfony/FOSRestBundle), [caching services](https://github.com/TheBigBrainsCompany/TbbcCacheBundle), [injecting dependencies](https://github.com/schmittjoh/JMSDiExtraBundle), etc... everything just describing things, and after all these things [generating an exportable documentation](http://apigen.org/) based on what we documented/described.

Documentation shouldn't replace all the code, I'm against things like [this](https://github.com/mmoreram/ControllerExtraBundle#paginator-example) or [this](https://doctrine-orm.readthedocs.org/en/latest/reference/annotations-reference.html#sqlresultsetmapping), but documentation is no more a secondary thing to do in our list, it can be used to do the things in that list.

### References

* [https://github.com/schmittjoh/JMSSerializerBundle](https://github.com/schmittjoh/JMSSerializerBundle)
* [<span style="line-height: 1.5em;">https://github.com/jbafford/PasswordStrengthBundle</span>](https://github.com/jbafford/PasswordStrengthBundle)
* [<span style="line-height: 1.5em;">https://github.com/stof/StofDoctrineExtensionsBundle</span>](https://github.com/stof/StofDoctrineExtensionsBundle)
* [<span style="line-height: 1.5em;">https://github.com/rdohms/dms-filter-bundle</span>](https://github.com/rdohms/dms-filter-bundle)
* [<span style="line-height: 1.5em;">https://github.com/mmoreram/ControllerExtraBundle</span>](https://github.com/mmoreram/ControllerExtraBundle)
* [<span style="line-height: 1.5em;">https://doctrine-orm.readthedocs.org/en/latest/reference/annotations-reference.html</span>](https://doctrine-orm.readthedocs.org/en/latest/reference/annotations-reference.html)
