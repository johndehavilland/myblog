---
title: Using AMQP with Azure Service Bus and Python
layout: post
modified: 
categories: blog
excerpt: 
tags:
- Azure
- ServiceBus
- Python
- AMQP
date: '2017-07-05'
image:
  feature: 
share: true,
comments: true
---

Azure Service Bus does support the Advanced Message Queuing Protocol (AMQP) 1.0 protocol. It is a comprehensive messaging protocol and is typically used where reliability and interoperability are key. It provides a wide range of messaging features such as reliable queuing, publish-subscribe, transactions etc.

Since Azure Service Bus supports this, I was looking for a way to leverage it within my Python code. It was a little trickier than I first expected as there are differences between Python v2.7 and Python v3.0

For this to work with Python you have to use the [Apache Qpid Proton library](https://qpid.apache.org/proton/index.html). 

`pip install python-qpid-proton`

When connecting to Azure Service Bus to use AMQP you will need to create a connection string that looks like:

`amqps://keyname:key@servicebusname.servicebus.windows.net/queuename`

The key parts here are the keyname and key. You can get these from your Azure Service Bus. By default there will be a root shared access key.

So an example string will look like:

`amqps://RootManageSharedKey:bWFd1mmnwMQ9yau2...[redacted]...EohtswwzwInt6eY=@<servicebusname>.servicebus.windows.net/queuename`

The code snippet for python v2.7 is pretty simple

{% highlight python %}
from proton import Messenger, Message
import urllib.parse

messenger = Messenger()
message = Message()
key = "bWFd1mmnwMQ9yau2...[redacted]...EohtswwzwInt6eY="
enc = urllib.parse.quote_plus(key)
message.address = "amqps://RootManageSharedKey:"+enc+"@<servicebusname>.servicebus.windows.net/quicktest"

message.body = u"This is a text string"
messenger.put(message)
messenger.send()
{% endhighlight %}

Now you may run into an issue with the authentication mechanism and so, if you do, the following should work. The *allowed_mechs* property allows you to alter the default mechanism. This works for Python 3 and also for Python 2.

{% highlight python %}
from proton import Message
from proton.utils import BlockingConnection
from proton.handlers import IncomingMessageHandler
from urllib import quote_plus

key = "bWFd1mmnwMQ9yau2...[redacted]...EohtswwzwInt6eY="
server = 'amqps://RootManageSharedAccessKey:' + quote_plus(key, safe='') + '@jdhservicebus.servicebus.windows.net/'
queue = '<queuename>'
conn = BlockingConnection(server, allowed_mechs="PLAIN")
sender = conn.create_sender(queue)
sender.send(Message(body="Hello World!"))
conn.close()
{% endhighlight %}