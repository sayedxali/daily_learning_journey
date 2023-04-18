# <span style='color: olive'>Day 1/90</span>

Date : 18-April-2023



## <span style='color: olive'>Introduction</span>

### <span style='color: navy'>Problem with Thread Request (traditional thread/request)</span>

The problem with this approach is that, one single thread is allocated for a request. Let's say we want to make a db call. Get the cells, and do some operations on those cells, or write the data cells on the file system. 

Db calls, File system, etc ... are I/O task (Input/Output).  These tasks are time consuming. During execution, the thread will be blocked in will stay in waiting state. 

> Each thread is assigned to 1MB of stack memory.  If you are expecting 500 concurrent request, you'd need 500MB of ram your ram will be used for the request alone without including the memory needed for the actual work!!!

### <span style='color: navy'>IO Models</span>

There are 4 types of IO models :

1.  **Synchronous + blocking** : this is the model that we've been using by default. We make a request, and wait for that request to give a response. 

    <span style='color: indigo'>REAL WORLD ANALOGY : </span>I called an insurance company, the automated phone service tells me to click this button, that button, until I reach the manager's office. They say manager is in meeting so please wait for 5 minutes. I'm going to wait for the meeting to end and then I can talk to the manager. Here, I'm the thread and Insurance company is the remote server. In this whole time, they've blocked, and I'm waiting for the response.

2.  **Asynchronous**: when we make request, we don't wait for the response instead, we leave the responsibility to another thread and busy ourselves (requested thread) with another logic. It sounds like a better solution but it's a bit difficult to deal with the threads.

    **<span style='color: purple'>From previous analogy</span> :** I give the phone to my friend and I go get myself busy with something else. My friend will do the work.

3. **Non-blocking** (event-driven model) : It is more difficult than asynchronous 🙄😂🤐

   <span style='color: purple'>**From previous example : **</span> Once I call the company, press all those options, It will make a note of my phone number and will call you later.

4. **Non-blocking + Asynchronous :** mixture of 2 and 3.

   <span style='color: purple'>**From previous example : **</span> My friend calls the company, and he don't have to wait for the response either. They'll call him.

The levels of I/O Models is :

easy < medium < hard < very hard obviously.

This is where Reactive programming helps us. It provides us nice abstraction that, we write code like synchronous but behind the scenes it follows non-blocking + async model. All the complexities are taken care of for us.

### <span style='color: navy'>Reactive Streams</span>

In a microservice pattern, we have lots and lots of services. For instance, when a user wants to buy a product. The *UserService* has to make a request to *OrderService*, this service has to make a  request to *PaymentService*. Also, payment service needs to know to the user-id to ship the product using *ShippingService*.

As you can see, there are a lot of I/O calls done in a sequential way bcuz we cannot call the PaymentService if we do not know the order-id. Also we need to do that for every user.

I/O-as-a-service

From previous example, our friend will act as a service. He will take requests and make calls to company. Since it is a non-blocking + async model, he won't wait for the response and instead, he'll take infinitely another request. 

From his perspective, it's unbounded never ending stream of requests. He gets the requests continuously and he need to handle them efficiently.

So for it to be solved, peoples like Netflix, Twitter, LinkedIn, they all came up with a specification for processing potentially unbounded streaming data. It is called **Reactive Stream Specification **like JPA.

This follows *Observer Pattern*. Like for instance twitter, we follow a highly skilled developer. We also have some followers. When that skilled developer posts a tweet, we may or may not like or comment or share on it. Our followers will also see that tweet. 

The person on top (developer) is the publisher, and the followers are observer.

![](D:\Spring-boot-Tutorial\!Reactive Programming\notes_pics\publisher_subscriber.png)

for our example, where we are both a follower and publisher, (publisher and subscriber) :

![](D:\Spring-boot-Tutorial\!Reactive Programming\notes_pics\processor.png)

### <span style='color: navy'>Reactive programming</span>

With all the introduction, Reactive programming is a subset of event-driven, asynchronous programming in which you register a set of callbacks (listeners), where data goes through the pipeline (chain).

We can see reactive programming as these 3 pillars :

- Asynchronous data processing
- Non-blocking
- Functional style / Declarative 

We have these implementations for reactive programming :

- Akka
- rxJava2
- Reactor

### <span style='color: navy'>Publisher & Subscriber Communication</span>

publisher subscriber communication steps :

- step 1 : Subscriber wants to connect 

  There will be 2 instances, Publisher and subscriber. Subscriber wants to get updates from publisher.


- step 2 : Publisher calls onSubscriber

  When the publisher accepts the subscriber, he hands over the subscription object to the subscriber.


- step 3 : Subscription 

- step 4 : Publisher pushes data via onNet

  When the subscriber requests let's say 4 for an object from the publisher using subscription object, the publisher will respond with 4 items and calling `onNext()` method 4 times.


- step 5 : onComplete

  When the publisher has already emitted all the items to the subscriber, the publisher can call the `onComplete` method to notify subscriber.

  
- step 6 : onError

  If an error occurs, then publisher will notify the subscriber with the error details.


Terminologies

- Publisher
  o Source
  o Observable
  o Upstream
  o Producer
- Subscriber
  o Sink
  o Observer
  o Downstream
  o Consumer
