---
title: 'Conways Game of Life'
date: 2014-06-04T20:45:28+00:00
---
Several months ago I was interviewing with various companies, primarily for senior web developer roles. In total I probably spoke with just under a dozen companies. Some had phone screens and some had in-person interviews. Some had written tests and some used white boards. My favorite of all the different interview approaches were the companies who asked me to build something. And of the companies who asked me to build something, my favorite was being asked to build Conway&#8217;s Game of Life (<a href="http://en.wikipedia.org/wiki/Conway's_Game_of_Life" target="_blank">wikipedia link</a>).

[<img class="aligncenter size-medium wp-image-226" alt="Conway_s_Life" src="/images/uploads/2014/06/Conway_s_Life-300x199.png" width="300" height="199" srcset="/images/uploads/2014/06/Conway_s_Life-300x199.png 300w, /images/uploads/2014/06/Conway_s_Life-1024x679.png 1024w, /images/uploads/2014/06/Conway_s_Life.png 1422w" sizes="(max-width: 300px) 100vw, 300px" />](/images/uploads/2014/06/Conway_s_Life.png)

Before I get into Conway&#8217;s Life (the main thrust of this blog post), let me briefly comment on why open-ended assignments are the way to go when it comes to job interviews:

  * Do you want to see how well someone can bullshit? Or do you want to see how well they can code?
  * Assigning a project measures not only technical prowess, but dedication and interest in the position
  * Instead of assessing how well someone will do on tasks tangentially related to the position, why not see how well they&#8217;ll actually do with something more real-world?
  * Best of all, it&#8217;s fun

Of the three interviews in which I was given projects, two were open-ended. Of the two, my favorite one was this: &#8220;Build an implementation of Conway&#8217;s Game of Life&#8221;.

In a nutshell, Conway&#8217;s Life is a programming exercise in which a grid of cells is implemented. Each cell can be either on or off. The number of adjacent cells which are &#8220;on&#8221; determines whether or not a given cell is toggled on during each &#8220;turn&#8221;. This simple logic can result in fairly complicated patterns emerging from an extremely simple starting point.

My implementation was written in Javascript and HTML.

## **Check it out by visiting <a href="http://www.dan24678/js/life-program/index.htm" target="_blank">this page</a> and clicking Play.**

Here are a few interesting points about my design:

  * I split the design into two modules. The <a href="https://github.com/DrLongGhost/life-program/blob/master/src/life.js" target="_blank"><strong>life</strong></a> class implements an internal state of the Life board and the <a href="https://github.com/DrLongGhost/life-program/blob/master/src/lifedom.js" target="_blank"><strong>lifedom</strong></a> class handles exposing this state via the DOM.
  * The animation in **lifedom** works by toggling the background of the table cells on and off. Originally, the animation was so fast that I actually needed to slow it down (via setTimeout) to create a more aesthetically appealing effect. I added a slider that lets you slow down or speed up the animation by changing the value of the timeout.
  * I added unit tests. They make sense for the **life** class, less so for **lifedom**, so I got lazy as I added various bells and whistles to the latter.
  * Because I originally wanted tests for **lifedom**, it lead me to a regrettable design decision: there is HTML in the javascript. I wanted to avoid duplication and only define the HTML of the game board once. Generally speaking, there are 3 ways to do this: 
      * Use PHP to define an HTML template to use in both the unit tests and the application. **Pro:** Easy. **Con:** Requires PHP.
      * Use Javascript for the template. **Pro:** Easy. **Con:** Ugly. Clunky.
      * Use Grunt to manage different builds for test versus production. **Pro:** Best pure javascript solution. **Con:** More complicated than the alternatives.

## Performance

For me, the most interesting part of this project came shortly after I finished my first implementation. I decided to check it out on my 1st generation iPad Mini. The animation looked horrible. The &#8220;frame rate&#8221; was way too low and it looked visibly underpowered and stuttered. I tried reducing the timeout to try and speed things up, but this didn&#8217;t help: entire frames would be dropped and it still looked like shit.

It&#8217;s funny because I knew immediately how this would go down:

  * I&#8217;d first trust my gut and try a quick fix
  * This wouldn&#8217;t work so I&#8217;d get more analytical and go through looking for additional optimizations
  * I would once again fail and resort to replacing the animation with canvas as a last ditch effort to get something that looked decent. I had no idea if this would work.

