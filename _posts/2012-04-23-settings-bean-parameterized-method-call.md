---
title: "Settings bean - parameterized method call from EL"
permalink: "/2012/04/settings-bean-parameterized-method-call.html"
description:
categories:
  - xpages
---
With Xpages it's convenient to keep your settings in a properties file. They're easy to create, maintain and access from EL (Expression Language), SSJS (Server Side JavaScript) and Java. And they're fast too! But what if your settings are somewhere else, like in a central database? What can you do to create something that works from all three languages (EL, SSJS and Java) and have it fast too?

Beans are great for solving these kind of problems. But there's one issue: it's not possible to use parameters from EL when calling methods.

Example: imagine you have this User bean and it has a method called `getName()`. From Java or SSJS you call `User.getName()`, from EL you use `User.name`. No problem.

Now you do the same with a Settings bean. You handcraft a method `getValue(String name)` that retrieves the value for the property that was passed as a parameter. From Java or SSJS you would call something like `Settings.getValue('db_names')`. But how do you call the same function from EL? `Settings.value['db_names']` won't work. The system expects the `getValue()` method to return a Map and will try to get the value for 'db_names' from that Map. The Settings bean itself won't even get to see the parameter 'db_names' and thus can't do any smart things itself. So how do you solve that without creating a hard coded method for every possible setting?

I found the answer in <a href="http://wiki.apache.org/myfaces/Parameters_In_EL_Functions" target="_self" title="">an article on MyFaces Wiki</a>. What you do is add a parameter-less method `getValue()` to the Setting bean, next to the `getValue(String name)` method that's already there. This method returns a object of a class that implements the Map interface. This class has a custom made implementation for the Map method get(). This method itself will see the 'db_names' parameter and with that is able to retrieve the correct value from its parent, the Settings bean.

Got it? No? Code speaks louder than words.

### Settings bean

```
package nl.thimojansenit.bean;

import java.io.Serializable;
import java.util.Date;
import java.util.HashMap;
import java.util.Map;

import nl.thimojansenit.DummyMap;

/**
 * @author Thimo Jansen <thimo@jansenit.nl>
 */
public class Settings implements Serializable {

 private static final long serialVersionUID = 1L;
 private Map<String, Cache> _cache = new HashMap<String, Cache>();

 /**
  * Retrieve cached value of setting. If not yet cached or value is too old (> 60 minutes), get fresh value
  *
  * @param name
  *            of setting to retrieve
  * @return value of setting
  */
 public String getValue(String name) {
  Date now = new Date();
  if (!_cache.containsKey(name) || (now.getTime() - _cache.get(name).getDatetime().getTime()) > 1000 * 60 * 60) {
   _cache.put(name, new Cache(getFromMap(name)));
  }

  return _cache.get(name).getValue();
 }

 /**
  * Get SettingsMap object through which EL can fetch the value for a setting
  *
  * @return SettingsMap
  */
 public SettingsMap getValue() {
  return new SettingsMap(this);
 }

 /**
  * Static method to retrieve settings, replace this code with your own logic
  */
 static private String getFromMap(String name) {
  Map<String, String> settings = new HashMap<String, String>();
  settings.put("hostname", "www.thimojansenit.nl");
  settings.put("db_names", "names.nsf");

  return settings.get(name);
 }

 /**
  * InnerClass used to provide settings to EL. This class uses the parent bean to fetch values for settings
  */
 @SuppressWarnings("unchecked")
 private class SettingsMap extends DummyMap implements Map {
  // Reference to parent Settings bean
  private Settings _settings;

  public SettingsMap(Settings settings) {
   _settings = settings;
  }

  public Object get(Object obj) {
   return _settings.getValue((String) obj);
  }
 }

 /**
  * InnerClass used to store cached values, includes date/time stamp
  */
 private class Cache {
  private Date _datetime;
  private String _value;

  public Cache(String value) {
   _datetime = new Date();
   _value = value;
  }

  public Date getDatetime() {
   return _datetime;
  }

  public String getValue() {
   return _value;
  }
 }
}
```

As explained above, this is just a Settings bean. It has a `_cache` map to store retrieved values in. It stores them as an object of class Cache with both the value and a time-stamp. The method `getValue(String name)` looks at the `_cache` map and tries to get the value from that. It it's not there or if it's too old, it gets the value from the method `getFromMap(String name)`. This method is the one that you normally would replace with a lookup in a database, or something similar.

So far so good. This works fine when called from Java or SSJS. But not in EL.

The special bits to make this work with EL are the `getValue()` method that returns a SettingsMap and the innerclass SettingsMap that implements Map and extends DummyMap.

The DummyMap (see below for code) is just there so you don't have to implement each required Map method every time you use this construction. Convenience.

The class SettingsMap itself has a constructor and an implementation of the `get()` method. The constructor expects a reference to the parent Bean as an argument, so the code can use that reference later on to easily retrieve values back from the Bean. With this it knows where to find the Bean.

The `get(Object obj)` method is what's called when the EL tries to get the value from the Map. Because this method now has our own implementation, we're able to see the parameter (which was not possible before) and retrieve the value on the fly. In this case we use the reference to the Settings Bean to call the `getValue(String name)` method there and return that.

### EL tear down

The EL for this call would look like this:

```
Settings.value['db_names']
```

Let's tear that down and explain what each part does.

`Settings` references the Settings bean, `value` calls the parameter-less `getValue()` method on the Settings bean that returns the SettingsMap. That Map is used to retrieve the value with the call `SettingsMap.get("db_names")`. Now as we've written the implementation for this `get()` ourselves, we now have the power to use the parameter and get the value from the parent's `getValue("db_names")` method and return that to the XPage.

Pretty clever right?

### DummyClass

As promised, here's the code for DummyClass:

```
package nl.thimojansenit;

import java.util.Collection;
import java.util.Map;
import java.util.Set;

// abstract class used by java server faces to pass parameter to a method as map key
@SuppressWarnings("unchecked")
public abstract class DummyMap implements Map {
 public Collection values() {
  return null;
 }

 public Object put(Object key, Object value) {
  return null;
 }

 public Set keySet() {
  return null;
 }

 public boolean isEmpty() {
  return false;
 }

 public int size() {
  return 0;
 }

 public void putAll(Map t) {
 }

 public void clear() {
 }

 public boolean containsValue(Object value) {
  return false;
 }

 public Object remove(Object key) {
  return null;
 }

 public boolean containsKey(Object key) {
  return false;
 }

 public Set entrySet() {
  return null;
 }

 // subclasses should override this method call their method with obj as the parameter
 public abstract Object get(Object obj);
}
```

Disclaimer/attribution: The code for this DummyClass is just about copy/pasted from the <a href="http://wiki.apache.org/myfaces/Parameters_In_EL_Functions">Myfaces Wiki.</a>

Hope this explains it a bit and that you found it useful. Happy hacking!
