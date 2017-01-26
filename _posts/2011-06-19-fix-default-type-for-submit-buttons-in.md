---
title: "Fix default type for submit buttons in IE (updated)"
permalink: "/2011/06/fix-default-type-for-submit-buttons-in.html"
description:
categories:
  - xpages
---
For submit buttons - you know, the new `<button>Click me</button>` type - to work properly in Internet Explorer you have to explicitly add the type of the button. So the above example becomes `<button type="submit">Click me</button>`.

With Themes in Xpages it's very easy to fix this on an application wide level.

- Open your favorite theme
- Add the following tags:
```
<control>
  <name>Button.Submit</name>
  <property>
    <name>type</name>
    <value>submit</value>
  </property>
</control>
```

Now all the buttons in your application with an Xpages  button of "Button type" "submit" will have the type added to fix IE.

Happy hacking!

**Update **19-06-2011 19:50: While this fixed the submit action, in IE9 the form still won't be saved. The problem is that on the client XSP tries to attach an event to the button, but fails to do so. Not so happy right now.

**Update **19-06-2011 21:45: The IE9 problem has been solved, please read <a href="/2011/06/fix-ie9-submit-events.html">Fix IE9 submit event</a>.
