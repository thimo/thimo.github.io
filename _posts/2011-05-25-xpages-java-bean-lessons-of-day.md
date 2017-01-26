---
title: "Xpages - Java Bean lessons of the day"
permalink: "/2011/05/xpages-java-bean-lessons-of-day.html"
description:
categories:
  - xpages
---
This Xpages stuff is still fairly new to me and there are lots of little things I struggle with. Today was spend trying to figure out how to build some of the log-in/log-out logic. For a couple of problems I could not find a direct answer on the internet, so I thought I'd share what I came up with.

### Remove a bean from the session

In the application I'm building there's this "User" bean that holds all kinds of user data and provides some application logic. When the user clicks the log out button, it would be neat to cleanup that bean. The following line of SSJS code removes the "User" bean.

```
facesContext.getExternalContext().getSessionMap().remove("User");
```

### Invalidate the session

Another option for the log-out cleanup function is to invalidate the whole session, which you can do with the following line of SSJS code:

```
facesContext.getExternalContext().getSession(true).invalidate();
```

Of course, this cleans up a lot more than just the User bean.

### Read property from resource bundle

In the beans, you would of course like to use the resource files. It's easy. Put the following Java method in a Utils class:

```
public static String getResource(String bundleVar, String name) {
 XSPContext xspContext = XSPContext.getXSPContext(FacesContext
   .getCurrentInstance());

 try {
  // There has to be bundle defined with var name <bundlevar>
  ResourceBundle bundle;
  bundle = xspContext.bundle(bundleVar);
   if (bundle != null) {
   return bundle.getString(name);
  }
 } catch (IOException e) {
  e.printStackTrace();
 }

 return "";
}</bundlevar>
```

And call it as follows:

```
Utils.getResource("settings", "db_users");
```

For reference, "settings" is the name of the bundle resource and "db_users" is the name of the setting. From the theme configuration:

```
<resources>
 <bundle>
  <src>/WEB-INF/settings.properties</src>
  <var>settings</var>
 </bundle>
</resources>
```

### Get a request variable

The User bean does some auto-login magic. To be able to do that it needs to read the request cookies. No problem! Take this static function, put it in some Utils class:

```
Utils.getCookies();
```

```
public static String getCookies() {
 // System.out.print(XSPContext.getXSPContext(
 // FacesContext.getCurrentInstance()).getUrl());

 HttpServletRequest request = (HttpServletRequest) FacesContext
   .getCurrentInstance().getExternalContext().getRequest();
 return request.getHeader("cookie");
}
```

You can modify this to read other request parameters, to extend the handling of cookies, etc..

Have fun!
