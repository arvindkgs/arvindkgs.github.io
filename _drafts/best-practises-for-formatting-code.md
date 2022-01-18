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
2. 