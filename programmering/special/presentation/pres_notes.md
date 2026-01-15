# Overview
* Patterns and anti-patterns meant to help us find "problems" in our programs
	* Gives a common solution to a common problem
	* Also gives developers a common vocabulary to use while describing their work.
* Since patterns are meant to describe and solve problems without care of architecture and language, their solutions are solution descriptions on a higher level, not code able to be pasted.
* While researching patterns and anti-patterns, I found a range of different pattern categories, as presented here;
* The names are pretty self explanatory, but will present a bit more about them, as well as one or more patterns or anti-patterns per category.
	* For some categories, mainly Communication and Data handling, there existed much more patterns than some of the others. I have therefore decided to further divide these into several subcategories.
# Communication
* Patterns related to sending information between different computing components, such as different functions, classes, or different computers.
* Two different subcategories: Network and Internal
* Network further divided
	* Data Handling
		* Which data do we include in a Request?
		* Can we combine several requests into one?
		* Empty semi-truck example of anti-pattern
	* Request Handling
		* What can we do while waiting for a request?
		* Asynchronous request-reply example of pattern.

## Network Communication
* Only subcategory with its own subcategories
### Empty Semi-truck
* Think of the population of a web store.
	* Need to get the account's name, profile picture, region, wishlist, shopping cart
	* Need to get the categories of wares, the wares names, their icons, descriptions and price.
	* The backend might only allow for one or a few wares to be fetched at once, or not for all the information to be gathered in the same request
	* This is what's called an empty semi-truck, either a poorly written server or client leading to an unnecessary amount of message sending in between.
* If server issue
	* Allow for sending multiple of the same object when appropriate
	* Allow for combining several requests into one message.
* If client issue Much easier
	* Refactor code to remove excess requests.
### Asynchronous Request-reply
* Anyone having used the internet knows it can take time to receive data sent over it.
* Especially when the server itself needs information from other sources, or for other reasons will process the request slowly.
* This pattern suggest using the 202 and 302 status codes to not have clients waiting for results.
	* Immediately returns 202, meaning it has accepted the request, will give result later.
	* The client later asks for the result when it is needed.
	* If result is ready, redirects to where the result is found.
* Bit more complex to use than the previous one
* Allows us to ask for results before needed, letting server load in background.
	* For example a website can allow loading of
## Internal Communication
* As mentioned, internal communication discusses patterns related to patterns where the time to send/receive a message is lower, and the execution of requests is more important.
### Choreography
* By severing the direct connection between clients and their services, it becomes easier to scale up
* The choreography pattern suggest using a queue of tasks, where workers can pull tasks they can complete when they are free.
* This is very similar to the worker pool  pattern, with the key differentiator being this pattern uses services instead of workers.
	* All services are not able to complete all requests in the queue, while in competing consumers, or worker pools, every worker is able to complete every task in the queue.

# Data Handling
* Patterns and anti-patterns related to storing and loading of data.
* Most discuss caching, some discuss other ways of optimising fetching of data
## Caching
### Cache-aside
* Very simple pattern
* When building application where the data stored, either in databases, buckets, or other types of long time storage, loading of said data is slow.
* Instead implement a cache on the client side, where each request first checks the cache
	* If the check is a hit, and the data hasn't been stored for too long, use that data.
	* If the check doesn't find any data, or the data found is too old, instead perform an I/O request to find the data.

# Parallel Execution
* Several patterns discuss how to use asynchrosity and parallelism to improve specific tasks
	* This category does **not** discuss using parallelism to solve a specific problem
	* Instead discusses how to improve our usage of parallelism.
* The competing Customers pattern is a great example of one of these patterns.
### Competing Consumers
* Possible anecdote about parallel computing with workers
* Very similar to the choreography pattern
* When scaling a service horizontally, or parallelise a task, we run into a problem of understanding how to best allocate tasks between them.
* Depending on how the threads or services are schedule their performance can differ
	* Depending on the size of the different tasks also
* As you can see, there's several problems that arise by directly assigning tasks to separate instances of a service
	* If we instead create a queue we append tasks to, all process can assign themselves a task when they are free, minimising synchrosity in execution.
# Orchestration
* These patterns instead discusses connections between different services
	* Not the Communication
* How to have several instances of the same service
* How to share computing resources between different services or instances of services.
### Chaotic Session Management
* When implementing a system, with several instances of the same service, persistance of state might become an issue.
	* If a user connects to instance 1, does some things, and want to get the result of what they did, it becomes a problem if during this process, they were switched to using another instance.
	* This can be solved by sharing session dada between instances, but moving sessions between processes can be time consuming, and limits the benefits gotten from running several instances of the same service.
* To solve this we can assign each new session to a separate instance, forcing the usage of the same instance until the session is over.
* This is not perfect since it's hard to know when assigning a session to an instance how long lasting and how resource consuming it will be, but a slightly worse performance in one instance is preferable to worse performance for every session.

# Execution Ordering
* Self explanatory name
* A parallel program with execution steps ordered badly can be forced to execute synchronously.
	* For example performing operations on text in different files, if each file is fetched after the previous file is completed, smaller files decrease effectiveness of parallelism.
	* By instead fetching data from all files at the start of the execution might lower execution time, but might also increase memory utilisation.
### Priority Queue
* Working with systems, where several tasks happen, and maybe not all are of equal importance, priority queues intend to help
* For example in cloud computing, can assign users to different tiers, making sure that paying customers get a better experience than free tier users.
* Two different systems of priority queues with their own benefits.
* With a single queue, it gets sorted every time a new message is added, making sure the highest priority items are at the front.
	* Might lead to starvation if new higher priority tasks keep pushing lower priorities down.
* We can instead create one queue per priority level.
	* Lets us assign workers to each priority level, solving the starvation problem
	* Can be hard to create new levels of priority while system is active, since it requires creating new queues instead of just changing a number.
# Conclusion
* In conclusion several patterns often exist for the different problems you might face during performance engineering
* Unlike patterns in fields such as Object-Oriented Programming, patterns seem to be not as a developed discussion point in performance engineering, it is common to find the same pattern described under different names, and several extremely similar patterns.
* As stated previously, patterns are described in such a way to be independent of architectures and programming languages
	* This means they aren't a cut and paste solution to a given problem
	* But usually patterns still aren't hard to implement, most patterns come with a guide on what to do to fix a given problem
* Patterns almost always gives a good solution to a given problem (provided the pattern is meant to solve that problem)
	* With that said they might not be the best solution for a problem
	* This isn't a problem in most fields, but for performance engineering one could argue it's better not using patterns in certain classes
		* Though probably better to use pattern in almost every case.
