---
title: "Be careful feeding the ResponseWriter, use big bites"
permalink: "/2013/01/be-careful-feeding-responsewriter-use.html"
description:
categories:
  - xpages
---
Earlier this week I got bitten by one of the consequences of writing JavaScript code in a Java environment. There are some things you need to be aware of and I for one was not.

In this case: the XPages run-time can't always determine what the type is of the JavaScript value you're feeding to a Java method. It can be problematic with single position values, either a string or a number. Is it a char, a String or an int?

What's happening? Consider the following XPage:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<xp:view xmlns:xp="http://www.ibm.com/xsp/core" rendered="false">
<xp:this.afterRenderResponse><![CDATA[#{javascript:
var writer = facesContext.getResponseWriter();

writer.write("abc");
writer.write("a"); /* BOOM!! */

writer.endDocument();
}]]></xp:this.afterRenderResponse>
</xp:view>
```

You would expect this XPage to output `abca`, but it doesn't. It will output `abc` and at the same time show the following error on the Domino server's console:

`HTTP JVM: com.ibm.xsp.exception.EvaluationExceptionEx: Error while executing JavaScript action expression`

Changing the `a` to something with two characters fixed the error and gave the expected output. But feeding it the number `0` (which was my original problem) did not. `12` on the other hand was fine.

If course, <a href="http://draft.blogger.com/blogger.g?blogID=6757635430621066528">this had to be a bug</a>, so I send a nice little report to   Paul Hannan. Unfortunately it's not (so much for street credibility points), and Martin Donnelly explains why:

> The com.sun.faces.renderkit.html_basic.HtmlResponseWriter class has overloaded write() methods - like this:
>
> void write(char)<br/>
> void write(char[], int, int)<br/>
> void write(int)<br/>
> void write(String)<br/>
> void write(String, int, int)<br/>
>
> For `writer.write("g");` the engine does not know whether to call void write(char) or void write(String) on the Java side.
>
> This is a general JavaScript to Java issue, as there is no JavaScript character type so we cannot be type-explicit on the SSJS calling side.
>
> To pass in a single character to the writer class that you either:
> a) find a non-ambiguous version of a write method and force that to be called,
> b) declare the var as an explicit Java type and pass that in
>
> Both are shown here:
>
>
> var writer = facesContext.getResponseWriter();<br/>
> writer.write("abc");<br/>
> writer.write("gh");<br/>
> //writer.write("g"); //nope<br/>
> // This will work ...<br/>
> writer.write("z",0,1);<br/>
> // So will this ...<br/>
> var r = new java.lang.Character('r');<br/>
> writer.write(r);<br/>
> writer.endDocument();<br/>

This problem can happen easily when you're creating an <a href="http://openntf.org/XSnippets.nsf/snippet.xsp?id=xagent">XAgent</a>. Be careful what you feed the `writer.write()`. If you're not, you might get some hard to debug errors.

Happy hacking!
