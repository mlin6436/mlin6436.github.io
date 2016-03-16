---
layout: post
title: "A Twitter Integration using MQ"
tags: ["integration", "java", "activemq", "mq", "message queue", "websocket", "poll", "network", "twitter", "mac", "hbc", "apps"]
---

<div class="message">
I've been working on a hack to get twitter feed integrated into our application. With <a href="http://stackoverflow.com/questions/2868627/why-should-a-web-architecture-be-loosely-coupled" target="_self">loose coupling</a> is good for almost all web-based solutions in mind, a separate container that is dedicated to store, manage and process the feeds would be a nice thing to have. In which, I set out to implement this simple message queue service using ActiveMQ and WebSocket.
</div>

# TL;DR

If you just want to see a working example, or make sure you get the right dependencies: boom, [here](https://github.com/mlin6436/twitter-integration) you go!

# What is Message Queue?

You probably can find a dozen [explanations](http://en.wikipedia.org/wiki/Message_queue), but what it really means to me is that, Message Queue is a storage area of a mechanism, which allows distributed applications to communicate asynchronously by sending messages between the applications.

# Why Message Queue?

This [article](http://blog.iron.io/2012/12/top-10-uses-for-message-queue.html?spref=tw) gives you a lot of reasons why Message Queue could be so beneficial to large scale systems integration. Speaking from my experience working in various integration projects, Message Queue is the pursue for optimal performance, scalability and resilience, also low level of [coupling](http://martinfowler.com/ieeeSoftware/coupling.pdf), while accomplishing asynchronous communication, buffering and filtering at the same time.

# What choices do I have?

There are several renowned Message Queue brokers available, such as [ActiveMQ](http://activemq.apache.org/), [RabbitMQ](http://www.rabbitmq.com/) and [ZeroMQ](http://zeromq.org/). They all have their [merits](http://stackoverflow.com/questions/731233/activemq-or-rabbitmq-or-zeromq-or), so it is really up to you to make the best choice base on your circumstances. And just in case you are interested, I personally like RabbitMQ a lot, but since ActiveMQ comes for free in some ESB containers, why bother reinventing the wheel, right :)

{% include image.html url="/media/2014-07-02-a-twitter-integration-using-mq/stay-mediocre.gif" width="100%" description="Stay mediocre" %}

# An example would be nice...

The example I put together is a middle layer between twitter API and our applications, using ActiveMQ and WebSocket to implement the messaging service.

Its purpose is to make sure we are able to deliver tweets across different applications in a consistent and timely fashion. While to be precise, it is essentially to ensure we have a mechanism to filter out some pottery mouthes, control and monitor traffics to our applications.

## 1. Install ActiveMQ

If you are on a Mac, use [brew](http://brew.sh/). Otherwise, you should always be able to find somehting that fits you [here](http://activemq.apache.org/getting-started.html).

{% highlight bash %}
$ brew install activemq
$ activemq start
{% endhighlight %}

To verify the service is up and running:

{% highlight bash %}
$ netstat -an | grep 61616
{% endhighlight %}

or browse to [http://localhost:8161](http://localhost:8161). As basic usage, we are only interested in 'Queues', where the number of consumers, message queued and dequeued are displayed in the dashboard.

## 2. Java 8

If you can't be asked to pre-installed Java in Mac, you can always [download](http://www.oracle.com/technetwork/java/javase/downloads/index.html?ssSourceSiteId=otnjp) and install the latest and greatest, even though it is slightly trickier to set the configure the Mac, that doesn't mean it is not doable.

First, you need to locate where you JDKs are:

{% highlight bash %}
$ cd /System/Library/Frameworks/JavaVM.framework/Versions/
{% endhighlight %}

Then remove 'CurrentJDK' and symlink it to the version you just installed:

{% highlight bash %}
$ rm CurrentJDK
$ ln -s cd /Library/Java/JavaVirtualMachines/<jdk_version>/Contents/ CurrentJDK
{% endhighlight %}

## 3. Producer and Consumer

The whole message queueing fits in [producer-consumer](http://java.dzone.com/articles/producer-consumer-pattern) pattern perfectly. On one hand, the producer will stock the new tweets into the queue. On the other, the consumer will fetch the messages from the queue and process them.

Here is a producer implementation in the simplest form:

{% highlight java %}
import javax.jms.*;
import org.apache.activemq.ActiveMQConnection;
import org.apache.activemq.ActiveMQConnectionFactory;

public class Producer {
    private static String url = ActiveMQConnection.DEFAULT_BROKER_URL; // Configure to use localhost
    private static String subject = "TWEETQUEUE"; // This is the name of the queue you will see in ActiveMQ dashboard

    public static void main(String[] args) throws JMSException {
        ConnectionFactory connectionFactory =
            new ActiveMQConnectionFactory(url);
        Connection connection = connectionFactory.createConnection();
        connection.start();

        Session session = connection.createSession(false,
            Session.AUTO_ACKNOWLEDGE);
        Destination destination = session.createQueue(subject);
        MessageProducer producer = session.createProducer(destination);
        TextMessage message = session.createTextMessage("A new message");
        producer.send(message);

        connection.close();
    }
}
{% endhighlight %}

And this is what a consumer looks like:

{% highlight java %}
import javax.jms.*;
import org.apache.activemq.ActiveMQConnection;
import org.apache.activemq.ActiveMQConnectionFactory;

public class Consumer {
    private static String url = ActiveMQConnection.DEFAULT_BROKER_URL;
    private static String subject = "TWEETQUEUE";

    public static void main(String[] args) throws JMSException {
        ConnectionFactory connectionFactory
            = new ActiveMQConnectionFactory(url);
        Connection connection = connectionFactory.createConnection();
        connection.start();

        Session session = connection.createSession(false,
            Session.AUTO_ACKNOWLEDGE);
        Destination destination = session.createQueue(subject);
        MessageConsumer consumer = session.createConsumer(destination);
        Message message = consumer.receive();

        if (message instanceof TextMessage) {
            TextMessage textMessage = (TextMessage) message;
            System.out.println("Message: "
                + textMessage.getText());
        }

        connection.close();
    }
}
{% endhighlight %}

## 4. Twitter integration

To make devs' life easier, Twitter provides a nice HTTP client, called [hbc](https://github.com/twitter/hbc).

The Twitter API is an authorisation-based, which means you either provide your account and password in the configuration, or create a [Twitter App](https://apps.twitter.com/) then use the token provided.

I'd strongly recommend the later. Surely I won't tell you it is because I can't get the account and password working :) but it is because there are a lot of benefits using [OAuth](http://stackoverflow.com/questions/7561631/oauth-2-0-benefits-and-use-cases-why).

The exact example I referenced can be found in hbc, yet I blended in the Producer in this case, so that we are actually injecting tweets into ActiveMQ.

{% highlight java %}
import com.google.common.collect.Lists;
import com.twitter.hbc.ClientBuilder;
import com.twitter.hbc.core.Constants;
import com.twitter.hbc.core.endpoint.StatusesFilterEndpoint;
import com.twitter.hbc.core.processor.StringDelimitedProcessor;
import com.twitter.hbc.httpclient.BasicClient;
import com.twitter.hbc.httpclient.auth.Authentication;
import com.twitter.hbc.httpclient.auth.OAuth1;
import org.apache.activemq.ActiveMQConnection;
import org.apache.activemq.ActiveMQConnectionFactory;
import javax.jms.*;
import java.util.concurrent.BlockingQueue;
import java.util.concurrent.LinkedBlockingQueue;
import java.util.concurrent.TimeUnit;

public class Tweet {
 private static final String consumerKey = "";
 private static final String consumerSecret = "";
 private static final String token = "";
 private static final String secret = "";

 private static String url = ActiveMQConnection.DEFAULT_BROKER_URL;
 private static String subject = "TWEETQUEUE";

    public static void main(String[] args) throws InterruptedException, JMSException {
     ConnectionFactory connectionFactory = new ActiveMQConnectionFactory(url);
        Connection connection = connectionFactory.createConnection();
        connection.start();

        Session session = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
        Destination destination = session.createQueue(subject);
        MessageProducer producer = session.createProducer(destination);

     BlockingQueue<String> queue = new LinkedBlockingQueue<String>(10000);
        StatusesFilterEndpoint endpoint = new StatusesFilterEndpoint();

        endpoint.followings(Lists.newArrayList(871686942L)); // @BBCOne

        Authentication auth = new OAuth1(consumerKey, consumerSecret, token, secret);

        BasicClient client = new ClientBuilder()
         .name("sampleExampleClient")
         .hosts(Constants.STREAM_HOST)
         .endpoint(endpoint)
         .authentication(auth)
         .processor(new StringDelimitedProcessor(queue))
         .build();

        client.connect();

        for (int msgRead = 0; msgRead < 1000; msgRead++) {
         if (client.isDone()) {
                System.out.println("Client connection closed unexpectedly: " + client.getExitEvent().getMessage());
                break;
         }

         String msg = queue.poll(5, TimeUnit.SECONDS);
         if (msg == null) {
                System.out.println("Did not receive a message in 5 seconds");
            } else {
                System.out.println(msg);
                TextMessage message = session.createTextMessage(msg);
                producer.send(message);
            }
        }

        client.stop();
    }
}
{% endhighlight %}

## 5. Send via WebSocket

Once the queue started filling with junks, errr, I mean tweets. We need to find a way to let the messages out before the queue [implodes](http://activemq.apache.org/producer-flow-control.html).

In order to deliver the feeds to my applications, there are a number of broadcasting mechanisms I can use, but bearing speed and performance in mind in the case of leaving network connection open and message wrapping, [WebSocket](https://developer.mozilla.org/en/docs/WebSockets/Writing_WebSocket_client_applications) seems to be a very [trendy and fashionable](https://www.engineyard.com/articles/websocket) choice.

WARNING: if you DO need to deal with ancient browsers (prior 2011, like I am kidding. NO! I am serious!), make sure WebSocket is supported!

{% highlight java %}
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.concurrent.BlockingQueue;
import java.util.concurrent.LinkedBlockingQueue;
import java.util.concurrent.TimeUnit;
import javax.jms.Connection;
import javax.jms.ConnectionFactory;
import javax.jms.Destination;
import javax.jms.Message;
import javax.jms.MessageConsumer;
import javax.jms.TextMessage;
import javax.websocket.CloseReason;
import javax.websocket.OnClose;
import javax.websocket.OnMessage;
import javax.websocket.OnOpen;
import javax.websocket.Session;
import javax.websocket.server.ServerEndpoint;
import org.apache.activemq.ActiveMQConnection;
import org.apache.activemq.ActiveMQConnectionFactory;
import org.apache.log4j.Logger;
import org.glassfish.tyrus.server.Server;
import org.json.JSONObject;

@ServerEndpoint(value = "/tweets")
public class TweetFeedServer {
    private static String url = ActiveMQConnection.DEFAULT_BROKER_URL;
    private static String subject = "TWEETQUEUE";
    private Logger logger = Logger.getLogger(this.getClass().getName());

    @OnOpen
    public void onOpen(Session session) {
        logger.info("Connected ... " + session.getId());
    }

    @OnMessage
    public void onMessage(String message, Session session) throws Exception {
        ConnectionFactory connectionFactory = new ActiveMQConnectionFactory(url);
        Connection connection = connectionFactory.createConnection();
        connection.start();

        javax.jms.Session jmsSession = connection.createSession(false, javax.jms.Session.AUTO_ACKNOWLEDGE);
        Destination destination = jmsSession.createQueue(subject);
        MessageConsumer consumer = jmsSession.createConsumer(destination);

        BlockingQueue<String> queue = new LinkedBlockingQueue<String>(10000);

        for (int msgRead = 0; msgRead < 1000; msgRead++) {
            String msg = queue.poll(5, TimeUnit.SECONDS);

            Message jmsMessage = consumer.receive();
            if (jmsMessage instanceof TextMessage){
                TextMessage textMessage = (TextMessage) jmsMessage;

                try {
                    JSONObject receivedMessage = new JSONObject(textMessage.getText());
                    JSONObject processedMessage = new JSONObject();

                    System.out.println("Processed message: " + receivedMessage);

                    processedMessage.put("name", receivedMessage.getJSONObject("user").getString("name"));
                    processedMessage.put("icon", receivedMessage.getJSONObject("user").getString("profile_image_url"));
                    processedMessage.put("message", receivedMessage.getString("text"));

                    System.out.println("Processed message: " + processedMessage);
                    session.getBasicRemote().sendObject(processedMessage);
                } catch(Exception e) {
                    System.out.println(e.getMessage());
                }
            }

        }

        connection.close();
    }

    @OnClose
    public void onClose(Session session, CloseReason closeReason) {
        logger.info(String.format("Session %s closed because of %s", session.getId(), closeReason));
    }

    // For testing only
    public static void main(String[] args) {
        Server server = new Server("localhost", 8025, "/websockets", TweetFeedServer.class);

        try {
            server.start();
            BufferedReader reader = new BufferedReader(new InputStreamReader(System.in));
            System.out.print("Please press a key to stop the server.");
            reader.readLine();
        } catch (Exception e) {
            throw new RuntimeException(e);
        } finally {
            server.stop();
        }
    }
}
{% endhighlight %}

# In summary

The setup of ActiveMQ and Twitter Integration are pretty straight forward, yet finding the way to produce and consume messages took a while to try, also deciding what to use and how to implement the broadcasting part is quite fun.

The truly potential of Message Queue is surely more than this, the live async update, fast and lightweight queueing provide a limitless space for [large scale integration project](http://www.redhat.com/products/jbossenterprisemiddleware/fuse/).

I hope this blog does give you some ideas to think about.
