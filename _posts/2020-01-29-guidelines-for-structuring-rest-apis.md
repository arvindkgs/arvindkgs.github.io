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
comments: true
---
Guidelines for structuring REST APIs

I have employed the following practices to structure my REST APIs making them:

1. Intuitive (For Consumers)
2. Isolation of concern (For Developers)
3. Inherent Classification (For Consumers and Developers)
4. Extensibility (For Developers)

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

     >  POST: /ingest/{entity}
     >
     > Body: {
     >
     >  "type" : "PostgreSQL"
     >
     >  "connection-details" : "",
     >
     >  "contents" : "" 
     >
     >  } 

     For example:

              /ingest/rdbms

              /ingest/nosql ... etc
   * archive

     > POST: /archive/{entity}
     >
     > Body: {
     >
     >  "type" : "PostgreSQL"
     >
     >  "connection-details" : "",
     >
     >  "contents" : "" 
     >
     > }

The Java Controller Class for this will have following structure

{% highlight java  %} 

@RestController

class IngestController {

        @PostMapping(value = "/ingest/{emtity}", consumes = "application/json", produces = "application/json")

         public boolean ingestService(@PathVariable("operation") String entity, @ResponseBody IngestPojo params){

                  switch(entity) {

                     case "rdbms": //Logic for integrating with rdbms

                                             break;

                     case "nosql": //Logic for integrating with rdbms

                                             break;

                     //and-so-on

         }

}

//POJO for Request Body

class IngestPojo {

      String type;

       String connectionDetails;

       String contents;

}

 {% endhighlight %}

Disadvantages of this approach:

1. As the entry point in the above code is IngestController.ingestService(), each of the teams will need to update the same method leading to merge conflicts
2. The request body params are need to be general to support each of the entities - like nosql, rdbms, etc. Not enforcing strict parameter scope can lead to security threats and erroneous requests.
3. Can lead to code smells due to the switch statement growing in size as more entities are added.
4. No strict enforcement, consumer can pass an unsupported entity type in the path param.
5. As the number of entities grow, the endpoints also grow, leading to cumbersome documentation. So for the consumer looking consume these endpoints will be flooded with a long list of endpoints.

### Better approach

2. Entity -> Operation (Entity encompass operation)
   * rdbms

       POST: /rdbms/ingest
       Body: {
        "type" : "PostgreSQL"
        "connection-details" : "",
        "contents" : "" 
        }
        POST: /rdbms/archive
        Body: {
        "type" : "PostgreSQL"
        "connection-details" : "",
        "contents" : "" 
        }
   * nosql

       POST: /nosql/ingest
       Body: {
        "type" : "MongoDB"
        "connection-details" : "",
        "contents" : "" 
        }

   Each Entity will have its own Rest Controller class as follows:

   {% highlight java  %} 

   @RestController(path ="/rdbms")

   class RdbmsController {

           @PostMapping(value = "/ingest", consumes = "application/json", produces = "application/json")

            public boolean ingestService(@ResponseBody RdmsIngestPojo params){

                   //Logic for integrating with rdbms

            }

   }

   @RestController(path ="/nosql")

   class NosqlController {

             @PostMapping(value = "/ingest", consumes = "application/json", produces = "application/json")

             public boolean ingestService(@ResponseBody NosqlIngestPojo params){

                   //Logic for integrating with nosql

            }

   }

   {% endhighlight %}

Advantages:

1. Separate Rest Controller classes so developers can commit and push without merge conflicts.
2. Fixed endpoints(no path params) so less chances of invalid requests
3. Better grouping as all related operations are encapsulated in same class.
4. Better documentation. For example if more operations are added to the object storage APIs  like:
   * search
   * configure
   * index

   Then they will grouped as

       /rdmbs/ingest

                   /archive

                   /search

                   /configure

                   /index

       /nosql/ingest

                  /archive

                  ...
5. Each Entity is free to define it's own parameters. Hence you can enforce strict parameter scope. This is better for security and validations.
6. Lesser code smells

Side-note: When you need to group your APIs under an umbrella (be it entity/functional unit/workflow etc) make sure your umbrella/parent grows at a slower rate than your inner children. This gives a better structuring and visibility as your application grows.

Hopefully more to follow in this series on 'Designing REST APIs'.

{% if page.comments %}
<div id="disqus_thread"></div>
<script>
    /**
    *  RECOMMENDED CONFIGURATION VARIABLES: EDIT AND UNCOMMENT THE SECTION BELOW TO INSERT DYNAMIC VALUES FROM YOUR PLATFORM OR CMS.
    *  LEARN WHY DEFINING THESE VARIABLES IS IMPORTANT: https://disqus.com/admin/universalcode/#configuration-variables    */
   
    var disqus_config = function () {
    this.page.url = 'https://www.arvindkgs.com/2020/01/29/guidelines-for-structuring-rest-apis.html';  // Replace PAGE_URL with your page's canonical URL variable
    this.page.identifier = '2020/01/29/guidelines-for-structuring-rest-apis'; // Replace PAGE_IDENTIFIER with your page's unique identifier variable
    };
    
    (function() { // DON'T EDIT BELOW THIS LINE
    var d = document, s = d.createElement('script');
    s.src = 'https://arvindkgs-com.disqus.com/embed.js';
    s.setAttribute('data-timestamp', +new Date());
    (d.head || d.body).appendChild(s);
    })();
</script>
<noscript>Please enable JavaScript to view the <a href="https://disqus.com/?ref_noscript">comments powered by Disqus.</a></noscript>
{% endif %}
