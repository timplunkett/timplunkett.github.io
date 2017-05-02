---
layout: post
title:  "Using `git bisect run` with Drupal"
date:   2017-05-02 10:45:00 -0400
---
For a primer on the basic usage of `git bisect`, please read [webchick's excellent post](http://webchick.net/node/99) from several years ago. That post was my first introduction to using this helpful command.

<hr><br>

## The scenario

**Panels 8.x-4.0** was released last Friday, during the code sprints at DrupalCon Baltimore (a post with more info about this sprint is forthcoming). In the hours leading up to the release, there were several issues committed in rapid succession. And because of the flurry of activity through the issue queues, the automated test system was a bit backed up.

Due to sheer enthusiasm, no one noticed that the tests hadn't finished running, and a release was committed. When the dust settled, several people [reported a bug](https://www.drupal.org/node/2874489) in the UI.

The day began with a working release, **Panels 8.x-4.x-beta1**. There had only been a few commits between the beta and the stable release, and I could have tracked down the bug manually. Instead I reached for `git bisect`, because what's the point of knowing a tool if you never use it?!

## First attempt

```bash
git bisect start 8.x-4.0 8.x-4.0-beta1
```
After telling bisect the *bad* commit and the most recent known *good* commit, git will begin to step through each intermediate commit. At each step you must determine if the current commit is good or bad.

In this case there was a specific test that was failing, the `testLayoutSettings()` method in the `Drupal\panels\Tests\PanelsTest` class.

I could have run this test through the graphical runner, but instead chose to use the `run-tests.sh` script that ships with Drupal core:
```bash
# ../../ because this is from within a module directory
  php ../../core/scripts/run-tests.sh
# Web tests need to know where to find the local site
  --url "http://d8"
# This can be a class, or class::method
  --class "Drupal\panels\Tests\PanelsTest::testLayoutSettings"
```
Based on the output of this script, you then can run `git bisect good` or `git bisect bad`, and then repeat for each step. Eventually you will end up with the commit that introduced the error.

## Let's automate that!

Why do something manually if you can automate it?

Bisect supports a `run` subcommand, allowing you to pass any script for it to run on each step. The script should return 0 or 1 to indicate bad or good commits. Thanks to the work [done here](https://www.drupal.org/commitlog/commit/2/37705f2a8220776d86d333597a24cb40116981f5), `run-tests.sh` uses the correct exit codes!
```bash
git bisect start 8.x-4.0 8.x-4.0-beta1
git bisect run php ../../core/scripts/run-tests.sh
  --url "http://d8"
  --class "Drupal\panels\Tests\PanelsTest::testLayoutSettings"
```
Now there is no need to attend to each step, you can do something else while it runs, and when you are done the offending commit will be displayed!

## One step further
Why remember a command when you can create an alias?
```bash
#!/bin/bash

# USAGE
# git bisect-test <bad> <good> <test>

# CONFIGURATION
SITE_DIR='/Users/tim/www/d8'
SITE_URL='http://d8'

git bisect reset
git bisect start $1 $2
git bisect run php $SITE_DIR/core/scripts/run-tests.sh
  --url $SITE_URL --class $3
```
By creating this file, naming it `git-bisect-test`, and putting somewhere in your `$PATH`, you now use a single command:
```bash
git bisect-test 8.x-4.0 8.x-4.0-beta1 "Drupal\panels\Tests\PanelsTest::testLayoutSettings"
```

I hope this helps you as much as it helps me, happy bisecting!