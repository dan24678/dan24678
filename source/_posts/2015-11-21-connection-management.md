---
title: Connection Management
date: 2015-11-21T13:02:35+00:00
---
In my experience, an often-overlooked principle of endpoint design is the idea of minimizing connection use.  Any time you connect to an external resource, you should try to avoid connecting to it as much as possible and, if you really do need to connect to it, connect for as short a period of time as possible. And when you do connect, don&#8217;t have anything else connected at the same time.

## <span style="font-size: large;">Avoid Connecting to External Resources</span>

If you have a system that rejects requests because they&#8217;re invalid, request validation should happen before you connect to the DB so that invalid requests never even trigger a DB connection.

Similarly, don&#8217;t open a DB connection on ALL requests just because some percentage of requests need to execute a particular operation. Wait until you actually need to perform the operation to open the connection.

For other use cases, can you cache something locally and periodically invalidate the cache to avoid touching the DB on every request? In other words, does your application really need real-time data or is some level of delay acceptable so you can implement caching?

## <span style="font-size: large;">Connect as Briefly as Possible</span>

An anti-pattern in PHP is to open a DB connection immediately then let PHP close it for you when the page load is finished. If you do this, you are keeping the DB connection open for longer than you are actually using it, meaning you may need more total connections to support your app.

A best practice is to wait until you need a connection, then open it, run your queries and immediately close the connection before proceeding to actually use the data you fetched.

Another, more debatable piece of advice is to do data transformations and joins in your application code instead of in your DB queries. Again, this will make things easier on your database by distributing work to your (cheaper) application servers. But the cost in complexity and some level of waste may make this a non-starter.

## <span style="font-size: large;">Don&#8217;t Overlap TCP Connections</span>

Let&#8217;s say you have 1000 available MySQL connections and 50 available memcached connections and you keep them both open at the same time. You now have a situation where a slow-running MySQL query results in memcached connections being held open and your system goes down once your 50 memcached connections are swamped (even though you potentially had sufficient MySQL connections to actually handle the slow query, had the connections not overlapped).

This also holds true for other types of connections, such as Curl requests to external APIs or messaging systems like RabbitMQ or Kafka. I once saw slow-running requests to Facebook&#8217;s API cause my company&#8217;s memcached connection pool to be exhausted because the two were allowed to overlap.

## <span style="font-size: large;">Final Thoughts</span>

Connection pooling solutions can help reduce the latency of opening new connections but this doesn&#8217;t alter the inherent danger of depleting the maximum number of allowable connections within the pool, so much of the above still applies.

One thing that may turn the advice above on its head, however, is the move to multi-threading, asynchronous requests and the concepts of Actors and Futures in newer languages like Scala.  In these environments, I can envision scenarios where you may want to preemptively fetch some data on a separate thread before you&#8217;re sure you need it because the cost in connection usage and the waste of sometimes getting data you don&#8217;t need is a fair price to pay for a modest decrease in average response time.

The ability to do two things at once turns all the advice above on its head.  <span style="line-height: 1.5em;">The times they are a&#8217;changing.</span>

&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;-

Comment on reddit [here](https://www.reddit.com/r/programming/comments/3tpsuy/connection_management/).