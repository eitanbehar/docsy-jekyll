---
title:  "Powershell: Delete all local branches except master"
date:   2020-12-10 
categories: powershell
badges: 
 - type: warning
   tag: use carefully
tags:
 - powershell
 - git
---

Assuming your local git repo is on master, you can delete all local branches except master using this one-line script:

~~~ bash
git branch | foreach { $_.split( "`n" ) } | foreach { if ($_ -ne '* master' ) { git branch -D $_.replace(' ', '') } }
~~~

<!--more-->

I strongly recommend doing first a **dry-run** to see which branches will be deleted:

~~~ bash
git branch | foreach { $_.split( "`n" ) } | foreach { if ($_ -ne '* master' ) { write $_ } }
~~~ 

After I wrote the code above, I found this one: 
<https://dev.to/koscheyscrag/git-how-to-delete-all-branches-except-master-2pi0>
*which seems nicer :)*
