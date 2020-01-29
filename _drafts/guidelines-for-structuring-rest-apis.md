---
layout: post
sub_title: Talks and Articles
elements:
- article
- restapis
- best practices
- guidelines
last_modified_at: 2019-11-01T18:57:51.000+00:00
categories: []
title: Guidelines for structuring REST APIs

---
Guidelines for structuring REST APIs

I have employed the following practices to structure my REST APIs making them:

1. Intuitive (For Consumers)
2. Isolation of concern (For Developers)
3. Inherent Classification (For Consumers and Developers)
4. Extensive (For Developers)

Any RESTFUL application can be classified into following constructs:

1. Entities
2. Operations (Action to be performed on Entities)

Consider you are designing an **Object Storage API** similar to AWS S3 that supports operations like:

1. Ingest
2. Archive

Supported Entities are:

1. RDBMS (PostgreSQL, MySQL)
2. NoSQL (MongoDB, Redis)
3. TimeSeries (InfluxDB)
4. Message Queues (RabbitMQ)

Assume, each of these entities have different teams building custom integrations for them.

Now you could design your REST APIs as:

1. Operation->Entity (Operation encompass entities)
   * ingest

       POST: /ingest/{entity}
       Body: {
        "type" : "PostgreSQL"
        "connection-details" : "",
        "contents" : "" 
        }

   For example:

   /ingest/rdbms

   /ingest/nosql ... etc
   * archive

    POST: /archive/{entity}
    Body: {
     "type" : "PostgreSQL"
     "connection-details" : "",
     "contents" : "" 
     }

The Java Controller Class for this will have following structure

{% highlight java  %} 

@RestController

class IngestController {

        @`PostMapping(value = "/ingest/{operation}", consumes = "application/json", produces = "application/json")`

         public boolean ingestService(@`PathVariable("operation") String` `operation`){

         }

}

 {% endhighlight %}

OR

2. Entity -> Operation

   POST: