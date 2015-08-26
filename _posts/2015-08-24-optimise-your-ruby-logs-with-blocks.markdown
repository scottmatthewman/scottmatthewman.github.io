---
layout: post
title: "Optimise your Ruby logs with blocks"
date: 2015-08-24T13:36:09+01:00
---

When adding logger statements to Ruby or Rails apps, we're used to providing interpolated strings to whichever method matches the status level of our message:

{% highlight ruby %}
logger.info "Number of items matching our requirements: #{items.count(&:valid?)}"
{% endhighlight %}

Whether or not this string gets output to the log is dependent on the logger instance's `#level` property, which can vary (say, between development and production environments).

However, even if the `logger.level` determines that the text should not be output to the log, any ruby code called by the interpolated string will still be executed.

As an alternative, each of the `Logger` class's logging methods can take a block that returns a string. This block will only be called, and any Ruby code within will only be executed, if the severity is of a sufficient level to be output to the log:

{% highlight ruby %}
logger.level = Logger::WARN

# String parameter - Calculated, but not displayed
logger.debug "Retrieved items: #{items.inspect}"

# Block - Skipped completely
logger.debug { "Retrieved items: #{items.inspect}" }
{% endhighlight %}

So if your debugging messages are expensive in terms of processing, leaving them _in situ_ in production code won't affect your application's overall performance as long as you're mindful of your server's logging level.

**UPDATE:** As [Reddit user **tomthecool** notes][reddit-comment], there is little to no performance benefit (and may be some performance loss) if your log messages are just plain strings which you then wrap up in blocks, or where there is very little processing involved. The example code above that uses `#inspect`, for instance, would be unlikely to benefit to any substantial degree – it serves to demonstrate the syntax difference more than anything.

But I'd also suggest that logging at the debug level is where you're more likely to want to do some more detailed analysis of the data related to the problem you're trying to get to the bottom of. And in those cases, protecting your production code from expensive calls that have no effect on your log output could be of benefit.

---

_This blog post came out of [an entry][til-log] in the [Altmetric TIL (Today I Learned) library][til], an open source collection of knowledge that Altmetric developers have stumbled across in our working days. Feel free to have a look around!_


[til]: https://github.com/altmetric/til
[til-log]: https://github.com/altmetric/til/blob/master/ruby/logger_blocks.md
[reddit-comment]: https://www.reddit.com/r/ruby/comments/3iddvd/using_blocks_in_your_logger_statements_to_prevent/cug2wf0
