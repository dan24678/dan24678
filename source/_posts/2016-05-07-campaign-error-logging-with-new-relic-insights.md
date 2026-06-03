---
title: Campaign Error Logging with New Relic Insights
date: 2016-05-07T04:27:45+00:00
---
Several weeks ago, a project which I&#8217;d worked on for the better part of two months finally went live across all LeadiD customer campaigns. In many ways, I am prouder of this project than I am of anything else I&#8217;ve done in the ten years or so that I&#8217;ve been doing web development &#8212; in large part because the finished product is just so damn cool.

Because I can&#8217;t help myself, I&#8217;m going to jump right to the pretty pictures. By leveraging the [Insights reporting backend](http://newrelic.com/insights) provided by New Relic, LeadiD now has access to a full dashboard that tracks all the errors and debug data associated with our customer&#8217;s campaign scripts:

[<img class="aligncenter size-large wp-image-304" alt="Insights__Campaign_JS" src="/dan24678/images/uploads/2016/05/Insights__Campaign_JS-1024x537.png" width="640" height="335"  />](/dan24678/images/uploads/2016/05/Insights__Campaign_JS.png)

## Why is this Cool?

Thousands of businesses use New Relic or their competitors to monitor their web sites and track errors. Why is what LeadiD did so interesting?

For the answer to this, you need to look at what it is we are actually monitoring. In order for LeadiD&#8217;s core services to operate, our customers must include the LeadiD campaign javascript on their site. Simply by including this script on the footer of their site, our customers gain unique insight and visibility into the actions and motivations of their site visitors. First and foremost, our javascript was written with two main areas of focus: performance and stability. Nonetheless, millions of individual consumers execute our javascript each day and some of those visits result in javascript errors. It is these errors which we are now monitoring.

Specifically, whenever an individual consumer triggers an error in our campaign javascript on any of the 20,000+ web domains who have partnered with LeadiD, we get a record of that error in our New Relic Insights dashboard, where the data is tracked and graphed so we can immediately see the impact as we push out new features or fix pre-existing bugs.

## How Does it Work?

