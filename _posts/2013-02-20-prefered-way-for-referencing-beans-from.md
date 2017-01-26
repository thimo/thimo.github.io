---
title: "Prefered way for referencing Beans from Java (updated 21-02)"
permalink: "/2013/02/prefered-way-for-referencing-beans-from.html"
description:
categories:
  - xpages
tags: [java, beans]
---

When working in XPages SSJS (Server Side Javascript) it is very easy to reference your Managed Beans. Say you want to print the name of the user to the console and have a bean named `User` with a method `getUsername()`, you simply call:

```javascript
print(User.getUsername());
```

In Java it is not quite that easy, you have to dig a lot deaper to reach the bean. Examples on how to to this are readily available on the internet, copy/paste one in a `Utils` class and you end up with something like this:

```java
package nl.defrog;

import javax.faces.context.FacesContext;

public class Utils {
    public static Object getVariableValue(String varName) {
        FacesContext context = FacesContext.getCurrentInstance();
        return context.getApplication().getVariableResolver().resolveVariable(context, varName);
    }
}
```

Still want to print the name of the current user to the console? Call:

```java
UserBean userBean = (UserBean) Utils.getVariableValue(UserBean.BEAN_NAME);
System.out.println(userBean.getUsername());
```

It’s quite a bit of code for just being able to get to the username.

PS: See what I did with the name of the bean? I stored it as the constant `BEAN_NAME` in the `UserBean` and made it publicly available. See below for the final version of the code.

### Singleton pattern

To clean things up I’m following something common from the Singleton pattern.

A Singleton is a Java class of which only one instance will ever exist in the entire JVM. You cannot simply instantiate a new instance of the class by calling the constructor, you have to call the static method `getInstance()` on the class to get a handle to the object (that’s the relevant variant for this case, there are <a href="http://stackoverflow.com/questions/70689/what-is-an-efficient-way-to-implement-a-singleton-pattern-in-java">other ways</a>).

This sounds a bit like a bean, where we don’t want to instantiate a new object, but want to retrieve the existing Managed Bean from either scope (application, session, view, request).

### getInstance() on a Bean

To get the instance of the bean that’s already in a scope (or have it instantiated at the first access) we add a `getInstance()` method to the bean. This method will call the `getVariableValue()` method from the `Utils` class shown above and have it return the bean we’re looking for in the correct type.

See the complete UserBean below.

```java
package nl.defrog.beans;

import nl.defrog.Utils;

import java.io.Serializable;

public class UserBean implements Serializable {
    private static final long serialVersionUID = 1L;
    public static final String BEAN_NAME = "User";
    private String username;

    public UserBean() {

    }

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public static UserBean getInstance(String beanName) {
        return (UserBean) Utils.getVariableValue(beanName);
    }

    public static UserBean getInstance() {
        return getInstance(BEAN_NAME);
    }
}
```

Now if we want to print the name of the current user to the console, all it takes is a simple:

```java
System.out.println(UserBean.getInstance().getUsername());
```

If you want to keep the bean around, you could call:

```java
UserBean userBean = UserBean.getInstance();
```

Nice and clean, isn’t it? I thought so. Happy hacking!

### Updated 21-02-2013 12:30
As per Tim's suggestion (see comments) I've overloaded the `getInstance()` method so you can provide a bean name when calling the method. This is handy when you have multiple beans using the same Java class.

Now also available on <a href="http://openntf.org/XSnippets.nsf/snippet.xsp?id=getinstance-in-a-java-bean-1">XSnippets</a>.
