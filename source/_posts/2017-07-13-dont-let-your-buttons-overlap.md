---
title: 'Dont Let Your Buttons Overlap'
date: 2017-07-13T12:13:29+00:00
---
While testing some changes I made to a site for work, I caught myself making a somewhat common UI/UX mistake.

Here were the original screens. They show **Step 1** and **Step 2** of a &#8220;Record&#8221; functionality:

<p style="text-align: center;">
  <strong>Step 1</strong>
</p>

[<img class="aligncenter size-medium wp-image-336" alt="VoiceVibes 2" src="/dan24678/images/uploads/2017/07/VoiceVibes-2-300x152.png" width="300" height="152" />](/dan24678/images/uploads/2017/07/VoiceVibes-2.png)

&nbsp;

<p style="text-align: center;">
  <strong>Step 2</strong>
</p>

[<img class="aligncenter size-medium wp-image-337" alt="VoiceVibes 3" src="/dan24678/images/uploads/2017/07/VoiceVibes-3-300x169.png" width="300" height="169"  />](/dan24678/images/uploads/2017/07/VoiceVibes-3.png)

&nbsp;

Can you spot the problem?

The issue, which is admittedly minor, comes when users double-click the Stop button in Step 1. The first click advances the screen to Step 2 and the second click triggers the Cancel button. Most users know to only use a single click on buttons, but the capacity for users to misuse your UI can never be discounted.

One solution to the problem is to ensure there is an &#8220;Are you sure?&#8221; confirmation on the Cancel button. I already had that in place but didn&#8217;t consider it sufficient.

Another solution is to use Javascript to detect and suppress the second &#8220;half&#8221; of the double-click. But&#8230; ain&#8217;t nobody got time for that.

In the end, I tweaked the layout on the **Step 1** design to push the button further down so it no longer overlaps the other button in terms of screen position:

[<img class="aligncenter size-medium wp-image-338" alt="VoiceVibes" src="/dan24678/images/uploads/2017/07/VoiceVibes-300x193.png" width="300" height="193"  />](/dan24678/images/uploads/2017/07/VoiceVibes.png)