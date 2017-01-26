---
title: "Multiple file upload, the easy way"
permalink: "/2011/04/multiple-file-upload-easy-way.html"
categories:
  - lotus notes
---
<a href="http://2.bp.blogspot.com/-tCu4PIl8e7k/TbW7V8KPq1I/AAAAAAAAABU/VlaFFjPr63c/s1600/Screenshot%2B-%2B25-4-2011%2B%252C%2B20_19_37.png" onblur="try {parent.deselectBloggerImageGracefully();} catch(e) {}"><img alt="" border="0" height="153" id="BLOGGER_PHOTO_ID_5599587697305824082" src="http://2.bp.blogspot.com/-tCu4PIl8e7k/TbW7V8KPq1I/AAAAAAAAABU/VlaFFjPr63c/s200/Screenshot%2B-%2B25-4-2011%2B%252C%2B20_19_37.png" style="float: right; margin: 0pt 0pt 10px 10px;" width="200" /></a>
It turns out to be very easy to offer a multiple file upload control in Lotus Domino. If, and that's the big one, the end-user is on Firefox, Safari or Chrome. Internet Explorer, even including version 9, doesn't support this.

The steps are as follows:

- Add a File Upload Control to a Notes form
- Open its properties and go to tab ""
- Add "multiple" to the field "Other"

<a href="http://2.bp.blogspot.com/-Eipavcs7n4M/TbW9_qmkTGI/AAAAAAAAABc/r3XIR8Gy0vY/s1600/Screenshot%2B-%2B25-4-2011%2B%252C%2B20_30_14.png" onblur="try {parent.deselectBloggerImageGracefully();} catch(e) {}" style="clear: left; float: left; margin-bottom: 1em; margin-right: 1em;"><img alt="" border="0" height="233" id="BLOGGER_PHOTO_ID_5599590613170539618" src="http://2.bp.blogspot.com/-Eipavcs7n4M/TbW9_qmkTGI/AAAAAAAAABc/r3XIR8Gy0vY/s320/Screenshot%2B-%2B25-4-2011%2B%252C%2B20_30_14.png" width="320" /></a>Open the form in one of the supporting browsers, click "Browse" and you can select and upload multiple files with one upload control. Lotus Domino handles this as you would expect.

Yeah!
