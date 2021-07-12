---
layout: post
title:  "Checking the status of a Drupal issue fork"
date:   2021-07-12 12:20:00 -0400
---
Late last year, Drupal.org [enabled Gitlab-based merge requests for all projects](https://www.drupal.org/node/3181553). Instead of the typical patch-based workflow, each issue can have its own fork, with any number of branches and merge requests (MRs).

Because each issue's branches live in a different fork, this leads to many, many git remotes in your local repo.
Over time, as those MRs are merged or closed, there's not a straightforward way to close them all.

Of course, I decided to address this with a script (similar to the [previous post]({{page.previous.url}}), this requires [jq](https://stedolan.github.io/jq/)).
<script src="https://gist.github.com/timplunkett/ffecca0711ec3f296ba5c6e83c3ade77.js"></script>

At first I wrote a script that directly checked for any of the fixed/closed statuses, and ran `git remote remove` for each of them. But that was rather scary. My next iteration instead _printed out_ the `git remote remove` command for each fork.

But as I looked at it more, I decided it would be nice to simply print out all the remotes, grouped by their status. This way you can make your own decisions on what to do with the leftover ones:
```
$ git remote-status
Needs work:
	drupal-3111192
Needs review:
	drupal-2935999
	drupal-3025231
	drupal-3164389
```

As always, any improvements are welcome!
