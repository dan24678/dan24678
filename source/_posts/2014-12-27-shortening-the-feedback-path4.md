---
title: Shortening the Feedback Path
date: 2014-12-27T20:38:13+00:00
---
A subconscious best practice I&#8217;ve noticed in the world of web development is the drive to shorten the feedback path. This is best illustrated by an example.

Let&#8217;s pretend that a web form has various collapsible sections. The top section is expanded by default, pushing the bottom (collapsed) section down below the fold. Once the bottom section is expanded, there is a link that when clicked opens up a modal date-picker.

If you don&#8217;t optimize things, your workflow may look something like this:

  1. Make some changes to the date picker javascript
  2. Refresh the page
  3. Click to collapse the top section, bringing the bottom section into view
  4. Click to expand the bottom section
  5. Click the link to show the date picker
  6. Test your change

Obviously, this is a somewhat sub-optimal workflow that can grow quite painful quickly. Luckily, there are a number of different approaches to streamlining things a bit.

## The Best Best Practice: Tests

The ideal situation for developing your date picker is to do it in a test-driven manner.

If you can make the case that tests are worth doing (and they usually are), it&#8217;s definitely the way to go.  This is especially true if you are writing a library or a widget that is intended to be used in numerous different places. If our fictitious date picker is something we are writing from scratch, even if it is only likely to be used on this one page, the best way to go about it is to treat it as a widget with its own test suite and develop it in isolation from the rest of the page.

Tests provide immediate feedback. When you make a change to your date picker, you can have your tests auto-run in a separate window and get instant gratification. This completely side-steps the painful process of testing a widget that is hidden deep in the interactive guts of a complicated form.

The idea of doing test-driven development in javascript only became a reality fairly recently. There are many different combinations of javascript libraries that can be used to facilitate this, but the ones I find myself using most frequently are:

  * <a href="http://jasmine.github.io/" target="_blank">Jasmine</a>: Provides a BDD-based testing framework
  * <a href="http://phantomjs.org/" target="_blank">PhantomJS</a>: Enables headless browser testing. You can run meaningful tests against a WebKit-based browser solely from the commandline
  * <a href="http://gruntjs.com/" target="_blank">Grunt</a>: A task-runner to tie all the pieces together

## Sometimes You Can&#8217;t Do What You Should

I wouldn&#8217;t quite call unit tests for javascript a luxury for the same reason a seatbelt isn&#8217;t a luxury. Actually, a better analogy is probably an air bag in the early-90s.  All the nice cars had them and everyone knew air bags are the way to go and something you should have in your car.  But you spent all summer working at Dairy Queen for $4.65 an hour and only saved up enough money to buy an &#8217;82 Buick Regal with white leather seats, an FM radio and no air bags.

And so, sometimes we find ourselves debugging a date picker without recourse to tests.

In order to avoid the painful multi-step process of refresh, click, scroll, then click again to see the results of our change, there are a couple things we can do.

## Automate the Clicking

Rather than forcing yourself to click on the page elements each time, figure out what the equivalent jQuery code is to trigger these elements and then automate it.

The code might look something like this:

<pre>$('.collapsible-header:first').click(); // hide the top section</pre>

<pre>$('.collapsible-header:nth-child(3)').click(); // show the bottom section</pre>

<pre>$('#dingleberry-form a.picker-trigger').click(); // show the date picker</pre>

There&#8217;s a couple ways you can trigger this code. The easiest is to simply type it into the javascript console as a 1-liner and trigger it manually. You can then use &#8220;arrow-up&#8221; to get to it after each page refresh. This isn&#8217;t a bad way to go, but we can do better.

Another option is to surround it with a $(function() { }) so it will be called onload and put it into our page&#8217;s JS code somewhere. This completely automates it with only a single drawback: you may forget to remove it later. Add a &#8220;TODO: Remove this shit&#8221; comment above the code to help draw attention to it later.

Or even better, install <a href="https://chrome.google.com/webstore/detail/dotjs/dlnccnmhpmdidoiecanghgienhoglnim?hl=en" target="_blank">dotjs</a> or a similar extension so you can execute arbitrary javascript on your pages without actually putting it in your versioned, &#8220;production&#8221; files. But at that point, maybe you should just write a test.

## Keep Shit Modular

