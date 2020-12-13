When we want to take a project and turn it into a product, we need to think about overall system architecture.

Wikipedia defines System Architecture as: \
_"A system architecture is the conceptual model that defines the structure, behavior, and more views of a system._
 
## Scaling
As a product grows in User base, scaling becomes an increasingly pressing issue. As the number of Users and/or actions taken in our product increase, we want to make sure our systems can handle the extra work. If we take this into consideration from the outset, we are taking steps to avoid scaling problems in the future.

When considering options in approach to scaling, the scale cube, as described in [The Art of Scalability](http://theartofscalability.com/) can assist.

### x-axis scaling
- clones of the full code base share workload as distributed by a load balancer
  
### y-axis scaling
- the product features are split over multiple services that can run independently and are very good at one thing

### z-axis scaling
- clones of the full code base manage the workload between them but each clone handles just one subset of data
  
***

## Architecture
Some questions to ask at the start of a project are:
- Do we need to worry about scaling at all?
- What languages/frameworks are we using?
- What database system are we using?
- Where will the database be stored?
- What APIs will we be interacting with?

From the answers to these questions we can start considering the overarching architecture of our product.

### Monolith
A monolithic application keeps the entire code in one big code base. It is a good way to get prototypes up and running and can be suitable for start-ups who need something quick and functional. Netflix, Amazon and eBay all started life as monoliths! For a small team working on a small set of features this can work but as complexity increases and/or the team size increases, having one codebase can be incredibly hard to manage and can be overwhelming for new team members.

### Microservices
A popular alternative is to use multiple microservices. These indepedent microservices are very good at one thing and can talk to each other using API calls. In a product team, each service can have it’s own subteam and should be able to be deployed separately from other services thus providing shorter test and iteration times. A distributed system such as this can, however, bring it's own issues - what if one microservice is down? Is there a fallback process? Examples of naming/responsibilities of microservices might be: 'checkout', 'shipping', 'account'.

### Layered Approach
A twist on the microservices approach is to use multiple servics but have them responsible for a specific 'layer' of product. The three layers in this approach are:
- **Presentation** [where everything someone sees happen]
- **Application** [where all of the processing and thinking happen]
- **Database** [where all of our data is stored]

***
## Design Patterns
Once decided on an architecture approach, we can consider how to design each service (or the monolith). Within any architecture you may use more than one design pattern in different parts of the product.

There are many document design patters and often patterns are created to solve problems with a previous way of building software, or because a way of building software was being repeated and someone thought they had better document and analyse it!

Let's look at three design patterns:

### MVC
**M**odel **V**iew **C**ontroller is a tried and tested pattern that was first documented in the 1970s!
- **Model** is where we manage all of our data and our business logic.
- **View** is what the user sees and where they interact with our product.
- **Controller** is what handles any requests between the two.

### Flux
Flux was created to solve the 'problem' in MVC where data can flow in many directions, always coming back through to the controller. Flux is designed to be similar to MVC but have data flow in only one direction so that it is easier to debug.

Flux has four components:
- **Views** is what the user sees but more complicated than in MVC as they are constantly listening to the store and updating themselves accordingly.
- **Stores** are where our data is kept.
- **Actions** are small objects, tiny chunks of data which can represent anything. Examples could be a user’s click.
- **Dispatcher** which processes the actions and updates the stores accordingly. These are much more lightweight than controllers in MVC and are often reused.

### Redux
Redux was designed to simplify Flux to have one centralized store and a reducer responsible for all business logic thus making debugging a smoother process.

***

## Formalising Your Design
Having an architecture description is extremely useful when planning and ensuring a shared understanding across the team. It can also help you to spot potential issues in advance. From a napkin doodle, to using an ADL (Architecture Design Language), a representation of your system architecture is really worth the effort!

_An architecture description is a formal description and representation of a system, organized in a way that supports reasoning about the structures and behaviors of the system."_ - Wikipedia


 