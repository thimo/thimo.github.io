---
title: Inline HTML vs. Components, the showdown
permalink: "/2013/02/inline-html-vs-components-showdown.html"
description: ""
categories:
  - xpages
---

The last few days there was a bit of discussion in the XPages community on using inline HTML in XPages versus components. So whether you should write `<div>My little div</div>` (inline HTML) or `<xp:div>My little div</xp:div>` (component).

<a href="https://twitter.com/PaulSWithers">Paul Withers</a> wrote <a href="http://www.intec.co.uk/html-or-xpage-component/">a very nice blog (HTML or XPage Component)</a> explaining what happens behind the scenes when you save your XPage. Read the blog if you haven't already, you won't regret it! To summarize: the XPage's XML is converted to Java and, compared to the XPage with inline HTML, for the XPage with components less Java code is generated. Great, but Paul doesn't explain whether that's a plus or not.

The next day there's a follow up blog by <a href="https://twitter.com/timtripcony">Tim Tripcony</a> titled <a href="http://www.timtripcony.com/blog.nsf/d6plinks/TTRY-94Z52Z">Passthru vs. component - my perspective</a>. Tim dives deeper into the internals and provides even more insight into what happens behind the scenes. Again, read the blog! The bottom line: it might be that inline HTML produces more Java code, but the classes involved are less complex and quite likely rendering them will take a bit more effort and time.

### The showdown

So when using components there's less Java code to be generated, but the rendering of the components will quite likely take a bit more time. How much, is it a significant disadvantage? And additionally, how much do you loose in performance when you chose `xp:panel` over the dedicated components like for instance `xp:div`.

For the test I've created an XPage with three repeaters. Each repeater will run 10.000 times and create the HTML equivalent of `div.myClass>span>table>tr>td*2` (<a href="http://docs.emmet.io/">Emmet</a>). The first one will use the dedicated components, the second one generic panel components and the third will use inline HTML. For each repeater I will measure how long it takes to run it.

Note: the HTML output will be hidden with a style "display: none;", to keep things readable in the browser. As this is a client-side-only setting, it will not affect the outcome of the test.

Running the test page (for the code, see below) 10 times and averaging the numbers gives the following outcome:

 <table class="marked">    <caption>Run time for repeater</caption>    <tr>        <td>Inline HTML</td>        <td style="text-align: right;">132ms</td>        <td></td>    </tr>    <tr>        <td>Components</td>              <td style="text-align: right;">226ms</td>        <td style="text-align: right; font-weight: bold;">+ 94ms</td>    </tr>    <tr>        <td>Panels</td>              <td style="text-align: right;">283ms</td>        <td style="text-align: right; font-weight: bold;">+ 57ms</td>    </tr>    <tr>        <td colspan="3" style="font-style: italic;">Average from 10 runs</td>    </tr></table>

So, when you have a repeater that runs 10,000 (ten thousand) times, and you create 6 nested components on each run, you loose 94ms in total on components compared to using inline HTML. For using panels there's an extra cost of 57ms.

The positive thing to take away from this is that it really doesn't matter, speed-wise, if you use inline HTML or XPages components (incl. panels). Which of the two to use depends on a) your own personal preference and b) whether you need the use the extra functionality the XP components provide.

###  A caveat

The test I did is a bit primitive and doesn't represent something you'd do in The Real World(™). If you look at the code below, you'll find a disabled `xp:text` component. In one of the `td`'s the current line number is printed. If I enable that and re-run the test you get a slightly different outcome:

 <table class="marked">    <caption>Run time for repeater</caption>    <tr>        <td>Inline HTML</td>        <td style="text-align: right;">320ms</td>        <td></td>    </tr>    <tr>        <td>Components</td>              <td style="text-align: right;">482ms</td>        <td style="text-align: right; font-weight: bold;">+ 162ms</td>    </tr>    <tr>        <td colspan="3" style="font-style: italic;">Average from 10 runs</td>    </tr></table>

Interestingly the difference in the time it takes to render the HTML increases. I'm not sure why, but at least it gives a hint that the first test is a bit too simplistic to provide real world guidance. Your mileage may vary.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<xp:view xmlns:xp="http://www.ibm.com/xsp/core" xmlns:xc="http://www.ibm.com/xsp/custom">
    <xp:this.dataContexts>
        <xp:dataContext var="manyNumbers">
            <xp:this.value><![CDATA[${javascript:
var numbers = [];
for (var i = 0; i < 10000; i++)
    numbers.push(i);
return numbers;
}]]></xp:this.value>
        </xp:dataContext>
    </xp:this.dataContexts>

    <xp:text escape="true" id="computedField1" value="#{javascript:start = new Date(); return '';}"></xp:text>

    <xp:repeat id="repeat1" rows="#{javascript:manyNumbers.length}" var="number" value="#{manyNumbers}" style="display: none;">
        <xp:div styleClass="myDiv">
            <xp:span>
                <xp:table>
                    <xp:tr>
                        <xp:td>Number</xp:td>
                        <xp:td>
                            <!--<xp:text escape="true" value="#{number}"></xp:text>-->
                        </xp:td>
                    </xp:tr>
                </xp:table>
            </xp:span>
        </xp:div>
    </xp:repeat>

    Repeat with components: 
    <xp:text escape="true" id="computedField2" value="#{javascript:var now = new Date(); now.getTime() - start.getTime();}">
        <xp:this.converter>
            <xp:convertNumber type="number" integerOnly="true"></xp:convertNumber>
        </xp:this.converter>
    </xp:text>
    ms

    <br />

    <xp:text escape="true" id="computedField3" value="#{javascript:start = new Date(); return '';}"></xp:text>

    <xp:repeat id="repeat3" rows="#{javascript:manyNumbers.length}" var="number" value="#{manyNumbers}" style="display: none;">
        <xp:panel tagName="div" styleClass="myDiv">
            <xp:panel tagName="span">
                <xp:panel tagName="table">
                    <xp:panel tagName="tr">
                        <xp:panel tagName="td">Number</xp:panel>
                        <xp:panel tagName="td">
                            <!--<xp:text escape="true" value="#{number}"></xp:text>-->
                        </xp:panel>
                    </xp:panel>
                </xp:panel>
            </xp:panel>
        </xp:panel>
    </xp:repeat>

    Repeat with panels: 
    <xp:text escape="true" id="computedField5" value="#{javascript:var now = new Date(); now.getTime() - start.getTime();}">
        <xp:this.converter>
            <xp:convertNumber type="number" integerOnly="true"></xp:convertNumber>
        </xp:this.converter>
    </xp:text>
    ms

    <br />

    <xp:text escape="true" id="computedField4" value="#{javascript:start = new Date(); return '';}"></xp:text>

    <xp:repeat id="repeat2" rows="#{javascript:manyNumbers.length}" var="number" value="#{manyNumbers}" style="display: none;">
        <div class="myDiv">
            <span>
                <table>
                    <tr>
                        <td>Number</td>
                        <td>
                            <!--<xp:text escape="true" value="#{number}"></xp:text>-->
                        </td>
                    </tr>
                </table>
            </span>
        </div>
    </xp:repeat>

    Repeat with inline HTML: 
    <xp:text escape="true" id="computedField6" value="#{javascript:var now = new Date(); now.getTime() - start.getTime();}">
        <xp:this.converter>
            <xp:convertNumber type="number" integerOnly="true"></xp:convertNumber>
        </xp:this.converter>
    </xp:text>
    ms

</xp:view>
```
