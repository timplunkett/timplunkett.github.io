---
layout: post
title:  "Find followers of a Drupal.org issue"
date:   2021-05-17 16:30:00 -0400
---
It's been almost 10 years since Drupal.org introduced [support for following issues without having to leave a comment](https://www.drupal.org/forum/general/news-and-announcements/2011-10-11/stop-subscribing-start-following).
The number of people following an issue is shown in the issue search and on the issue itself.

But nowhere in the UI can you see _who_ is following an issue. (The [issue discussing this](https://www.drupal.org/project/project_issue/issues/1304558) hasn't seen any movement in 7 years).

Thankfully the [Drupal.org API](https://www.drupal.org/drupalorg/docs/apis/rest-and-other-apis) _does_ expose this information. Until the UI does, here is a short shell script to parse out this information:

<script src="https://gist.github.com/timplunkett/9b66e6fcd6d07f767c0a3e8167166e29.js"></script>

Note that the script requires [jq](https://stedolan.github.io/jq/).

Any feedback on my functional but unpolished shell scripting skills is welcome.
