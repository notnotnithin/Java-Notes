**CompletableFuture** 

1. CompletableFuture was introduced in Java8. CompletableFuture is an Asynchronous Reactive Functional Programming API which allows you to write asynchronous code using the functional programming style. Basically, this API allows you to write asynchronous code similar to the code that we write using Streams API.
2. CompletableFuture is created to overcome the limitations of Future API.

**Relationship between CompletableFuture and Reactive API/Reactive Programming** 

A Reactive API has these 4 qualities.

* Responsive
* Resilient
* Elastic
* Message Driven 

***Responsive*** 

This means any code that you write will be responsive, which fundamentally means asynchronous. Let's say you have a blocking code which could be a REST API or a DB call. The call will return immediately and the response will be sent to the caller code when the data is available. 

***Resilient*** 

This means any exception or error that happens during the execution of the code won't break your application or code. 

***Elastic*** 

This means asynchronous computations normally run in a pool of threads when you use CompletableFuture. The pool of threads can scale up or down based on the need, which means it can go up if the number of asynchronous tasks are really huge or it can go down automatically when you have fewer tasks. 

***Message Driven*** 

This means the asynchronous computations interact with each other through messages in an event driven style. 

CompletableFuture API is available in Java since Java8 and it extends two interfaces. 

* Future
* CompletionStage 

All the methods that are present in CompletableFuture class can be categorized into 3 different groups. 

* Factory Methods 
* Completion Stage Methods 
* Exception Method 

***Factory Methods***

These methods are used to initiate the asynchronous computations. Basically, these are static methods through which we can initiate the asynchronous computations. 

***Completion Stage Methods*** 

These methods are used to achieve asynchronous computations where we can chain multiple asynchronous methods.

***Exception Methods*** 

These methods are primarily used to handle exceptions in CompletableFuture.


**supplyAsync and thenAccept()**

We will explore two functions that's part of the CompletableFuture API.

* supplyAsync()
* thenAccept()

***supplyAsync()***
* The supplyAsync() falls under the category of Factory Method which means it's a static method in CompletableFuture. 
* The supplyAsync() is used to initiate the asynchronous computation which fundamentally means it's going to trigger a background task and return immediately.
* The input to supplyAsync() is a Supplier functional interface.
* The supplyAsync() method returns a CompletableFuture<T>.

***thenAccept()***
* The thenAccept() is a Completion Stage Method.
* The thenAccept() is used to chain asynchronous computations which means it has a technique to access the result of the previous asynchronous computation and take some action on it.
* The input to the thenAccept() method is a Consumer functional interface which means it will accept the result of the previous computation and then take action on it.
* The thenAccept() method returns CompletableFuture<Void> which means you normally use it as a last step in your asynchronous computation to log something.

````
public class CompletableFutureHelloWorld {

  public static void main(String[] args) {
    HelloWorldService helloWorldService = new HelloWorldService();

    CompletableFuture.supplyAsync(helloWorldService::helloWorld) // runs in a common fork-join pool
        .thenAccept(result -> log("Result is: " + result));
    log("Done!");
  }
}

public class HelloWorldService {

  public String helloWorld() {
    delay(1000);
    log("inside helloWorld");
    return "hello world";
  }
}
````

When we execute the above example, the program will print "Done!" without printing "Hello World". Why is that? 

As soon as supplyAsync() is executed, the main thread is released. That's one of the reasons why we see "Done!" in the first place and the result is basically being executed in a background thread.

But if you take a look at the helloWorld() method, it is going to give us the response after a delay of one second. Even before this delay got exhausted, our main program got completed. That's one of the reason why you just see "Done!" and not anything else.

If we add a delay of 2 seconds we can block the main thread for 2 seconds and when we do that, we will be able to see the result. 

````
public class CompletableFutureHelloWorld {

  public static void main(String[] args) {
    HelloWorldService helloWorldService = new HelloWorldService();

    CompletableFuture.supplyAsync(helloWorldService::helloWorld)
        .thenAccept(result -> log("Result is: " + result))
        .join();
    log("Done!");
    delay(2000);
  }
}
````

