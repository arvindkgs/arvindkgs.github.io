---
layout: post
sub_title: Talks and Articles
elements:
- article
- formatting
- best practices
- code
last_modified_at: 2022-01-18T15:30:51.000+00:00
categories: []
title: Best practises for formatting code

---
Code is the blueprint of any software and making code easier to read should be one of the fundamental principles followed by all developers.

Code formatting is opinionated and individualistic. But looking at code for more than a decade, I found there are codes that just look easier to read. They seem to follow a set of best practices that can be useful for all of us developers.

 1. Use fewer comments  
   Yes, that's right. While it sounds counter-intuitive, it looks to reduce the fatigue of the reader. The code should be self-explanatory. Example:

    ```java
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
    ```

    This can be reduced to

    ```java
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
    ```

 2. Using code formatters in IDE. I prefer to use [google java format](https://github.com/google/google-java-format "Google java format").
 3. Using [maven formatter plugin](https://github.com/coveooss/fmt-maven-plugin) to auto format code on build
 4. Using Intellij IDE [inspection](https://www.jetbrains.com/help/idea/code-inspection.html "inspection") and [intent actions](https://www.jetbrains.com/help/idea/intention-actions.html#apply-intention-actions "intent actions") to refactor code Reference: [https://lightrun.com/best-practices/the-quick-and-easy-guide-to-reformatting-code-in-intellij/](https://lightrun.com/best-practices/the-quick-and-easy-guide-to-reformatting-code-in-intellij/ "best practises reformatting code in intellij")
 5. Using static code analysis tools like sonarqube to catch issues like deep nesting.
 6. Fluent APIs\
    When using Java's fluent API to write a functional style of programming, the operator should be in the new line.  
    **Correct**
    ```java
    .onException(IllegalArgumentException.class)
    .handled(true)
    .setHeader(Exchange.CONTENT_TYPE, constant("text/plain"))
    .setBody().constant("Invalid request data");
    ```

    **Incorrect**  
    ```java
    .onException(IllegalArgumentException.class).
    handled(true).
    setHeader(Exchange.CONTENT_TYPE, constant("text/plain")).
    setBody().constant("Invalid request data");
    ```
  
 7. Test names\
    test should follow the BDD convention: givenX_whenY_thenZ\
    *the _given_ part is optional, but the other two are not*\
    __example__:
    ```java
    whenSendingAPost_thenCorrectStatusCode
    ```

    The delimiter (underline) should only be used between these sections, and not anywhere else\
    **for example - this isn’t correct**:
    ```java
    whenSomethingHappens_andSomethingElse_thenSuccessfull
    ```
7. **tests - structure**\
    always use **a new line before the _then_ section** of the test
    **for example (notice the new line):**

    ```java
    public void whenSomething_thenSomethingElse {  
        // some preparation code belonging to the when section

        assert( ...)
    }
    ```
      
 8. **code - new lines**
    here are a few suggestions of where to add (and not add new lines)
    * add a new line before the comment: _// getters and setters_
    * add a new line between the _given_, _when_ and _then_ sections of a test
      
 9. **code - breaking a new line into multiple lines**\
    **example 1** - <span style="color:red;font-size:16.0pt" > incorrect </span>
    ```java
    public Object process(BeanContext context, Object object,
    String name, Object value) {
    ``` 

    **example 1** - <span style="color:green;font-size:16.0pt" > correct </span>
    ```java
    public Object process(
    BeanContext context, Object object, String name, Object value) {
    ```

    **example 2** - <span style="color:red;font-size:16.0pt" > *incorrect* </span>
    ```java
    @RequestMapping(
    value = "/ex/foos", headers = { "key1=val1", "key2=val2" },
    method = GET)
    ```

    **example 2** - <span style="color:green;font-size:16.0pt" > *correct* </span>
    ```java
    @RequestMapping(
    value = "/ex/foos",
    headers = { "key1=val1", "key2=val2" },
    method = GET
    )
    ```

    **example 3** - <span style="color:red;font-size:16.0pt" > incorrect </span>
    ```java
    public void givenDestWithNullReverseMappedToSourceAndLocalConfigForNoNull
    _whenFailsToMap_thenCorrect() {
    ```

    **example 3** - <span style="color:green;font-size:16.0pt" > correct </span>\
    Either put the entire method on a new line or rename it; definitely don’t break a method name in the middle
    when breaking a line with an operator, put the operator on the new line

    **example 4** - <span style="color:red;font-size:16.0pt" > incorrect </span>
    ```java
    CompletableFuture<String> completableFuture =
    new CompletableFuture<>();
    ```
    

    **example 4** - <span style="color:green;font-size:16.0pt" > correct </span>
    ```java
    CompletableFuture<String> completableFuture  
    = new CompletableFuture<>();
    ```

### References

1. Reference: \[do-we-need-comments-in-our-code\]([https://trishagee.com/2021/05/14/do-we-need-comments-in-our-code/#more-1535](https://trishagee.com/2021/05/14/do-we-need-comments-in-our-code/#more-1535 "https://trishagee.com/2021/05/14/do-we-need-comments-in-our-code/#more-1535"))
2. Baeldung