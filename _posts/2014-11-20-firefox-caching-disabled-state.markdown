---
layout:     post
title:      "Preventing Firefox from caching input disabled state"
subtitle:   "Back button and refreshing keeps disabled state"
date:       2014-11-20 17:34:00
author:     "Henrique Graca"
header-img: "img/fence.jpg"
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

<p>I lost a couple of hours trying to mock the HtmlHelper but finally I was able to create a really nice mocked version that I think covers a lot of real test cases such as returning partial views, redirections and rendering Html from an Html extension.</p>
<p>Good coding!!</p>