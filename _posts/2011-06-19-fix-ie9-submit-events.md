---
title: "Fix IE9 submit events"
permalink: "/2011/06/fix-ie9-submit-events.html"
description:
categories:
  - xpages
---
In my previous blog on <a href="/2011/06/fix-default-type-for-submit-buttons-in.html">how to fix the default type for buttons in IE</a> I ended with a remark that although the method described fixes the button type, in Internet Explorer 9 the save action still does not work. This seems to be IE9 specific, as IE8 works as expected.

After a bit of reverse engineering I found the cause of the problem and came up with a solution.

Looking at the submitted data there is a difference between Firefox and Internet Explorer 9. In IE9 the field `$$xspsubmitid` is empty, in Firefox it is filled with the id of the server side event that should be triggered (i.e. "`view:_id1:_id2:_id7:event1"`).

At the bottom of the webpage there's a bit of Javascript code that 'binds' the server side event to the click on the button:

```
XSP.addOnLoad(function() {
XSP.attachEvent("view:_id1:_id2:_id7:event1", "view:_id1:_id2:_id7:button1", "onclick", null, true, 2);
}); ```

The method `attachEvent` calls `XSP.addOnLoad` and that's where funny things happen. This is the method, un-minified from `/domjs/dojo-1.4.3/ibm/xsp/widget/layout/xspClientDojo.js`:

```
this.addOnLoad=function x_aol(_1b9){
    if(!this._listeningForDojoOnload){
        this._listeningForDojoOnload=true;
        dojo.addOnLoad(this,"_loaded");
    }
    this._onLoadListeners.push(_1b9);
};```

On the fourth line the `dojo.addOnLoad` method is called to add a call to XSP's method `_loaded`. This method executes the code to add listeners that have been added to the `_onLoadListeners` array.

Now, what's all this about? It turns out that IE9 calls the `_loaded` method immediately after it is added on line 4, before the listener events have been added to the `onLoadListeners` array. These client side events will never be added, and thus the server side event will not be fired on submit.

The solution? Override the `addOnLoad` with a slightly modified version and add it somewhere in the current page in a scriptBlock (or Javascript client side library). This modified version fills the `onLoadListeners` array before adding a call to the `_loaded` method so that the listeners will always be added, even in IE9.

```
XSP.addOnLoad=function x_aol(_1b9){
    this._onLoadListeners.push(_1b9);
    if(!this._listeningForDojoOnload){
        this._listeningForDojoOnload=true;
        dojo.addOnLoad(this,"_loaded");
    }
};```

The cause of the problem is probably in Dojo's `addOnLoad`. But I expect Dojo's code to be less easy to fix and settled for this solution.

Game on!
