title: Useless Stuff I Still Know
author: Dan
date: 2019-12-12 09:41:52
tags:
---
I've been doing Web Development since about 2002. In many ways, things are not all that different today than they were 17 years ago. Many of the problems I work on in my current job are similar to those I worked on almost 2 decades ago when I first learned Javascript by making an insurance calculator for the financial services company I worked for at the time.

But, while many of the higher-level concepts and core technologies have been around for all this time, there are numerous quirks and behaviors associated with the earlier versions of Internet Explorer, Netscape Navigator, and the first versions of Firefox that simply have no relevance today.

Those latter pieces of useless, arcane lore are the purpose of this blog post. Here are some of the behaviors, code and bugs that I had to deal with when I was first getting started in this industry. Most of this info is so outdated that knowing this stuff is of no practical use today. But it's fun to reminisce.

## #12: `<marquee>` Tags

This one makes the top of the list, since it is arguably still somewhat relevant. The marquee tag lets you easily make a box of scrolling text:

<marquee>This just in!! The Americans have bombed Sesame Street. There are reports of massive muppet casualties. Stay tuned for more information at the top of the hour.</marquee>

The [CanIUse page](https://caniuse.com/#search=marquee) for this one has an interesting tidbit. While this tag is non-standard and deprecated, it has universal support in all current browsers -- as a result of Firefox finally adding support for this tag in January, 2019 after not supporting it for 13 years!

Oddly, I would bet that more people know about the infamous <blink>blink tag</blink> (which hasn't worked in a browser since 2013) than know about `<marquee>`, which still works to this day.

## #11: Frames

I'm sure that all web developers are familiar with frames. But I don't think they all appreciate the nuances of the history around their usage in web development.

First, it's important to understand the problem that they were introduced to solve.

Backend web-centric programming languages like PHP did not exist back in the day. There were certainly ways to use code to programmatically generate HTML -- mainly throught CGI code written in Perl or C, or Server-Side Includes in Apache -- but these were too complicated and difficult to use for simple tasks.

That's where frames came in. If you wanted to have a menu on your website and have the same menu appear on twenty different pages, you had two choices. You could copy and paste the menu 20 times; or you could separate the menu from the rest of your pages and put it in a frame.

I can also remember doing some projects in the editor Dreamweaver, which let you define reusable templates, such as a "menu". It would let you change the menu template and then automatically upload the modified versions of all 20 pages that included your inlined menu HTML.

These days frames are mostly known for their once obscure cousin, the `<iframe>`. It's funny to look back and remember a time when regular frames were widespread, and iframes almost unheard of. 

## #10: Image Maps

The `<map>` tag lets you take a single image and section if off into various links. So you can take a map of the United States in a single image and make each state clickable to take you to a different URL.

It looks like the `<map>` tag is still supported today, and not even deprecated. Their biggest use case today is probably in HTML emails, since email clients and rendering are somewhat frozen in time and lag a decade behind the rest of the Internet.

The interesting thing here is that there really isn't a better alternative to the `<map>` tag when you want to do this sort of thing quickly. So the tag didn't fade from the public eye because there was a better way to do it. It seems to have gone away because designers just stopped using clickable maps as a means of navigation. Honestly, it's weird to see how such a niche thing caught on and got so popular in the first place.

## #9: Netscape Layers

Okay! Now, we're getting into the good stuff.

Back in the early days of the Browser Wars when Netscape Navigator reigned supreme and Internet Explorer 3 and 4 were nipping at its heels, CSS was still in its infancy. You could do basic stuff, but there was no concept of z-index.

Netscape introduced their own method of positioning overlapping arbitrary elements called "layers". It was confusing and did not get much traction. It was pretty quickly abandoned in favor of z-index and the related positioning properties in CSS version 2.

But, if you search on the Web for "Netscape Layers" there is still plenty of information on them to be found. The brief [wikipedia layer_element entry](https://en.wikipedia.org/wiki/Layer_element) has some interesting tidbits, including how "document.layers" was an early browser-sniffing technique to find Navigator.

## #8: Select Boxes in IE6

Speaking of z-indexes, one of the most annoying gotchas with Internet Explorer 6 was that it did not respect z-index when it came to select boxes.

The developers who added select boxes to Internet Explorer (probably around version 3 or 4) were being lazy that day and just shoehorned in the regular select boxes that were used in Windows programs. Apparently they were called [Windows Shell ListBox and Combobox controls](https://blogs.msdn.microsoft.com/ie/2006/01/17/for-the-select-few/).

So when CSS 2 support got added in and z-index allowed positioned items to overlap and hide other elements with varying degrees of opacity, none of that worked with select boxes. They just always showed through anything you tried to position on top of them. So when you wanted to make a modal box that hid all the content underneath it, you would actually need to manually set "visibility: hidden" on every select box on the page so they could not be interacted with. And hopefully you would remember to restore them later when the user closed the modal.

This bug was eventually fixed in IE 7. However, prior to that, there was one other approach to solving this problem...

## #7: showModalDialog in IE6

IE 6 actually had its own dedicated, proprietary method for showing a modal dialog on a page, and it was awesome.

Creating a modal dialog in Firefox was always a bit of a pain. The [submodal library](https://code.google.com/archive/p/submodal/) was the goto solution, but the general technique was to overlay a transparent "mask" div on the page to block the user from interacting with anything on the page, and then position a smaller, centered window with your modal content.

But, if you were working on some corporate Intranet application and didn't need to worry about supporting non-IE browsers, all you needed was a single method: "[showModalDialog](https://docs.microsoft.com/en-us/previous-versions/ms536759%28v%3Dvs.85%29)".

It would let you provide a URL to an HTML file that was used to populate the modal and would just work. It showed a transparent overlay over the rest of the page that even supported covering select boxes. There was even a sister method "showModelessDialog" that would remove the toolbar and scrollbar.

## #6: VBScript

IE 6 actually supported two types of scripting languages in `<script>` tags: JScript and [VBScript](https://en.wikipedia.org/wiki/VBScript). JScript was, of course, identical to Javascript but Microsoft couldn't actually call it that because of trademark issues. VBScript, however, was a completely proprietary language that only worked in Windows and Internet Explorer.
  
I think the idea was that VBScript would provide a unified scripting experience so that Windows programmers could learn only one scripting language and have it work in Windows applications, Excel macros, and in Web applications. I saw it in the wild once or twice, but for the most part, if you were working on a Web page in the early 2000's, you were using "JScript". Every once in awhile though, you'd look in the source of a webpage and see `<SCRIPT TYPE='Text/VBScript'>` where some insane person was using it to do roll-overs or a hover menu.

## #5: DOM in old IE

IE 6, 7 and 8 would store an incredibly frustrating version of the DOM in memory.

If you gave it this XHTML:

`<h1 class="header">Header Here</h1>`

and then queried with some Javascript to read that element back out of the DOM, this is what you would get:

`<H1 class=header>Header Here</H1>`

Uppercase element names and no quotes around attribute values...

This made regular expressions far more complicated than they needed to be and generally just felt ugly and depraved. Every time you would use Internet Explorer to return its version of your carefully crafted, validated, strict XHMTL 1.0 code, you suddenly felt like you needed a shower.

## #4: Firefox eats its own cursor

Because I've been picking on IE so much, it's only fair that I include at least one Firefox bug. This one was my favorite.

I couldn't find the exact bug report to corroborate my memory, but this is how I remember it.

The cursor, also called a "caret", in Firefox version 1 (or maybe it was 3) would get eaten and you were unable to get it back.

Specifically, if an input had focus and the cursor was visible in it and you set "display: none" on the input, both the cursor and the input would disappear. If you clicked in another input box, you could type but your cursor was permanently invisible. There was no way to get it back without reloading the page.

The fix was to be sure you always called the blur() method on an input before you hid it. Suuuuper annoying.


## #3: "Operation Aborted"

Our final few tidbits are more of Internet Explorer's lovely foibles.

"Operation Aborted" was one of my least favorite IE bugs. There is a pretty good write-up about this bug [here](https://weblogs.asp.net/infinitiesloop/Dealing-with-IE-_2600_quot_3B00_Operation-Aborted_2600_quot_3B002E00_-Or_2C00_-how-to-Crash-IE).

The TLDR is that IE 6 & 7 would completely shit the bed and render nothing at all if you tried to append a node to the body of your document. It would show an empty white page with an error alert: "Internet explorer cannot open the internet site {http://site.com}. Operation Aborted."  That's pretty bad, right?

Well... technically, you *could* append to the body as long as you did it from a script tag that was not a direct child of the body (such as a grandchild or a script in the head). And you could always safely append to nodes other than the body.

If you remembered the specifics of this weird bug, you were fine. But it was obscure enough that you would often forget about it, despite the catastrophic consequences of doing so.

## #2: hasLayout

Internet Explorer 7 and earlier had a proprietary property called "hasLayout". As the name implies, the property indicates whether an element is able to be rendered on its own, or if it needs to inherit styles from a parent element in order to be rendered.

The property itself is a read-only property, but its importance belied that fact. If you were trying to structure floats on a page and the page itself exhibited strange behavior and generally did not act as it was supposed to, a good first step was looking at the affected elements' hasLayout values. When you found an element that contained floats but "did not have a layout" then you had found your cluprit.

Since you could not simply define "hasLayout: true", you needed to employ one of several tactics to modify the element(s) to gain layout. You could set width, height, zoom or several other properties. As soon as you did so, like magic, your layout suddenly began working again.

A decent write-up on hasLayout can be found [here](https://www.sitepoint.com/internet-explorer-haslayout-property/).

## #1: Debugging in IE

Nothing sucked in early versions of Internet Explorer more than the (lack of) debugging tools.

Not to sound like the "I walked 80 miles uphill both ways to school" guy, but Web developers today really cannot appreciate the struggle.

It wasn't that there was no Web inspector (although there wasn't).

It wasn't that there was no debugger (although there wasn't).

It wasn't that there was no console (although there wasn't).

The problem was that when IE hit a Javascript error (because, say, you accidentally included a trailing comma in a JSON structure) all you were given in terms of an error message was some bullshit like this:

`
Line: 5
Char: 33
Error: Unknown Runtime Error
`

There were like 10 different errors that IE might give you and they all meant nothing. So you were left with a goddamn line number and character number to figure out what went wrong.

In the nightmare scenario, you had code that you'd written on a Mac or Linux box that had non-Windows carriage returns in it so everything displayed in Notepad on one line with those stupid little squares where there were supposed to be line breaks. Once you had found the right line somehow, you would click the right arrow key counting as you went: one, two, three, four, five, six, seven. All the way up to thirty-three.

And then you stared at the code for several minutes trying to figure out what it was about that particular character that triggered the error. But, of course, sometimes the actual problem was earlier in the code, so once you realized THAT, you stared at the code for several more minutes, questioning your career choices. What would you be doing right now if you had listened to Greg and gotten into automative HVAC instead?