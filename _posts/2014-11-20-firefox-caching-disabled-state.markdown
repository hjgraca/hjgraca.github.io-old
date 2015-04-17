---
layout:     post
title:      "Preventing Firefox from caching input disabled state"
subtitle:   "Back button and refreshing keeps disabled state"
date:       2014-11-20 17:34:00
author:     "Henrique Graca"
header-img: "img/spiral_bg.jpg"
description: "Preventing Firefox from caching input disabled state"
---
<p>
    Whilst developing a simple feature to disable a button and add a spinner on form submit so that a user couldn't click the button and resubmit the page if it took too long.
</p>
<h2 class="section-heading">The Problem</h2>

<p>Firefox caught me by surprise no other browser has this behaviour but it seems like Firefox caches the disabled state of an input between page refreshes and back browser button if that state was set by javascript and the only way to remove that state is hard refresh (ctrl+f5) </p>

<h2 class="section-heading">The Solution</h2>

<p>Adding <strong>autocomplete="off"</strong> to the input prevented Firefox from caching.</p>

<pre><code>"< input type="submit" value="Submit" autocomplete="off" />"
</code></pre>

<p>You can also add that attribute to the form itself</p>

<pre><code> "< form novalidate="novalidate" action="" method="post" autocomplete="off" >"
</code></pre>


<h2 class="section-heading">Final thoughts</h2>

<p>
    Bad move Mozilla, keep the standards guys. I lost a couple of hours trying to figure out a nice solution. The quest lead me to the following online articles
<a href="http://stackoverflow.com/questions/5985839/bug-with-firefox-disabled-attribute-of-input-not-resetting-when-refreshing">StackOverflow</a> and
<a href="https://bugzilla.mozilla.org/show_bug.cgi?id=654072">Mozilla</a>
</p>
