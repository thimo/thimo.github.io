---
title: "Use HTML5 elements in Internet Explorer 6-8"
permalink: "/2012/07/use-html5-elements-in-internet-explorer.html"
description:
categories:
  - html
---
<div class="separator" style="clear: both; text-align: center;"><a href="http://3.bp.blogspot.com/-4kAWNQQ_YKI/T_c6-_EtBsI/AAAAAAAAADU/C98Q8_rV-SM/s1600/220px-HTML5_oval_logo.png" imageanchor="1" style="clear: right; float: right; margin-bottom: 1em; margin-left: 1em;"><img border="0" src="http://3.bp.blogspot.com/-4kAWNQQ_YKI/T_c6-_EtBsI/AAAAAAAAADU/C98Q8_rV-SM/s1600/220px-HTML5_oval_logo.png" /></a></div>One of the (as you probably know many) problems with Internet Explorer is that up to version 8 it refuses to render unknown HTML elements. Being created before the HTML5 standard was finalized, you're out of luck if you want to make a nice HTML5 web site and use the new elements like header, footer or menu. IE will treat them as inline elements and not apply any styling you defined in a stylesheet.

Fortunately, it turns out to be pretty easy to teach IE about these new elements, so that it will render them correctly. The trick is to use Javascript to create the elements you need in the DOM before the browser starts rendering.

For XPages applications I use the following code. Put it in your theme's <resources> section. It will only be included for visitors who use Internet Explorer 6 up to 8 and it create a bit of Javascript in the head of each page.

```
<script
    clientSide="true"
    rendered="#{javascript:context.getUserAgent().isIE(6,8)}"
    contents="document.createElement('header');document.createElement('hgroup');
        document.createElement('nav');document.createElement('menu');
        document.createElement('section');document.createElement('article');
        document.createElement('aside');document.createElement('footer');">
</script>
```

With this IE will treat the elements as if they are div's, and that's pretty much all you need.

Game on!
