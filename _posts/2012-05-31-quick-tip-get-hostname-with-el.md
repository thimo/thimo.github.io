---
title: "Quick tip: Get hostname with EL"
permalink: "/2012/05/quick-tip-get-hostname-with-el.html"
description:
categories:
  - xpages
---
Here's a quick little tip, something I hadn't realized before and wanted to share.

Normally when I need to get the host name used in the current request I would do something like:

```
#{javascript:context.getUrl().getHost()}
```

It's a Javascript call, which is always slower than pure Java or Expression Language (EL). Where possible I try to use EL instead of Javascript.

So, can you achieve the same result with EL? Yes, and it's actually pretty straightforward. Just drop the "get" and "()" and fix the case:

```
#{context.url.host}
```

There's another example on the <a href="http://xpageswiki.com/web/youatnotes/wiki-xpages.nsf/dx/Memory_Usage_and_Performance">XPages Wiki</a>.

Happy hacking!
