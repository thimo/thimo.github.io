---
title: "Bulk cleanup old Working Set databases (updated)"
permalink: "/2011/05/bulk-cleanup-old-working-set-databases.html"
description:
categories:
  - domino designer
---
**Updated 15-05-2011 20:35**: As Nate commented, it turns out to be possible to bulk-select-and-remove database entries from the Working Sets with the Package Explorer view, without actualy removing the .nsf's themselves. Please <a href="http://2011/05/bulk-cleanup-old-working-set-databases_15.html">read the new post</a>.

Domino Designer's Workings Sets are a great help in the life of a Lotus Domino developer. They make switching between projects a lot easier and up your productivity. But after a while they become cluttered with databases you might not be working on anymore.

Cleaning up from either Designer's Application view or the "Managing Working Sets" popup can only be done one database at a time. And it's a slow process, not something you'd want to full your working day with. The Package Explorer view does support a multiple-select and delete, but that action will not only remove the Working Set reference, but also delete the Notes/Domino database itself!

If you're not afraid of editing XML by hand (if you are, you are probably reading the wrong blog), my previous blog post on <a href="/2011/05/recover-designer-working-sets.html">recovering Working Sets</a> can help. The Working Set databases are stored in an XML file in your Notes Data directory:

> notes\data\workspace\.metadata\.plugins\org.eclipse.ui.workbench\workingsets.xml

You can open the XML file in an editor (<a href="http://notepad-plus-plus.org/">Notepad++</a> is my favorite) and remove unwanted databases. Make sure you close Domino Designer before you start editing. Also, please pretty please, make a backup from the workingsets.xml file first and/or use a decent backup program (I still recommend <a href="http://www.crashplan.com/consumer/crashplan-plus.html">CrashPlan+</a>).

Note: As far as I know this will not remove the data that has been stored on disk in the notes\data\workspace directory, so you might want to clean that one up to.

I've heard managing Working Sets will be approved upon in an upcoming version of Designer. But until then, you can do it by hand and save yourself a lot of time.
