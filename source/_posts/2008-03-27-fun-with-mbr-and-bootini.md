---
title: Fun with MBR and Boot.ini
date: 2008-03-27T21:09:16+00:00
---
I had a bit of a scare these past 24 hours when I thought that I had fucked up my newly installed and configured Windows XP PC, which for the purposes of this blog I will refer to as Lester. Lester has a 500gig SATA harddrive, as well as an older 250 gig drive, which was a holdover from my prior computer. As a result, the smaller, secondary harddrive had 3 partitions on it: an XP system partition, an Ubuntu partition and an NTFS partition with a buttload of mp3s on it.

Last night, I decided to delete the 3 separate partitions on my secondary drive and reformat the whole thing as a single NTFS partition for mp3s. I used Partition Magic and some other partition program I had snagged off BitTorrent. I was very cautious while formatting and succeeded in deleting the partitions and wiping the drive. However, when I rebooted, Lester would not start up and displayed an ominous message about no bootable media being found.

This was pretty damn confusing, since I knew that I had formatted the correct drive and was pretty sure that all my windows files were still present on the main drive. What the fuck had happened? Was it a virus?

Well, to make a long story short, I eventually was able to restore the MBR (Master Boot Record) and boot.ini on the main drive and get it to boot into Windows again (although I ended up installing Windows on the mp3 drive just so I&#8217;d have something to boot into so I could muck around with the main drive). After I had fixed Lester, I realized what had happened. When I had originally installed XP, for some bizarre reason, the boot.ini file for XP was installed, not on the hard drive containing my Windows system, but on the hard drive containing my mp3s and the other 2 unused partitions. Thus, when I wiped this drive, it blew out my boot.ini file and Lester was dead in the water.

Even odder, when I installed the temporary version of XP on the newly wiped mp3 drive to give myself access to the files on the main drive, it put the new boot.ini file on the main drive! So, at that point, boot.ini was where I wanted it and I only had to go in and edit it to tell it to load the windows install on drive 0 instead of drive 1.

In case anyone ever has a similar issue, here are the instructions I finally found on editing the MBR and boot.ini:

  1. You can try restoring the MBR in MSDOS with either the **FIXMBR** command or with **fdisk \mbr**. In my case, this didn&#8217;twork, since I was still missing boot.ini.
  2. I was able to edit the new boot.ini file with the following commands, run from the commandline (I don&#8217;t think boot.ini is visible within Windows Explorer, even with Show Hidden Files enabled): 
      1. attrib -H -S -A -R boot.ini &#8212; This sets the boot.ini file as editable
      2. notepad boot.ini &#8212; Pops it open in Notepad so you can edit it.
      3. attrib +H +S +A +R boot.ini &#8212; Restores boot.ini as a hidden, system file, after you have made your edits and saved.
  3. Reboot and all is well. You can add multiple entries in boot.ini under the [operating systems] section. For me, the operative thing to change was the **rdisk** option. You&#8217;d think the **disk** option would control which harddrive is being described, but actually the physical drive is referenced by the rdisk option. Thus, I had to change:  
     **multi(0)disk(0)rdisk(1)partition(1)\Windows**  
    to  
     **multi(0)disk(0)rdisk(0)partition(1)\Windows**

I must confess that despite the stress of thinking I had lost all my files, it was pretty damn gratifying to actually fix this myself by getting down and dirty with the MBR. Nevermind that I wouldn&#8217;t have ever had this problem if I hadn&#8217;t fucked up in the first place&#8230;