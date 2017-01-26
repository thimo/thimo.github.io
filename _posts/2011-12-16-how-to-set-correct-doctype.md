---
title: "How to set the correct DOCTYPE"
permalink: "/2011/12/how-to-set-correct-doctype.html"
description:
categories:
  - lotus notes
  - xpages
---
<div class="separator" style="clear: both; text-align: center;"><a href="http://1.bp.blogspot.com/-Ud27rEAeo40/Tuj5nExBdtI/AAAAAAAAAC8/RaKaeF84hDM/s1600/doctype.jpg" imageanchor="1" style="clear: right; float: right; margin-bottom: 1em; margin-left: 1em;"><img border="0" height="100" src="http://1.bp.blogspot.com/-Ud27rEAeo40/Tuj5nExBdtI/AAAAAAAAAC8/RaKaeF84hDM/s200/doctype.jpg" width="200" /></a></div>By default Domino prints out the following, half decent DOCTYPE, leaving out the link to the DTD:

```
<!DOCTYPE HTML PUBLIC
   "-//W3C//DTD HTML 4.01 Transitional//EN">
```

This causes browsers to render the page in quirks mode and not care very much about standards. This makes it harder to get pages to render the same in the different browsers (Internet Explorer, Firefox, Chrome).

If you care about a correct layout of the page you would want something like:

```
<!DOCTYPE HTML PUBLIC
   "http://www.w3.org/TR/html4/strict.dtd">
```

Or if you're lazy, like I am, and maybe want to pretend to do HTML 5, use:

```
<!DOCTYPE html>
```

For **Xpages** it's simply a setting in the Application Properties, Xpages tab:

<div class="separator" style="clear: both; text-align: center;"><a href="http://3.bp.blogspot.com/-Ji7EPUOhyIQ/Tuj6q0TKVkI/AAAAAAAAADE/ori_S_x-gpU/s1600/Screenshot+-+14-12-2011+%252C+20_35_17.png" imageanchor="1" style="margin-left: 1em; margin-right: 1em;"><img border="0" src="http://3.bp.blogspot.com/-Ji7EPUOhyIQ/Tuj6q0TKVkI/AAAAAAAAADE/ori_S_x-gpU/s1600/Screenshot+-+14-12-2011+%252C+20_35_17.png" /></a></div>

For a **LotusScript agent** as straightforward as printing the content-type, a blank line and then the doctype.

```
Print "Content-type: text/html"
Print
Print "<!DOCTYPE html>"
Print "<html>"
...
```

For **forms** (and views that are captured in a $$ViewTemplate form) it's a bit harder, **unless **you know how to do it:

- Create a computed for display field named "$$HTMLFrontMatter"
- Hide it
- Set its value to"<!DOCTYPE html>" (or whatever doctype you prefer)

Et voilà! That's it. Happy little doctypes, causing browsers to render the pages in standards compliant mode.

Happy hacking!
