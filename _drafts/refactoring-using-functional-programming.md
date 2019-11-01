---
layout: post
sub-title: Talks and Articles
elements:
- article
- functional_programming
- refactoring
- design_patterns
- java
- python
last_modified_at: 2019-11-01 17:09:14 +0000
title: Refactoring using functional programming
categories: Talks and Articles

---
# Refactoring using functional programming

## What

Refactoring is a tool used for making code more readable and understandable to humans, thereby maintainable. It does not effect the functionality. In fact it should not effect the functionality.

## Why

Refactoring is necessary when you have other people read your code. For that matter, if you revisit your code after a break, you may also find it difficult to understand. Refactoring also makes it easier to add new functionality and for others to utilize it in their code. So it is important to refactor code.

## When

You should consider refactoring when you find it difficult to make changes to your code. There are a few concepts that help in identifying when you need to refactor, like [Code Smells](https://medium.com/r/?url=https%3A%2F%2Fsourcemaking.com%2Frefactoring%2Fsmells "Code Smells"). Code is said to smell, when:

* An object refers to the internal attributes of another object (code envy)
* Method is too big
* Too many if-else conditions
* Duplication of code etc

## How

Common patterns occur in software engineering. These patterns have been identified and a set of best practices has been defined to model these patterns. These set of best practices are called Design patterns. Design patterns makes your code:

* Extensible
* Loosely coupled
* Readable
* Reuseable (as an API)

There are multiple design patterns that you can look up [here](https://medium.com/r/?url=https%3A%2F%2Fsourcemaking.com%2Fdesign_patterns "design patterns").

## Application

Let us look at the following example. Suppose I need to write a program as follows:

Name: ‘**extract-properties**’.

Description: **Extracts and prints value of properties from file**

Input:

* **file** (of type — xml | property | json ..) and
* **property names** **of values to extract**
* **dynamic map** (replaces the dynamic map key (if) found in the property values, with corresponding map values.)

Now we can split the problem into smaller problems as:

1. **Extract** properties from given file (**Extract** method should support file types like — xml, property, json.)
2. **Replace** placeholders with dynamic values

There are three ways to solve the problem.

1. Procedural
2. Object Oriented
3. Functional

Lets evaluate all three.

### Procedural

Procedural way of solving the problem,

* **main** method reads file content and calls **extract** method.
* **extract** method parses the content based on type, using an if condition that calls the specific parse method and extracts the properties and returns these properties.
* **main** method now call **replace** method to replace the placeholders with its dynamic values.

  ![](/uploads/StrategyDesignPattern-Procedural.png "Procedural implementation")

  **Advantages** of the above approach:
  1. Ease to write when extensions/modification are limited.

  **Disadvantages** of the above approach?
  1. This causes a bottleneck on the extract method. In a team of developers, constant updates to a single entity by all the developers will result in merge conflicts.
  2. Suppose you need to add another handler, say ‘YAML’ file type. You will need to add another if else block in ‘extract’ method. Hence the 'extract' method will increase in size as extensions are added. This will make it less readable. 
  3. Having business logic interspersed with your I/O operations will result in unit test cases that will need actual resource versus a mock.
  4. The resource I/O operations code cannot be reused in other parts of the code as it is tightly coupled to the business logic.

  ### Object Oriented

  Let’s have a base class ResourceHandler. This defines methods extract () and replace (). extract() needs to allow for custom implementation to support future enhancements. While replace() needs to be common logic to be used irrespective of the custom extract implementation.

  We can model this as follows:

![](/uploads/ObjectOriented-Structure.png "Class diagram of resource handler and its derivaties")

As you see ResourceHandler has methods:

1. public Map handle(). This contains the business logic, which for our purposes can be as simple as:

       public Map handle() {
         extract();
         replace();
       }
2. protected abstract void extract()
3. private void replace()

Now the abstract method ‘extract’ needs to be overridden by its child classes:

1. XmlResource
2. JsonResource
3. PropertiesResource

Below is the main class.

![](/uploads/ObjectOriented_Strategy_Pattern.png "Main calling class for the object oriented code")

Advantages of this approach:

1. This structure allows to add new extensions without modifying the base ‘handler’ method, that is invoked to extract and replace properties. So child classes cannot corrupt the business logic.
2. There is no more a bottleneck on the extract method as it was with the procedural code. Now each custom extract method has it’s own implementation in it’s own class. This will reduce the possible merge-conflicts between multiple developers working on different custom extract implementation.
3. Object ‘ResourceHandler’ is closed for modification while being open for extension. This is one of the pillars of the [SOLID](https://itnext.io/solid-principles-explanation-and-examples-715b975dcad4) principle. For example if, ResourceHandler is shared as jar, it can still be extended to support additional resource types, but the underlying base logic is still restricted.

Disadvantages:

1. The IO logic to extract properties for given type, is still coupled to the business logic, due to the structure of the Base class. As the only public method is handle, which calls the extract method and replace method. So we cannot use extract in isolation.

### Functional

Lets implement functional code using interfaces as follows:

![](/uploads/Functional_Lambda_Structure_Strategy.png "ResourceHandler Interface")

ResourceHandler interface defines a single method ‘extract’ that takes the file and list of properties to extract, and returns the corresponding extracted properties from the input file.

We will utilize the ResourceHandler in the business logic, and inject the specific implementation in to the class ExtractReplace that encapsulates the business logic as follows:

![](/uploads/Functional_ExtractReplace_Strategy.png "Class ExtractReplace")

Now the main class will look like this:

![](/uploads/Functional_Main_Lambda_Strategy_Pattern.png "Main class with Lambda function")

The main class creates an object of type ExtractReplace by passing an implementation of ResourceHandler Interface via the Lambda construct as seen. This demonstrates the capability to define adhoc anonymous implementations which do not need to be housed in a type of it’s own, thereby reducing structural complexity.

We can also follow a more structured approach, where the different flavours of ResourceHandlers are housed in their own types.

![](/uploads/Functional_Structure_Strategy.png "ResourceHandler Structure")

This structure allows to use XmlHandler/JsonHandler … objects independently for extracting properties. This is an advantage over Object-oriented approach shown above.

The main class can create objects of specific types of ResourceHandler and pass them to the ExtractReplace class as follows:

![](/uploads/Functional_Strategy_Pattern.png "Main class logic for Functional")

You can further decouple the if-else code for instantiating ResourceHandler by having a builder class (Using Builder Pattern).

![](/uploads/Functional_Builder_Main_Strategy_Pattern.png "Main class for Functional code with builder pattern")

The enhanced ExtractReplace class with ‘builder’ pattern will look like this:

![](/uploads/Functional_Builder_ExtractReplace_Strategy.png)

More on the builder pattern [here](https://dzone.com/articles/design-patterns-the-builder-pattern).

Advantages:

1. Over and above the advantages that are provided by Object Oriented approach, this also separates the extraction logic from the business logic. Hence you can reuse this extract method.
2. You can follow a structured approach by creating different implementation classes of ResourceHandler, which can be resused.
3. You can also follow a adhoc anonymous approach using Lambda functions. This is useful if you are not reusing the extraction logic else where and want to reduce the structural complexity.

### Final Notes

The above functional code is an implementation of Strategy pattern, similar to Collections.sort, where you can pass a custom Comparator implementation. As you can see the code is extensible, easier to consume, understand and extend.