One important thing to notice is all the things are getting executed in the Fork.Join Pool. This is a proof that supplyAsync() is executing the whole computation and thenAccept() in a totally different thread pool. That's one of the reasons why we do not see the results before we added the delay.

To programmatically access the result without even adding the delay is by using a function called join().So what this does is it blocks the caller thread until the result is completed or the whole computation is completed.

````
public class CompletableFutureHelloWorld {

  public static void main(String[] args) {
    HelloWorldService helloWorldService = new HelloWorldService();

    CompletableFuture.supplyAsync(helloWorldService::helloWorld)
        .thenAccept(result -> log("Result is: " + result))
        .join();
    log("Done!");
  }
}
````

Here, the main thread will be blocked until the program is completed calling the whole helloWorld() function, which made this call to wait for a second.

The join() is going to block the main thread which is a problem. We should never block the main thread.

**Comparing with the 4 qualities**
The concepts are responsive, resilient, elastic and message driven. The example that we can see maps to two of the four reactive principles. They are **responsive** and **message driven**.

***Number one is responsive***
The supplyAsync() function maps to the responsive nature of the reactive programming principle, which means fundamentally asynchronous. As soon as the call is made, the call returns immediately. That's the responsive nature of the code that you write with the CompletableFuture.

***Number two is the message driven aspect of the reactive programming***
If you look at this example, the data flows from supplyAsync() to thenAccept() in the form of messages. Meaning when the data is returned from this supplyAsync() it goes to the thenAccept() in a reactive fashion. Basically, which means when **helloWorldService::helloWorld** completes it goes ahead and emits an event to the thenAccept() function and then the thenAccept() function will act on it. That's the message driven aspect of reactive programming.


**Transform data from one form to another using thenApply() method**
* The thenApply() method is a Completion Stage method which means it has a capability to access the result of the previous asynchronous computation and then act on it.
* thenApply() is used to transform the data from one form to another. 
* The input to the thenApply() function is a Function functional interface. Function functional interface takes an input and then returns an output. 
* The return type of the thenApply() function is CompletableFuture<T>.

Here, we are going to apply an uppercase function and print the result.

````
public class CompletableFutureHelloWorld {

  public static void main(String[] args) {
    HelloWorldService helloWorldService = new HelloWorldService();

    CompletableFuture.supplyAsync(helloWorldService::helloWorld)
        .thenApply(String::toUpperCase)
        .thenAccept(result -> log("Result is: " + result));
    log("Done!");
    delay(2000);
  }
}
````

What would happen is that the helloWorld() function after a delay is going to emit the hello world string and in the thenApply() function we are going to apply the uppercase operation to that particular string and then we are going to pass that transformed value to the thenAccept() function and then the result would be the uppercase version of the hello world.

**Combining independent async tasks using thenCombine()**
* The thenCombine() method is a Completion Stage method.
* This is basically used to combine multiple independent CompletableFutures to produce a single output.
* To explain this using a simple example, let's say you have a client and a service. The client makes a call to the service asking for some data. Behind the scenes, the service is going to interact with two different services to provide the response to the client. The use case is that there is no dependency between the service1 and service2 call and those calls can be made parallel so that the overall latency in this case would be the max time between these two calls. That is maxOf(service1, service2). The idea is to improve the overall performance of the calls to the max time taken by either of these calls.

* thenCombine() has two arguments. The reason being we are going to deal with two different futures. The two arguments are CompletionStage and BiFunction functional interface.
* The thenCombine() returns a CompletableFuture.

**Invoking async tasks using themCompose() method**
* The thenCompose() method is a Completion Stage method.
* This is used to transform the data from one form to another form. 
* The input to then compose is a Function functional interface.
* The thenCompose() method deals with functions that return a CompletableFuture.
* If you take a look at the below method, it outputs a CompletableFuture. So if you have any function that returns a CompletableFuture and then you want to use that in your CompletableFuture pipeline then you would use thenCompose().

````
public CompletableFuture<String> worldFuture(String input) {
  return CompleteableFuture.supplyAsync(() -> {
    delay(1000);
    return input + " world!";
  })
}
````

* The difference between thenApply() and thenCompose() is that thenApply() deals with functions that return a value, not a Future.
* The thenCompose() method returns a CompletableFuture<T>.
