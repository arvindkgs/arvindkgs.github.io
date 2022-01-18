---
layout: post
sub_title: Talks and Articles
elements:
- " formatting"
- " code"
- best practices
last_modified_at: 2022-01-18 07:13:52 +0000
categories: []
title: 'Best practises for formatting code '

---
Code is the blueprint of any software and making code easier to read should be one of the fundamental principles followed by all developers.

Code formatting is opinionated and individualistic. But looking at code for more than a decade, I found there are codes that just look easier to read. They seem to follow a set of best practices that can be useful for all of us developers.

1. Use fewer comments  
   Yes, that's right. While it sounds counter-intuitive, it looks to reduce the fatigue of the reader. The code should be self-explanatory. Example:

   {% highlight java  %}

       public Booking createBooking(@RequestBody Booking booking, RestTemplate restTemplate) {
       // In a "real" environment this would at the very least be a property/environment variable, but ideally something like Service Discovery like Eureka
       
       Restaurant restaurant = restTemplate.getForObject("http://localhost:8080/restaurants/" + booking.getRestaurantId(), Restaurant.class);
       
       // check if the restaurant actually exists 
       
       if (restaurant == null) {
       
           throw new RestaurantNotFoundException(booking.getRestaurantId());
       
       }
       
       // check if the number of diners in the booking is more than the number of seats in the restaurant
       
       if (restaurant.capacity() < booking.getNumberOfDiners()) {
       
           throw new NoAvailableCapacityException("Number of diners exceeds available restaurant capacity");
       
       }
       
       // if we got this far, the booking is valid and we can save it
       
       return repository.save(booking);
       }

     
   {% endhighlight %}

   This can be reduced to

   {% highlight java  %}

       public Booking createBooking(@RequestBody Booking booking, RestTemplate restTemplate) {
       Restaurant restaurant = restTemplate.getForObject("http://localhost:8080/restaurants/" + booking.getRestaurantId(), Restaurant.class);
       
        checkIfRestaurantExists(restaurant, booking.getRestaurantId());
       
        checkIfCapacityExists(restaurant, booking.getNumberOfDiners());
       
        return repository.save(booking);
       }
       
       private void checkIfRestaurantExists(Booking booking, Restaurant restaurant) {
           if (restaurant == null) {
               throw new RestaurantNotFoundException(booking.getRestaurantId());
           }
       }//etc

     
   {% endhighlight %}

   Reference: [https://trishagee.com/2021/05/14/do-we-need-comments-in-our-code/#more-1535](https://trishagee.com/2021/05/14/do-we-need-comments-in-our-code/#more-1535 "https://trishagee.com/2021/05/14/do-we-need-comments-in-our-code/#more-1535")
2. Using code formatters in IDE. I prefer to use [https://github.com/google/google-java-format](https://github.com/google/google-java-format "Google java format"). 
3. Using Intellij IDE [inspection](https://www.jetbrains.com/help/idea/code-inspection.html "inspection") and [intent actions](https://www.jetbrains.com/help/idea/intention-actions.html#apply-intention-actions "intent actions") to refactor code Reference: [https://lightrun.com/best-practices/the-quick-and-easy-guide-to-reformatting-code-in-intellij/](https://lightrun.com/best-practices/the-quick-and-easy-guide-to-reformatting-code-in-intellij/ "best practises reformatting code in intellij")
4. Using static code analysis tools like sonarqube to catch issues like deep nesting.
5. Fluent APIs  
   When using Java's fluent API to write a functional style of programming, the operator should be in the new line.  
   **Correct**

   {% highlight java  %}   
   _.onException(IllegalArgumentException.class)_

       _.handled(true)_

       _.setHeader(Exchange.CONTENT_TYPE, constant("text/plain"))_

       _.setBody().constant("Invalid request data");_

   {% endhighlight %}

   **Incorrect**  
   {% highlight java  %}   
   _.onException(IllegalArgumentException.class)._

       _handled(true)._  

       _setHeader(Exchange.CONTENT_TYPE, constant("text/plain"))._

       _setBody().constant("Invalid request data");_

   {% endhighlight %}
6. Test names  
   \- test should follow the BDD convention:

   _givenX_whenY_thenZ_

   \- the _given_ part is optional, but the other two are not

   **- example**:

   _whenSendingAPost_thenCorrectStatusCode_

   \- also, the delimiter (underline) should only be used between these sections, and not anywhere else

   \- **for example - this isn’t correct**:

   _whenSomethingHappens_andSomethingElse_thenSuccessfull_
7. **tests - structure**

   \- always use **a new line before the _then_ section** of the test

   \- for example (notice the new line):

   {% highlight java  %} 

   _public void whenSomething_thenSomethingElse {  
    // some preparation code belonging to the when section_

   _assert( ...)_

   _}_

   {% endhighlight %}
8. **code - new lines**

   \- here are a few suggestions of where to add (and not add new lines)

   \- add a new line before the comment: _// getters and setters_

   \- add a new line between the _given_, _when_ and _then_ sections of a test
9. **code - breaking a new line into multiple lines**

   \- **example 1 - incorrect**:  
   {% highlight java  %} 

   _public Object process(BeanContext context, Object object,_

   _String name, Object value) {_

   {% endhighlight  %} 

   \- **example 1 - correct**:  
   {% highlight java  %} 

   _public Object process(_

   _BeanContext context, Object object, String name, Object value) {_

   {% endhighlight  %} 

   \- **example 2 - incorrect**:  
   {% highlight java  %} 

   _@RequestMapping(_

   _value = "/ex/foos", headers = { "key1=val1", "key2=val2" },_

   _method = GET)_

   {% endhighlight  %} 

   \- **example 2 - correct**:  
   {% highlight java  %} 

   _@RequestMapping(_

   _value = "/ex/foos",_

   _headers = { "key1=val1", "key2=val2" },_

   _method = GET_

   _)_

   {% endhighlight  %} 

   \- **example 3 - incorrect**:  
   {% highlight java  %} 

   _public void givenDestWithNullReverseMappedToSourceAndLocalConfigForNoNull_

   __whenFailsToMap_thenCorrect() {_

   {% endhighlight  %} 

   \- **example 3 - correct**: either put the entire method on a new line or rename it; definitely don’t break a method name in the middle

   \- when breaking a line with an operator, put the operator on the new line

   \- **example - correct**:  
   {% highlight java  %} 

   CompletableFuture<String> completableFuture   
    = new CompletableFuture<>();

   {% endhighlight  %} 

   \- **incorrect**:

   {% highlight java  %} 

   CompletableFuture<String> completableFuture =

   new CompletableFuture<>();

   {% endhighlight  %} 