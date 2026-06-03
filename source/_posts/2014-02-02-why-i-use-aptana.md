---
title: Why I Use Aptana
date: 2014-02-02T13:48:36+00:00
---
Most of the developers I know use one of the following IDEs:

  * [Sublime Text](http://www.sublimetext.com/) &#8212; The latest and greatest for many front-end devs
  * vim or [MacVim](https://code.google.com/p/macvim/) &#8212; A staple for back-end engineers, among others
  * [TextMate](http://macromates.com/) &#8212; For folks who were down with Macs since Day One
  * Emacs &#8212; Do you want to use an editor from the 70s but feel like vim is too mainstream? Emacs has you covered.

I am an outlier among my peers in that I use [Aptana](http://www.aptana.com/). At least for now. Before you judge me, please continue reading.

For those who aren&#8217;t familiar with Aptana, it is a fork of the Eclipse project that combines Eclipse&#8217;s formidable plug-in base with a lot of polish added by the Aptana team.  I&#8217;ll admit that part of my reason for continuing to use it is that I&#8217;m simply too lazy to switch to Sublime Text (which has more momentum behind it) or vim (which would let me use the same local IDE as I use for remote editing). However, the simple fact that Aptana so thoroughly meets my needs means I don&#8217;t have any pressing reason to switch.

The main reason I love it is that it brings me down to needing only two open windows while I&#8217;m working: Aptana for code edits and commits and a terminal window to view the results of automated unit tests (plus a third window when I need an actual browser). The git integration in Aptana is second-to-none and I can visually inspect and stage my commits without leaving my editor. It does however lack support for staging selective hunks of a file, or generating a new feature branch &#8212; two tasks in which I still need to turn to terminal. Aptana also has seamless FTP support which makes pushing changes to shared hosts a breeze. There&#8217;s also Heroku deployment that I&#8217;ve been meaning to play around with.

The configuration options can be a bit daunting because so much of the editor can be tweaked to your liking, but after using it for awhile, you&#8217;re generally able to find the settings you need. In fact, there&#8217;s a lot of cool stuff hidden in the configuration options, like executing PHP and other scripts direct from Aptana, as well as terminal and web browser integrations that can actually make Aptana a single-window solution. I haven&#8217;t played with these too much because I do prefer to have these applications open in separate windows.

For me, it ultimately comes down to the fact that I&#8217;m comfortable with Aptana, and for the stuff I do 90% of the time, using it is second-nature. The core things you need an IDE for (editing, find/replace, easy navigation through large amounts of code, quick access to docs, version control) Aptana does incredibly well.

It does have its failings, however. For awhile, its stability was so poor that I affectionately referred to it as Craptana. With the release of version 3.0, however, stability is generally rock solid (apart from the occasional, interminable &#8220;Building Workspace&#8221; loading message).

My main beef with Aptana at this point is the bizarre window management scheme. Aptana lets you detach windows, move them around and switch between some sort of &#8220;tiled mode&#8221; and &#8220;overlay mode&#8221;. The problem is that I don&#8217;t want any of this functionality and when I accidentally detach something, I can&#8217;t always figure out how to put it back. This is best illustrated with some screenshots.

Here is how Aptana looks when it&#8217;s behaving itself:

<p style="text-align: center;">
  <a href="/images/uploads/2014/02/Web_-_dan24678_app_webroot_js_validanguage_api_js_-_Aptana_Studio_3_-__Users_dand_git.png"><a href="/images/uploads/2014/02/Web_-_dan24678_app_webroot_js_validanguage_api_js_-_Aptana_Studio_3_-__Users_dand_git.png"><img class="wp-image-150 aligncenter" alt="Web_-_dan24678_app_webroot_js_validanguage_api_js_-_Aptana_Studio_3_-__Users_dand_git" src="/dan24678/images/uploads/2014/02/Web_-_dan24678_app_webroot_js_validanguage_api_js_-_Aptana_Studio_3_-__Users_dand_git-1024x794.png" width="614" height="476"  /></a></a>
</p>

&nbsp;

But if I start playing with the windows and moving shit around, I eventually end up with this:

<p style="text-align: center;">
  <a href="/images/uploads/2014/02/Web_-_dan24678_app_webroot_js_validanguage_api_js_-_Aptana_Studio_3_-__Users_dand_git-15.png"><img class="aligncenter  wp-image-151" alt="Web_-_dan24678_app_webroot_js_validanguage_api_js_-_Aptana_Studio_3_-__Users_dand_git-15" src="/dan24678/images/uploads/2014/02/Web_-_dan24678_app_webroot_js_validanguage_api_js_-_Aptana_Studio_3_-__Users_dand_git-15-1024x796.png" width="614" height="478"  /></a>
</p>

&nbsp;

Every time I get fed up with Aptana and am tempted to make the switch to Sublime Text, it seems to sense my frustration and begins behaving itself. It must have learned that trick from my daughter.

When I&#8217;m working, I prefer to focus my energy on the task at hand and not obsess over refinements to my tool chain (sometimes to a fault). This is the main reason it&#8217;s 2014 and I&#8217;m still using Aptana &#8212; it stays out of my way, does what I need it to do and let&#8217;s me move quickly between tasks.