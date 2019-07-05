---
title: "When to Use What: REST, GraphQL, Webhooks, & gRPC"
date: 2019-07-05
comments: true
categories: 容器
tags:
    - watch
---

With all of the love and proclamations about REST, we can sometimes forget that it’s simply one of many options. REST is a very good standard for a wide variety of APIs, but there are other `API design styles` for more nuanced scenarios.

To help API developers make sense of which API design style to use and for what situation, let’s look at `REST` within the context of three other options – `gRPC`, `GraphQL`, and `Webhooks`. We’ll offer `real world examples` of REST, GraphQL, gRPC, and Webhooks in practice, and analyze their strengths and weaknesses to highlight what makes each option a good choice.

 <!-- more -->

[文章转载](https://nordicapis.com/when-to-use-what-rest-graphql-webhooks-grpc/)

# REST

`REST` is probably the most commonly known item in this piece, as it has become very common amongst web APIs. REST as a concept was first defined by Roy Fielding in his doctoral dissertation in the year 2000. He laid out the groundwork for an architectural system defined by a set of constraints for web services, using a stateless design ethos and a standardized approach to building web APIs.

REST by its very nature is stateless, and is built in such a way that any web service that is compliant with REST can interact in a stateless manner with textual resource representations. These operations are usually defined using GET, POST, PUT, and other HTTP methodologies as a matter of standardized interactions.

One of the chief properties of REST is the fact that it is `hypermedia` rich. Hypermedia and REST are so closely reliant, in fact, that Roy Fielding has stated that APIs are technically not RESTful if they do not support hypermedia. This ultimately means that in a REST API, the client and server are `loosely coupled`, which grants both clients and servers extreme amounts of freedom in resource manipulation. Due to this, rapid iteration, server evolution, resource provision elasticity, and other such elements are enabled and supported.

REST supports a lot more than we’ll dive into here, but with a `layered architecture`, efficient `caching`, and high scalability, REST is a highly discoverable and highly morphable solution to a multitude of issues and constraints. The value of having standardized `HTTP` verbiage is hard to understate, providing context to the end user, and standardizing most interactions. All told, REST is a very efficient, effective, and powerful solution for the modern microservice API industry.

> More on REST: [Is REST Still a Relevant API Style?](https://nordicapis.com/is-rest-still-a-relevant-api-style/)

## Real-World Example: PayPal

One example of this type of API is the PayPal REST API. PayPal has a strong core business function — provide the integrated systems for payment processing. Accordingly, their APIs have to make this easy. Resources must be easily identifiable, calls must be understood with and without context, and most importantly, a variety of media must be supported in order to effectively handle a wide range of payment types and methodologies.

To this end, the PayPal API is designed to be easy to understand and easy to integrate with. Look at this example, taken from their documentation, in which a call lists a range of activities within the API:

```bash
curl -v -X GET https://api.sandbox.paypal.com/v1/activities/activities?start_time=2012-01-01T00:00:01.000Z&amp;end_time=2014-10-01T23:59:59.999Z&amp;page_size=10 
-H "Content-Type: application/json" 
-H "Authorization: Bearer Access-Token"
```

Here, we can see the hallmarks of an effective RESTful implementation. We can see a standard HTTP verbiage form in GET doing exactly what GET should do — retrieve a resource. The URI in this case is well defined as “activities”, and allows for specification of a time zone and in-line request constraints for page size. Additionally, the return is in a specified, known, hypermedia-supporting format. This is REST in a nutshell, and is an example of a use case in which a lightweight, stateless system is exactly what is needed to deliver the resources to the end client.

# gRPC

While REST is decidedly modern, `gRPC` is actually a new take on an old approach known as `RPC`, or Remote Procedure Call. RPC is a method for executing a procedure on a remote server, somewhat akin to running a program on a friend’s computer miles from your workstation. This has its own benefits and drawbacks – these very drawbacks were key in the development and implementation of REST, in fact, alongside other issues inherent in systems like SOAP.

A key difference between gRPC and REST is the way in which RPC defines its contract negotiation. Whereas REST defines its interactions through terms standardized in its requests, RPC functions upon an idea of contracts, in which the negotiation is defined and constricted by the client-server relationship rather than the architecture itself. RPC gives much of the power (and responsibility) to the client for execution, while offloading much of the handling and computation to the remote server hosting the resource.

For this reason, RPC is very popular for IoT devices and other solutions requiring custom contracted communications for low-power devices. REST is often seen as being overly demanding of resources, whereas RPC can be used even in `extremely low-power situations`. gRPC is a further evolution on the RPC concept, and adds a wide range of features.

The biggest feature added by gRPC is the concept of `protobufs`. Protobufs are language and platform neutral systems used to serialize data, meaning that these communications can be efficiently `serialized` and communicated in an effective manner. Additionally, gRPC has a very effective and powerful authentication system that utilizes SSL/TLS through Google’s token-based system. Lastly, gRPC is also `open source`, meaning that the system can be audited, iterated, forked, and more.

> More on gRPC: [Exploring The gRPC Framework for Building Microservices](https://nordicapis.com/exploring-the-grpc-framework-for-building-microservices/)

## Example APIs – Google Cloud, Bugsnag

It’s difficult to demonstrate gRPC in the wild — this comes largely from the fact that, [according to the gRPC documentation itself](https://grpc.io/docs/), gRPC is typically used in the “last mile of computing.” In other words, gRPC is usually the end system driving and facilitating communication between disparate services and APIs.

Nonetheless, the gRPC documentation cites that, due to its transportability, gRPC is used within the mobile computing space, as well as an intermediary and processing system for data from the `Google Cloud BigTable Client API`, the `Google Cloud PubSub API`, and the `Google Cloud Speech API`. This makes sense, as the use of standard transport mechanisms and the relatively agile data load gRPC offers can be best utilized for streamlined, active, and repetitive communications.

Another example of gRPC in production can be found with [Bugsnag](https://blog.bugsnag.com/using-grpc-in-production/), a stability monitoring service. The Bugsnag engineering team found the initial design process smoother than building RESTfully. Eventually, however they found that the “barrier to entry for developing and testing gRPC was quite high,” due to a lack of [tutorials and best practices](https://github.com/grpc-ecosystem/awesome-grpc). Overall, the latency improvements and decreased transport costs made using gRPC a huge success for Bugsnag.

# GraphQL

GraphQL’s approach to the idea of client-server relationships is unique amongst these options, and is somewhat of a reversal of the traditional relationship. With GraphQL, the client determines what data they want, how they want it, and in what format they want it in. This is a reversal of the classic dictation from the server to the client, and allows for a lot of extended functionality. GraphQL is starkly different from REST, which is more an architecture than anything else, and from RPC, in which the contract is negotiated by client and server but largely defined by the resources themselves

It should be noted that a huge benefit of GraphQL is the fact that, by default, it typically delivers the smallest possible request. REST, on the other hand, typically sends everything it has all at once by default – the most complete request, in other words. Because of this, GraphQL can be more useful in specific use cases where a needed data type is well-defined, and a low data package is preferred.

That being said, GraphQL’s benefits are often somewhat oversold. The idea that you never have to version is derived from deprecating fields and replacing them with new ones, which is what REST evolution is concerned with. Thus, you shouldn’t think of GraphQL as “better” than REST or the “next step”, as it is often framed, but rather as an alternative option for a “new relationship between client and data”.

## Example API – GitHub

One example of GraphQL usage can be found in the GitHub API GraphQL API. While the initial RESTful API was powerful, and did what was requested, the GitHub team found that the REST API was inflexible. Speaking on the problem, the team said that the API responses “simultaneously sent too much data and didn’t include data that consumers needed,” which is the classic pain point that caused the development of GraphQL in the first place.

Accordingly, GitHub needed a way to deliver their content to requesters without requiring multiple distinct, complex calls. They needed to allow users to morph their requests, stating what exactly they needed. And most importantly, they needed the API to still be capable of handling the basic requests that the bulk of their REST API already handled efficiently. To that end, Github added support for GraphQL, delivering these key functionalities.

# Webhooks

While GraphQL is an option to extend an API, and gRPC is a re-tooling to a classical approach, `Webhooks` are an entirely different approach to resource provision than anything discussed here. A Webhook is relatively simple – simply put, it’s an HTTP POST that is triggered when an event occurs.

This is a reversal of the classic client-server relationship — in the classic approach, the client requests data from the server, and the server then provisions that data for the client. Under [the Webhook paradigm](https://sendgrid.com/blog/whats-webhook/), the server updates a provisioned resource, and this then automatically sent to the client as an update. The server pushes this data. Thereby, the client becomes not a requester, but a passive receiver.

Ultimately, this reversal can be used to facilitate a lot of communication that would otherwise require more complex requests and constant polling on the remote server. By simply receiving the resource without requesting it directly, you can update remote codebases, distribute resources easily, and even integrate into existing systems to update endpoints and other data concerning the API proper.

> Related: WebHooks vs WebSub: [Which Is Better For Real-Time Event Streaming?](https://nordicapis.com/webhooks-vs-websub-which-one-is-better-to-stream-your-events-in-real-time/)

## Example API – Foursquare (and SendGrid)

Webhooks are a relatively simple and effective offering, and thus, their implementation is equally simple and effective. The [Foursquare method](https://developer.foursquare.com/docs) of using a webhook is essentially a flow in which the user “checks in,” prompting a webhook to push updated content to other systems and portals. In this way, the user can directly interact with the location that they are visiting while alerting others as to the nature of their relationship with the location via a client-resource analogy.

As you get further into webhooks as an implementation, you’ll often see more complex integrations. For example, SendGrid uses webhooks to send [event data updates](https://sendgrid.com/docs/API_Reference/Event_Webhook/event.html) to subscribing clients, alerting them to changes to a large number of variables. SendGrid even implements a hybridized webhook method to [parse emails](https://sendgrid.com/docs/API_Reference/Webhooks/inbound_email.html)!

# Comparing Use Cases For REST, GraphQL, Webhooks, and gRPC

As you can plainly see, none of these options are truly “better” than the others, but instead fit into unique interaction scenarios. We can summarize these use cases as follows:

* `REST`: A stateless architecture for data transfer that is dependent on hypermedia. REST can tie together a wide range of resources that might be requested in a variety of formats for different purposes. REST is fundamentally concerned with stateless resource management, so it’s best used in such situations. Systems requiring rapid iteration and standardized HTTP verbiage will find REST best suited for their purposes.

* `gRPC`: A nimble and lightweight system for requesting data. gRPC, on the other hand, is best used when a system requires a set amount of data or processing routinely, and in which the requester is either low power or resource-jealous. The IoT is a great example of this.

* `GraphQL`: An approach wherein the user defines the expected data and format of that data. GraphQL is from Facebook, and that pedigree demonstrates its use case well — situations in which the requester needs the data in a specific format for a specific use. In those cases, those data formats and the relations between them are vitally important, and no other solution provides the same level of interconnected provision of data.

* `Webhooks`: Data updates to be served automatically, rather than requested. Finally, Webhooks are best used when the API in question primarily updates clients. While such APIs can also have other functions, even RESTful ones, the primary use of a Webhook microservice should be to update clients and provide updated, provisioned data upon the creation of the new, updated resource.

Choosing amongst these specific options is really a matter of aligning your business functions with the appropriate approach, and ensuring that the systems in place respond within the given parameters.

# Conclusion

Choosing a design approach is perhaps the most important decision made in early API development. It both structures the API, and impacts how the end user will interact with the resources behind the API itself. In other words, this is not just a choice of approach for the developer — this is a choice for how you are going to establish your relationship with your consumers.

Ultimately, the choice of which solution you go with comes down to what fits your particular use case. Each solution has a very specific purpose, and as such, it’s not fair to say one is better than the other. It is, however, more accurate to say that some are better at doing their core functions than the other solutions — such as the case of many RESTful solutions attempting to mirror RPC functionality.

Only you can determine which solution is best given your codebase. So, do your research and choose the correct approach from the get-go to reap some serious benefits. Your code will be leaner, more responsive, and tailored to the situation at hand.