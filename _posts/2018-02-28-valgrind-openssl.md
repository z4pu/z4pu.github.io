---
layout: post
title: Getting Valgrind and OpenSSL to play nice
date: 2018-02-28 16:00:00
description: Getting Valgrind and OpenSSL to play nice
categories: posts
hidden: false
comments: false
published: true
future: true
---

### When you really don't want to look at All The Errors

My life in and outside of work has been taken over by the OSCP. It's been great fun, and as someone who has never really been a gamer - HERESY!!! - I'm finally getting a sense of how addictive they can be. 10 out of 10, would recommend it. Being a great big infosec noob, I opted to take the three months of lab time, and have pushed my examination date out as far as humanly possible. Naturally, I'm getting extra lab time. I spent the first month working through the course materials, and am slowly developing a workflow for attacking the machines. 

I haven't succeeded with any of the reputedly fiendishly difficult ones, but we'll get there. 

A few days ago I took a long overdue (and for my sanity, much-needed) diversion from the OSCP to work on one of my to-do items for My Actual Job involving the OpenSSL API. My binary was doing what it was supposed to do. Yay. 

And because it's supposed to be A Good Thing, I ran my binary through Valgrind. 

Valgrind hated it so much that it crashed.

Apparently it's an issue with OpenSSL and my system installation of Valgrind 3.11.0.

I compiled the latest stable version of Valgrind (3.13 for now) and it worked. 

But the errors. 

Oh the errors. 

See this post for why: 

<http://www.hardening-consulting.com/en/posts/20140512openssl-and-valgrind.html>. 

Essentially, OpenSSL relies on uninitialised memory to generate enough entropy. 

You can go with what the author of the post suggested, or you can do what I do - a very blunt tool, to be fair, but then in my own code I'm fanatical about initialising ALL THE THINGS. 

{% highlight ruby %}

/opt/valgrind/3.13.0/bin/valgrind -v --log-file=../logs/valg.log --undef-value-errors=no --leak-check=full ./program_name

{% endhighlight %}

This reduces the errors to a far more manageable level. So you can focus on other things, like pwning machines in the Offensive Security lab... 