Another way to handle hiding the section you don&#8217;t want to see is to simply delete it off the page temporarily. If you&#8217;ve kept your code modular and concise then each page section will be its own template that you can suppress by commenting out a single line.

This technique proved invaluable to a page redesign I&#8217;m currently working on. There are 3 sections to the page and I placed each section in its own template. When I need to make a change to one of these sections, I can easily comment out the 2 lines in the parent template that include the sections I don&#8217;t want to see.

This technique really makes me appreciate jQuery&#8217;s complacent attitude when something doesn&#8217;t exist &#8212; e.g., _$(&#8216;.jacobs-ladder&#8217;).css(&#8216;height&#8217;, &#8216;10000px&#8217;)_ won&#8217;t throw any errors if there are no such elements on the page.

## Auto-Refresh the Page

Manually clicking the refresh button on the browser is for suckers. There are a couple good options for automating this:

  * [grunt-contrib-watch](https://www.npmjs.com/package/grunt-contrib-watch): A Grunt plugin for directory watching. A great option even for non-javascript projects
  * [Tincr](http://tin.cr/): A chrome extension for auto-updating JS/CSS that&#8217;s been changed without needing a full-page reload
  * [fswatch](https://github.com/emcrisostomo/fswatch): A general purpose solution that lets you trigger directory monitoring on-the-fly.
  * [reload.sh](https://github.com/dan24678/reload): A shell script written by yours truly. It is basically a wrapper around fswatch that lets you specify defaults and has added support for triggering browser reloads on OS-X.

One interesting aside is I&#8217;ve noticed that none of these solutions (even mine) really give me exactly what I want. Sometimes the detection of when a file has changed is slow and I&#8217;m left staring at the browser waiting on it to pick up the change, until I get frustrated and click it myself (thus defeating the purpose of the directory monitoring)*. Other times I will save a file and I _won&#8217;t_ want it to refresh the browser. Or I&#8217;ll save 5 files and only want a refresh on the last save. Still, it&#8217;s worth it for those times when I make a big change then switch over to my browser and the refreshed code is there waiting for me.

*One notable instance of when tests take a long time to run in response to a change occurs when VMs are in the mix. I&#8217;ve noticed that if you are monitoring a very large directory on a VM which is using a shared file-system such as NFS, changes can take tens of seconds to be detected. A great workaround is to monitor the large directory natively (outside of the VM) where directory monitoring performs well. When the change is detected, jiggle a file in a small, otherwise empty directory and then have the VM monitor **that**. This generally performs much better.

## Case Study: Writing reload.sh

I wrote [reload.sh](https://github.com/dan24678/reload) because I wanted to be able to set up browser reloading on-the-fly without needing to configure a Gruntfile. Also, it was a great excuse to write something in bash. My process for writing the script is itself illustrative of several steps I took to shorten the path to feedback.

While it would have been ideal to write tests for reload.sh then use it to trigger tests against a separate instance of itself, this wasn&#8217;t to be. This is unfortunate as it would&#8217;ve been super-meta to be able to use this script on itself. Instead, I opted to simply have it execute an &#8220;echo foo&#8221; when a file changed so I could see it was working. This initially resulted in the following workflow during development:

  1. Make a change to the script
  2. Click into a console window and run reload.sh (ask it to monitor /tmp/ and &#8220;echo foo&#8221; when it detects a file change)
  3. Click into a second console window and &#8220;touch /tmp/myself&#8221;. Watch for the output on console 1.
  4. Click back into the original console window and hit control-c to exit the script so I&#8217;m ready for my next change

This proved cumbersome but I quickly hit on a way to remove steps 3 & 4. I wrote a separate shell script to touch myself and then kill myself so I wouldn&#8217;t have to do it by hand:

<pre>touch /tmp/myself ; sleep 2; pkill -f reload.sh</pre>

The only downside to this workflow was that because reload.sh ran in a loop, I had to invoke my second script from within my first script (which meant I had one line in my script that I&#8217;d have to remove or comment out prior to committing it back into version control).

### In Conclusion

Shortening the feedback path may not always save you time, but it **will** save your sanity. Being able to immediately see the results of your work creates a much more pleasant experience. Use automation and whatever hacks you need to keep yourself from needing to click on anything. You&#8217;ll be better off for it.

&nbsp;