---
title: "Recover Designer Working Sets"
permalink: "/2011/05/recover-designer-working-sets.html"
categories:
  - domino designer
---
If your Domino Designer environment is as stable as mine, you loose your precisely crafted Working Sets at least once a week. I still do not know why Designer is as instable as it has been the last few weeks, but at least I have found a way to recover a lost Working Set. Hooray!

The first step is to make sure you have a backup of the notes/data directory. I recommend <a href="http://www.crashplan.com/consumer/crashplan-plus.html">CrashPlan+</a>. The family plan is hard to beat in price if you have more than one computer or a couple of VM's. It allows you to do offsite cloud backups, with up to 10 computers, unlimited in size. Additionally, you can backup to a local drive, NAS or friends computer (free, free, free). There's no excuse not to do backups.

With that out of the way, the Working Sets are stored in a file in your notes/data directory:

> notes\data\workspace\.metadata\.plugins\org.eclipse.ui.workbench\workingsets.xml

Each time Domino Designer starts up with blank Working Sets,  close Notes and Domino Designer, restore the file mentioned above from your backup system and start Notes / Domino Designer.

Game on!
