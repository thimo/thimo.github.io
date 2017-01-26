---
title: "Don't use shared fields on mail forms"
permalink: "/2011/02/shared-fields-on-mail-forms.html"
categories:
  - lotus notes
---
Domino provides an easy way to send database documents by email (SMTP) without requiring any work on formatting it: let Domino use the document's form to figure out a way to render the ASCII email. In LotusScript it works like this:

```
Call <NotesDocument>.send(True)
```

Even when sent through SMTP, the email will still kinda look like the way the document's form was designed. Easy and convenient, when looks are not that important.

But today I just couldn't get the document to be included in the email. Whatever I tried, the sent email would be empty. The help for the "send" function tips about the use of computed subforms, but I wasn't using them:

> Call notesDocument.Send( attachForm [, recipients ] )
>
> [snip]
>
> attachForm - Boolean. If True, the form is stored and sent along with the document. If False, it isn't. Do not attach a form that uses computed subforms.

It turns out, after a lot of trial and error, you can't use **shared fields** either on a form. Without them, the document was sent without a problem. Okay, thanks, game on!