My first attempt at better performance was to replace **.toggleClass(&#8216;on off&#8217;)** with direct manipulation of the backgroundColor property. My rationale was that this would avoid CSS repaints and perform better. I also added some caching of which cells were &#8220;on&#8221;, which avoided a jQuery **find()** operation. You can see the github changeset <a href="https://github.com/DrLongGhost/life-program/commit/aa1fedf3892dfe4d9526d789b02162a0015c4aaf" target="_blank">here</a>.

I added some basic benchmarking to measure the impact (or lack thereof) of this change. I created a running counter of &#8220;turns per second&#8221; which turned out to be a great benchmark. The less the animation slowed down the program, the higher the turns per second would be.

To measure each change, I used the R-Pentomino pattern and grabbed my benchmark at turn 150, which represents a peak for the pattern in terms of the number of enabled cells in the pattern (and consequent steep drop in animation performance on the iPad Mini). By comparing the Baseline/Control stats against a Test Run that has all the animation suppressed (such that each turn is calculated in the **life** class but never displayed on-screen) you can see how much the animation itself actually slows things down.

## Baseline/Control Stats

  * Chrome on Macbook: 16tps
  * iOS Simulator on Macbook: 16tps
  * iPad Mini (1st generation): 5tps
  * iPhone 5S: 14tps

## Animation Disabled

  * Chrome on Macbook: 19tps
  * iOS Simulator on Macbook: 19tps
  * iPad Mini (1st generation): 17tps
  * iPhone 5S: 18tps

Next, we see the impact of my first attempt at improving the animation performance by replacing **toggleClass(&#8216;on off&#8217;)** with **backgroundColor**. It helped, but not enough.

## Classes Removed

  * Chrome on Macbook: 18tps
  * iOS Simulator on Macbook: 19tps
  * iPad Mini (1st generation): 7tps
  * iPhone 5S: 16tps

I tried some additional optimizations which didn&#8217;t really do much:

  * Instead of turning the entire board off and then turning cells on, figure out which ones should remain on and don&#8217;t turn them off in the first place: <a href="https://github.com/DrLongGhost/life-program/commit/b3b7c51c0bf1b4bdc4fc5154c915769ff85fe1f1" target="_blank">commit</a>.
  * Keep the internal array state of **life** sorted so we iterate less: <a href="https://github.com/DrLongGhost/life-program/commit/173017b777608c0be70b657e865c9e35c8e51c4b#diff-75e67dc61ab59dcd658eda3bf02a2023L154" target="_blank">commit</a>.
  * Replace **document.getElementById** with a cache so it only gets called the first time: <a href="https://github.com/DrLongGhost/life-program/commit/3cbb4626ef6382fe796b72df424feda311ffd5bc" target="_blank">commit</a>. This last change made a tiny difference, at least on the iPhone.

## Cached DOM Lookups

  * Chrome on Macbook: 18tps
  * iOS Simulator on Macbook: 19tps
  * iPad Mini (1st generation): 7tps
  * iPhone 5S: 17tps

At this point, I knew my only hope of getting acceptable performance would be to drop the idea of using a <table> altogether and swap out the table with a <canvas> when the user clicks Play and use that for the animation (<a href="https://github.com/DrLongGhost/life-program/commit/f24eb6da258e9dfba76af9155821636891704661" target="_blank">changeset here</a>). The results were pretty interesting and gratifying. Most importantly, it gets performance to an acceptable level on the iPad Mini.

## Canvas Animation

  * Chrome on Macbook: 18tps
  * iOS Simulator on Macbook: 19tps
  * iPad Mini (1st generation): 11tps
  * iPhone 5S: 16tps

By reducing the timeout value, I&#8217;m able to get the turns per second higher on an iPad without dropping frames. Note how it actually performs slightly worse on an iPhone 5S. Even on my iPad Mini, canvas actually starts out worse than the <table> solution (peaking at 17tps versus 18tps) before eventually overtaking it by turn 150.

If I were motivated to work on performance more, I might consider coding something that turns the timeout value down dynamically to respond to a low tps value. This would allow a consistent animation speed on both overpowered and underpowered devices &#8212; you would set the desired tps, rather than setting the timeout interval.

## What&#8217;s Next

I&#8217;m happy with the performance aspect of my program at this point. I might eventually add some drag and drop functionality so you can drag gliders, lines, pentominos, etc. onto the game board to more easily build complex patterns. I also toyed with the idea of adding different colors to the animation such that areas of the game board which are more busy would be colored darker than the parts which are burned out.

If you&#8217;re bored, feel free to fork my implementation, add something cool and send me a pull request.