[<img class="alignleft size-medium wp-image-318" alt="campaign_js_blog" src="/dan24678/images/uploads/2016/05/campaign_js_blog-300x274.png" width="300" height="274"  />](/dan24678/images/uploads/2016/05/campaign_js_blog.png)As shown in the diagram, a customer site AwesomeWalruses.com has implemented the LeadiD campaign javascript. Every time a visitor encounters an error in our script (or if debug logging, which I&#8217;ll discuss later, is enabled) a record is sent back to a logging microservice running on our domain. Typically, this request is sent as an asynchronous XmlHttpRequest from the client browser and includes all the available details about the error. From there, the microservice pushes the data into New Relic Insights.

One key item to note is that regular javascript errors on AwesomeWalruses.com are not reported back to our logging system. It is only errors in <span style="text-decoration: underline;"><strong>our</strong></span> campaign javascript which are being tracked.

To get this level of granularity, much of our campaign javascript is wrapped in try/catch blocks, instead of using the alternative method of binding to the global window.onerror handler. While the latter solution can likely provide a more sure method of capturing all errors, it is more risky and complicated when you consider the need to filter out errors from the parent site. Try/catch blocks, however, carry with them their own [performance considerations](http://ryanmorr.com/reinventing-the-try-catch-block/).

One crucial feature which our campaign javascript now supports is a verbose debug logging setting. If this is enabled for a specific customer campaign, LeadiD receives javascript performance data from all visitors to a site, even if an error is never triggered. Because this is such a huge amount of data, the messages need to be batched together when they are sent to us and then unbatched in the microservice so each item is saved to New Relic separately. It&#8217;s interesting to note that enabling debug logging <span style="text-decoration: underline;"><strong>for a single customer</strong></span> triples our daily Insights data usage with New Relic (visible in the &#8220;Volume of Log Requests&#8221; graph above).

## Preliminary Project Design and Initial Prototype

When the project was first conceived, the specific backend where the logging data would be stored was an open question.

LeadiD was already using New Relic for server monitoring and [RayGun](https://raygun.com/) for application error logging. As a result, either service had the potential to be a good fit for this project. Additionally, I had previous experience with the open source project [Sentry](https://getsentry.com/welcome/) and, as a free software alternative to New Relic and RayGun, there were definite cost advantages to going with Sentry. As an additional free software alternative, we considered the [ELK stack](https://www.elastic.co/products) (ELK stands for Elastic Search, Logstash and Kibana).

With assistance from our first-rate Dev Ops team, I prototyped a backend that injected data into all the services except Elk, which we never tested due to the effort. (The prototype microservice was thankfully thrown away and reimplemented by Senior Engineer Ian Carpenter, which is what you hope for when you build a prototype). Next, we drew up a comparison table showing each choice&#8217;s strengths:

<p style="text-align: center;">
  <a href="/images/uploads/2016/05/Campaign_JS_Logging_Backend_-_Engineering_-_LeadiD_Confluence.png"><img class="aligncenter size-medium wp-image-308" alt="Campaign_JS_Logging_Backend_-_Engineering_-_LeadiD_Confluence" src="/dan24678/images/uploads/2016/05/Campaign_JS_Logging_Backend_-_Engineering_-_LeadiD_Confluence-300x232.png" width="300" height="232"   /></a>
</p>

To cut the chase, New Relic Insights was very compelling for 2 reasons:

  * The ability to run SQL-like queries against the data and graph the results with very little work
  * As a SaaS-based solution, we save large amounts of time versus writing and maintaining our own solution

One of the interesting realizations we had when comparing the chart above with our &#8220;technical requirements&#8221; was that error data and logging data are fundamentally two different things. Three of the four alternatives are more directly geared toward error data than storing normal application logging data, but New Relic Insights (which is essentially a NoSQL database masquerading as a relational one) proved itself able to be molded to fit both error messages **and** logging data. A few of the negatives to New Relic noted in the table ended up not mattering after all:

  * The 256-character limit on tag length is mitigated by simply spreading your data across multiple fields
  * While we relinquish some control by having our error and logging data stored in New Relic&#8217;s system, the [Insights API](https://docs.newrelic.com/docs/insights/new-relic-insights/adding-querying-data/querying-your-data-remotely) allows integrations with this data. (I even wrote a script called [insights2csv](https://github.com/dan24678/insights2csv) to easily pull down our data so we can import it into PostgreSQL and elsewhere)

## The Results

LeadiD now has a system that allows us to monitor in real-time the impact of our client-side releases. This enables us to monitor new versions of our javascript as it goes live across all our customer sites and compare the volume of errors thrown by the new version against the historical norm from prior versions. If we begin seeing more errors than expected for the new version, we can immediately roll back the deployment and begin digging into the logging data itself to figure out what went wrong.

To this end, we can easily pull the data down from New Relic Insights to feed it into other databases to enable more advanced queries. This also facilitates resource sharing with our Data Science department, who are now able to use the data on which consumers encountered errors to exclude that problematic data from the datasets they use to train machine learning and develop scoring models.

Several specific examples of how LeadiD Engineering are using the Insights data are visible in the dashboard graphs above. We are now able to identify specific customer sites which are sending us high volumes of error data. It is far better for a company to proactively identify issues and reach out to its customers than it is for the company to fix the problem only after customers have noticed and complained. LeadiD is now in a position to notice any such errors before our customers do and before the errors have a chance to have a major negative impact on our products.

By partnering with client sites to run trials of the verbose debug logging, we have already identified several potential improvements to our libraries. Being able to follow the breadcrumbs left by the debug logging is an essential tool in keeping our scripts up-to-date with the latest browser APIs and in ensuring smooth co-existence with the various javascript frameworks in use today. Without this logging ability, it is an order of magnitude more difficult to even recognize that something is wrong, let alone figure out where in the code the problem may reside.

All in all, the finished Campaign JS Logging system has met or exceeded all the project goals. I had a lot of fun developing the finished solution and it continues to provide value to LeadiD and our customers on a daily basis.