---
layout: post
title:  "PHP in a Tweet"
date:   2012-09-13 11:52:00
categories: twitter php code-golf
---

Yesterday an ex colleague tweeted something that captured my attention:

<blockquote class="twitter-tweet" lang="en"><p lang="en" dir="ltr"><a href="https://twitter.com/hashtag/php?src=hash">#php</a>&#10;function sanitize_http_protocol($link,$_protocol=&#39;http&#39;){ return $_protocol.&#39;://&#39;.array_pop(explode(&#39;://&#39;,$link)); }</p>&mdash; Stefano Azzolini (@lastguest) <a href="https://twitter.com/lastguest/status/245539785119256577">September 11, 2012</a></blockquote>

So I started thinking to a Twitter-powered [code golf](http://en.wikipedia.org/wiki/Code_golf "Code golf - Wikipedia, the free encyclopedia")ing competition. Looking for other examples you can see these:

<blockquote class="twitter-tweet" lang="en"><p lang="en" dir="ltr">class Container { protected <a href="https://twitter.com/search?q=%24s&amp;src=ctag">$s</a>=array(); function __set($k, <a href="https://twitter.com/search?q=%24c&amp;src=ctag">$c</a>) { <a href="https://twitter.com/search?q=%24this&amp;src=ctag">$this</a>-&gt;s[$k]=$c; } function __get($k) { return <a href="https://twitter.com/search?q=%24this&amp;src=ctag">$this</a>-&gt;s[$k]($this); }}</p>&mdash; Fabien Potencier (@fabpot) <a href="https://twitter.com/fabpot/status/1443952125">April 3, 2009</a></blockquote>

This is [Twittee](http://twittee.org/ "Twittee: A Dependency Injection Container in a Tweet"): a dependency injection container in a tweet.

<blockquote class="twitter-tweet" lang="en"><p lang="en" dir="ltr">require __DIR__.&#39;/c.php&#39;;if (!is_callable($c = @$_GET[&#39;c&#39;] ?: function() { echo &#39;Woah!&#39;; })) throw new Exception(&#39;Error&#39;);$c();</p>&mdash; Fabien Potencier (@fabpot) <a href="https://twitter.com/fabpot/status/1109191723">January 10, 2009</a></blockquote>

This is [Twitto](http://twitto.org/ "Twitto: A web framework in a tweet"): a web framework!

<blockquote class="twitter-tweet" lang="en"><p lang="en" dir="ltr">class µ{function __callStatic($n,$a){static <a href="https://twitter.com/search?q=%24r&amp;src=ctag">$r</a>;$s=$_SERVER;return <a href="https://twitter.com/search?q=%24n&amp;src=ctag">$n</a>==&#39;_&#39;?@$r[$s[REQUEST_METHOD]][$s[REDIRECT_URL]]():$r[$n][$a[0]]=$a[1];}}</p>&mdash; Stefano Azzolini (@lastguest) <a href="https://twitter.com/lastguest/status/246275840499929088">September 13, 2012</a></blockquote>

And this is a microframework created on the occasion of this "competition". It's called μ and you can find its documentation [here](https://github.com/lastguest/mu "lastguest/mu").

These are three really nice examples of PHP code in a tweet. Of course you can't use these two snippets in production, but you can see that a short script isn't always an useless script or something prosy.

I realized the next two examples and *would be great to see your tweets too!*

<blockquote class="twitter-tweet" lang="en"><p lang="en" dir="ltr">function array_intersect_equal($a, <a href="https://twitter.com/search?q=%24b&amp;src=ctag">$b</a>) { return array_uintersect($a, <a href="https://twitter.com/search?q=%24b&amp;src=ctag">$b</a>, function ($c, <a href="https://twitter.com/search?q=%24d&amp;src=ctag">$d</a>) { return <a href="https://twitter.com/search?q=%24c&amp;src=ctag">$c</a> == <a href="https://twitter.com/search?q=%24d&amp;src=ctag">$d</a> ? 0 : 1; }); }</p>&mdash; Emanuele Minotto (@EmanueleMinotto) <a href="https://twitter.com/EmanueleMinotto/status/246180418800472064">September 13, 2012</a></blockquote>

This is a function used to bypass the `array_intersect` note "**Note**: Two elements are considered equal if and only if `(string) $elem1 === (string) $elem2`. In words: when the string representation is the same."

<blockquote class="twitter-tweet" lang="en"><p lang="en" dir="ltr">function is_ajax() { return (isset($_SERVER[&#39;HTTP_X_REQUESTED_WITH&#39;]) &amp;&amp; $_SERVER[&#39;HTTP_X_REQUESTED_WITH&#39;] == &#39;XMLHttpRequest&#39;); }</p>&mdash; Emanuele Minotto (@EmanueleMinotto) <a href="https://twitter.com/EmanueleMinotto/status/246180729761964032">September 13, 2012</a></blockquote>

Using this you can check if a request is made using AJAX or is a simple web-client request. I think this could be a nice game (and a personal test) for each PHP developer.

## Game rules

1. The tweet **have** to be a well-formed PHP code. You can check the right syntax using `php -l tweet.php` as described here: [php.net/manual/en/function.php-check-syntax.php](http://php.net/manual/en/function.php-check-syntax.php)
2. A code is taggable **only** using things like `echo "#php";` or `return "#error";` but still keeping your tweet a well-formed PHP code
3. Open (`<?` and `<?php`) and close (`?>`) tags are **not recommended**

If you want suggest a ranking algorithm/formula, new rules or suggest methods to write the code, comment down here. For example you can evaluate comments, or the usage of some methods and classes, or evaluate more retweets than number of users that favorites your tweet, more the length or the utility, etc... I would be glad to see your tweets, so comment here linking your tweets!

### Update of 27/08/2015

As you can see from the tweet below, this code golf game seems to be still alive, **what about something more than a blog post?**

Leave a comment and we can discuss about it, I think that something like [140byt.es](http://140byt.es) or something more complex could be funny, and you? :)

<blockquote class="twitter-tweet" lang="en"><p lang="en" dir="ltr">&lt;?php class S{static function __callStatic($k,$f){static <a href="https://twitter.com/search?q=%24s&amp;src=ctag">$s</a>;return <a href="https://twitter.com/search?q=%24k&amp;src=ctag">$k</a>==&#39;get&#39;?$s[$f[0]]:$s[$f[0]]=$f[1];}} <a href="https://t.co/lnDU9RujAy">https://t.co/lnDU9RujAy</a></p>&mdash; Fontana Lorenzo (@fntlnz) <a href="https://twitter.com/fntlnz/status/634846928027709440">August 21, 2015</a></blockquote>

[_Old discussion on Hacker News_](https://news.ycombinator.com/item?id=6516864)
