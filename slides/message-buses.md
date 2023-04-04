---
theme: apple-basic
class: 'text-center'
lineNumbers: true
transition: slide-left
css: unocss
fonts:
  sans: 'Robot'
  serif: 'Robot Slab'
  mono: 'Fira Code'
layout: cover
---

<style>
.highlight {
  color: #4EC5D4;
}

.super {
    font-size: 0.6em;
    vertical-align: super;
    margin-left: 0.1em;
}

.sub {
    font-size: 0.6em;
    color: #888;
}
</style>

# Lightning Talk: Message Buses

Understanding Message Buses & Why we use them

<br/>

```cs {5,6}
public void Publish(IntegrationEvent evt, IDbContextTransaction? transaction)
{
    try
    {
        _eventPublishOrchestrator.Publish(evt, transaction);
        transaction?.Commit();
    }
    catch (Exception ex)
    {
        _logger.LogError(ex, "Failed to publish event: {@event}", evt);
        transaction?.Rollback();
    }
}
```

<style>
h1 {
  background-color: #2B90B6;
  padding-bottom: 0.5em;
  background-image: linear-gradient(45deg, #4EC5D4 20%, #146b8c 80%);
  background-size: 100%;
  -webkit-background-clip: text;
  -moz-background-clip: text;
  -webkit-text-fill-color: transparent;
  -moz-text-fill-color: transparent;
}
</style>

---

# Message Buses & Terminology

<v-clicks>

- <span class="highlight">Message</span> or <span class="highlight">Event</span>
- <span class="highlight">Message Bus</span> or <span class="highlight">Broker</span>
- <span class="highlight">Message Queue</span>
- <span class="highlight">Pub/Sub</span> or <span class="highlight">Topics</span>

</v-clicks>

---

# Messages or Events

<v-clicks>

A message is a single unit of communication

```cs
public class OrderCreatedEvent
{
    public Guid OrderId { get; set; }
    public Guid CustomerId { get; set; }
    public DateTime OrderDate { get; set; }
    public decimal Total { get; set; }
}
```

A message could be...

- A notification something happened
- A request for something to be done
- A response to a request
- A set of values

</v-clicks>

---

# Message Bus

<v-clicks>

A Bus is the <span class="highlight">system</span> which accepts and delivers your messages.

You might hear the phrase <span class="highlight">Broker</span> used instead of <span class="highlight">Bus</span>. Typically, they mean the same thing<span class="super">[1]</span>.

A Bus can run within your application, or it can be a <span class="highlight">separare service</span>. For modern use, we typicall mean a separate service.

Examples of standalone buses include:

- Azure Service Bus
- RabbitMQ
- Kafka

Consider the Bus to fill a similar role to a database <span class="highlight">server</span>. It's the service that holds your queues or tables, and processes your events or queries. 

<span class="sub">[1] Colloquially, when speaking about message buses. Don't @ me.</span>

</v-clicks>

---

# Message Queues

<v-clicks>

A <span class="highlight">Queue</span> is a channel for messages to be sent to and received from.

You might describe a queue as a <span class="highlight">channel</span> or <span class="highlight">pipe</span>. Messages go in one end, and come out the other.

Each queue will have a unique <span class="highlight">name</span> or <span class="highlight">address</span>.

Applications can send <span class="highlight">Events</span> into a specific queue, and other applications can <span class="highlight">subscribe</span> to that queue to receive the events.

</v-clicks>

---
layout: two-cols
---

# Message Queues

<v-clicks>

Messages can be stored in the queue <span class="highlight">for a long time</span>. How long is configurable, but the default for Azure Service Bus is 7 days.

This is useful for <span class="highlight">reliability</span>. If a subscriber is offline, the message will still be there when it comes back.

When a message is read from the queue and successfully processed, it's <span class="highlight">removed</span>.

Messages will only be read <span class="highlight">once</span>, regardless of how many readers are watching. If multiple apps need the event, we can send it to all of them with <span class="highlight">Pub/Sub</span>.

Single reads are useful. They allow us to <span class="highlight">scale</span> our apps. Adding more readers will 'spread' messages between them where we can be sure messages are not handled more than once.

</v-clicks>

::right::

<v-clicks depth="2">

![Message Queue 1](images/message-bus.png)
![Message Queue 3](images/message-bus-2.png)

</v-clicks>

---
layout: two-cols
---

# Pub/Sub or Topics

<v-clicks>

<span class="highlight">Pub/Sub</span> is a way of sending messages to multiple subscribers.

This is typically done with a special type of queue, called a <span class="highlight">Topic</span>.

A message sent to a topic will be delivered to <span class="highlight">all</span> subscribers.

</v-clicks>

::right::

<v-clicks depth="2">

![Pub/Sub](images/message-bus.png)

</v-clicks>

---

# Why use a Message Bus?

<v-clicks>

- <span class="highlight">Reliability</span>
- <span class="highlight">Scalability</span>
- <span class="highlight">Decoupling</span>
- <span class="highlight">Asynchronicity</span>

</v-clicks>

---

# Reliability

<v-clicks>

A message bus <span class="highlight">stores</span> messages for a long time.

This means that if a subscriber is offline, the message will still be there when it comes back.

Modern buses include additional reliability features, such as <span class="highlight">dead letter queues</span> and <span class="highlight">retry policies</span>.

</v-clicks>

---

# Scalability

<v-clicks>

A message bus allows us to <span class="highlight">scale</span> our apps.

If we want to increase processing throughput, we can add <span class="highlight">more subscribers</span>.

</v-clicks>

---

# Decoupling

<v-clicks>

A message bus allows us to <span class="highlight">decouple</span> our apps.

While beyond the scope of this talk, this is a key piece of groundwork of <span class="highlight">microservices</span>.

</v-clicks>

---

# Asynchronicity

<v-clicks>

A message bus allows us to <span class="highlight">asynchronously</span> communicate between apps.

This is useful for <span class="highlight">performance</span>. We can offload work to a background process, and return control to the user or continue with other work, instead of waiting.

</v-clicks>
