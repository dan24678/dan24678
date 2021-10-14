---
title: Copy and Paste Buttons in Linux with my Intellimouse Optical
date: 2008-04-28T08:23:53+00:00
---
I cannot live without copy and paste buttons on my mouse.

When I&#8217;m working on Web Development, I find myself constantly copying and pasting text and being able to do it from my mouse is a convenience I have grown to depend on. Copy and Paste are also useful in general computing terms, when moving files around in a window manager. The mouse I use at home and at work is the Intellimouse Optical.

<img src="http://www.activewin.com/reviews/hardware/mice/optical/images/mouse1.jpg" alt="Intellimouse Optical" align="left" height="192" width="198" /> 

**Intellimouse Optical**:  
Featuring five buttons,  
including a mouse wheel button

I have had this mouse for about 5 years now and it is very solid and dependable. Microsoft may not even make it anymore, but I have one for home and one for work, and judging by the past 5 years, they aren&#8217;t going to break anytime soon.

I have the left-side button mapped to paste (which I can trigger with my thumb) and the mouse wheel button mapped to paste. Since it is all too easy to accidentally trigger the mouse wheel button, I opted to map it to paste, which is non-destructive. I keep the right-side button mapped to delete, which is also extremely useful.

In Windows, the driver CD that came with the mouse makes it a snap to bind copy, paste and delete to the mouse buttons. In Linux, the task is a little more difficult, but after several hours of mucking around, I have it down to a science. Here&#8217;s how I got it working, documented for posterity:

  1. Replace the code referencing your mouse in /etc/X11/xorg.conf with the following definition for the MS Intellimouse: 
    <pre><tt><tt>Section "InputDevice"
        Identifier "Configured Mouse"
        Driver "mouse"
        Option "CorePointer"
        Option "Device" "/dev/input/mice"
        Option "Protocol" "ExplorerPS/2"
        Option "Buttons" "7"
        Option "ZAxisMapping" "4 5"
        Option "ButtonMapping" "1 2 3 6 7"
        Option "Resolution" "100"
EndSection</tt></tt></pre>

  2. Install the program **xvkbd**, which is needed by xbindkeys
  3. Install the program xbindkeys and place the following in a file named .xbindkeysrc in your home directory: 
    <pre><tt><tt>
"xbindkeys_show"
control+shift + q
/usr/X11R6/bin/xvkbd -xsendevent -text "\[Control]c""
b:2
/usr/X11R6/bin/xvkbd -xsendevent -text "\[Control]v""
b:6
/usr/X11R6/bin/xvkbd -xsendevent -text "\[Delete]""
b:7</tt></tt></pre>

  4. Reboot your computer or restart X windows to load the new xorg.conf file. Then run xbindkeys in the background (xbindkeys &) and you should be all set!

Happy copying and pasting!