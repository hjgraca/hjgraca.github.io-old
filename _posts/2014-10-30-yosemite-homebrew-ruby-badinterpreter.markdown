---
layout:     post
title:      "Ruby bad interpreter: No such file or directory"
subtitle: 	"OSX Yosemite Homebrew error."
date:       2014-10-30 21:58:00
author:     "Henrique Graca"
header-img: "img/The_Matrix_Background.jpg"
---

<p>After upgrading to OSX Yosemite homebrew was giving me the following error:</p>

<p><strong>
/usr/local/bin/brew: /usr/local/Library/brew.rb: /System/Library/Frameworks/Ruby.framework/Versions/1.8/usr/bin/ruby: bad interpreter: No such file or directory</strong></p>

<p>This error can easily be fixed in the following steps:</p>

<blockquote>
<ol>
<li>Open terminal</li>
<li>nano /usr/local/Library/brew.rb</li>
<li>In the first line change <strong>“1.8”</strong> to <strong>“Current”</strong>, so it should look like this:
#!/System/Library/Frameworks/Ruby.framework/Versions/<strong>Current</strong>/usr/bin/ruby -W0</li>
</ol>
</blockquote>

<p>And that's it, happy brewing!</p>