---
layout: post
title: "Planning Poker Made Easy"
modified:
categories: blog
excerpt:
tags: ['agile', 'socket-io', 'javascript', 'nodejs']
image:
  feature:
date: 2014-11-26T19:53:56-05:00
share: true
---

Often I work with remotely distributed teams where some (or all) of the team members are remote and attend meetings virtually. The other day, during a [planning games](http://en.wikipedia.org/wiki/Planning_poker) session, a few of the team members were remote and so, in order to facilitate the planning poker sessions, we looked for a web site that would provide the option for the team to all vote within a virtual room.
<!--more-->
We tried several but they all either had too many features, were overcomplicated or just did not work. We just wanted something simple that did the job without any fanfare or complication. So I created [planningpoker.biz](http://planningpoker.biz).

The requirements are simple:

1. Allow creation of a room
2. Allow others to join this room.
3. Room creator has the rights to:
    a. Reveal all votes
    b. Reset the votes.
4. Votes are captured in real time (no need for browser refresh).

To accomplish this I created a site with angularjs, socket.io and nodejs. Socket.io provides the real-time aspect.

To keep rooms separate and only broadcast signals to a given room I leverage the [rooms and namespaces](http://socket.io/docs/rooms-and-namespaces/) feature of socket.io. When a user joins a room we send a join:room signal broadcast to those attached to that room with an updated user list:

{% highlight javascript %}
socket.on('join:room', function (data) {
    socket.join(data.room_id);
    //...
    socket.broadcast.to(data.room_id).emit('join:room', users[data.room_id]);
}
{% endhighlight %}

[Socket.io](http://socket.io/) also handles votes and revealing/resetting of votes.

On the backend I am using nodejs. This is pretty simple and only really creates a new room id as well as handling the socket.io events. For now, I neglected to add in a persistence store but it would certainly make sense to in order to allow the site to scale.

You can check out the [site in action](http://planningpoker.biz) or you can view the source code on [github](https://github.com/thedarkinside/planningPoker).
