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

**Exception Handling in CompletableFuture**

Let's see the techniques that are available in CompletableFuture to handle them. Let's talk about the traditional exception handling in Java first. 

***Traditional Approach:***

* Exception handling in Java is available since the inception of Java. 
* Traditionally, exception handling is done by using the try-catch block. So you'll have the actual code statements inside the try block and the exception handling code inside the catch block.

***Using CompletableFuture***

* With CompletableFuture, you have different options available for exception handling. As we all know by now, CompletableFuture is a functional style API. 
* Here is an example code.

````
public String helloWorldMultipleAsyncCalls() {
  startTimer();
  CompletableFuture<String> hello =
      CompletableFuture.supplyAsync(() -> helloWorldService.hello());
  CompletableFuture<String> world =
      CompletableFuture.supplyAsync(() -> helloWorldService.world());
  CompletableFuture<String> hiCompletableFuture =
      CompletableFuture.supplyAsync(
          () -> {
            delay(1000);
            return " Hi CompletableFuture!";
          });

  // first argument represents the result of hello.
  // second argument represents the result of world.
  // join() will block the main thread and returns the result as a string.
  String helloWorld =
      hello
          .thenCombine(world, (h, w) -> h + w)
          .thenCombine(hiCompletableFuture, (prev, curr) -> prev + curr)
          .thenApply(String::toUpperCase)
          .join();
  timeTaken();
  return helloWorld;
}
````

If you closely look at this code, there are multiple failure points as we are dealing with independent service calls and this example can be wrapped in a traditional try-catch block. The code would look something like this. 

````
public String helloWorldMultipleAsyncCalls() {
  try {
    startTimer();
    CompletableFuture<String> hello =
        CompletableFuture.supplyAsync(() -> helloWorldService.hello());
    CompletableFuture<String> world =
        CompletableFuture.supplyAsync(() -> helloWorldService.world());
    CompletableFuture<String> hiCompletableFuture =
        CompletableFuture.supplyAsync(
            () -> {
              delay(1000);
              return " Hi CompletableFuture!";
            });

    // first argument represents the result of hello.
    // second argument represents the result of world.
    // join() will block the main thread and returns the result as a string.
    String helloWorld =
        hello
            .thenCombine(world, (h, w) -> h + w)
            .thenCombine(hiCompletableFuture, (prev, curr) -> prev + curr)
            .thenApply(String::toUpperCase)
            .join();
    timeTaken();
    return helloWorld;
  } catch (Exception e) {
    log("Exception is: " + e);
    throw e;
  }
}
````

But in this case, if any of the service calls fail, in that case, the whole transaction will fail and this will throw an exception to the caller. Nothing fancy here and this implementation is pretty straightforward. Is there any other approach in CompletableFuture to handle exceptions in the pipeline? The answer is, YES.
* CompletableFuture has a functional style of handling exceptions and we can use this in your CompletableFuture pipeline. 
* These are the 3 options that we have when it comes to handling exceptions using CompletableFuture. 
  * handle()
  * exceptionally() 
  * whenComplete()
You have 3 different pipeline methods that are available with CompletableFuture in order to handle exceptions but each of these functions work a little different. 
* These three functional style of handling exceptions can be categorized into two types. The first two options (handle() and exceptionally()) have the capability to catch the exception and recover, which means you can catch the exception and provide a recoverable value instead of just throwing the exceptions in the runtime stack. The third option has the capability to catch the exception, but the key difference is that it doesn't recover from the exception, which means it catches the exception, but still it throws exception to the caller. 

So these are the different options that we have when it comes to handling exceptions in CompletableFuture.

````
public String helloWorldMultipleAsyncCallsHandle() {
    startTimer();
    CompletableFuture<String> hello =
        CompletableFuture.supplyAsync(() -> helloWorldService.hello());
    CompletableFuture<String> world =
        CompletableFuture.supplyAsync(() -> helloWorldService.world());
    CompletableFuture<String> hiCompletableFuture =
        CompletableFuture.supplyAsync(
            () -> {
              delay(1000);
              return " Hi CompletableFuture!";
            });

    // first argument represents the result of hello.
    // second argument represents the result of world.
    // join() will block the main thread and returns the result as a string.
    String helloWorld =
        hello
            .handle((res, ex) -> {
              log("Exception is: " + ex.getMessage());
              return ""; // recoverable value in case of an exception
            })
            .thenCombine(world, (h, w) -> h + w)
            .thenCombine(hiCompletableFuture, (prev, curr) -> prev + curr)
            .thenApply(String::toUpperCase)
            .join();
    timeTaken();
    return helloWorld;
  }
````

* The handle() method gets called for both negative and positive scenarios and hence it is required to null check for any exception as shown below.

````
public String helloWorldMultipleAsyncCallsHandlePositiveScenario() {
  startTimer();
  CompletableFuture<String> hello =
      CompletableFuture.supplyAsync(() -> helloWorldService.hello());
  CompletableFuture<String> world =
      CompletableFuture.supplyAsync(() -> helloWorldService.world());
  CompletableFuture<String> hiCompletableFuture =
      CompletableFuture.supplyAsync(
          () -> {
            delay(1000);
            return " Hi CompletableFuture!";
          });

  // first argument represents the result of hello.
  // second argument represents the result of world.
  // join() will block the main thread and returns the result as a string.
  String helloWorld =
      hello
          .handle((res, ex) -> {
            log("Result is: " + res);
            if (ex != null) {
              log("Exception is: " + ex.getMessage());
              return ""; // recoverable value in case of an exception
            } else {
              return res;
            }
          })
          .thenCombine(world, (h, w) -> h + w)
          .thenCombine(hiCompletableFuture, (prev, curr) -> prev + curr)
          .thenApply(String::toUpperCase)
          .join();
  timeTaken();
  return helloWorld;
}
````

**Handle/Recover exceptions using "exceptionally()" function**
* The exceptionally() function takes a function as an input, which means it's going to just take one input and it's going to give you one output. That's not the case with handle() function. the handle() function takes a BiFunction and that's one of the reason why we were able to access both the result and the exception. In the case of exceptionally, it's going to be just the exception. So this is how our exceptionally block will look like. 

````
public String helloWorldMultipleAsyncCallsExceptionally() {
  startTimer();
  CompletableFuture<String> hello =
      CompletableFuture.supplyAsync(() -> helloWorldService.hello());
  CompletableFuture<String> world =
      CompletableFuture.supplyAsync(() -> helloWorldService.world());
  CompletableFuture<String> hiCompletableFuture =
      CompletableFuture.supplyAsync(
        () -> {
          delay(1000);
          return " Hi CompletableFuture!";
        });

  // first argument represents the result of hello.
  // second argument represents the result of world.
  // join() will block the main thread and returns the result as a string.
  String helloWorld =
      hello
        .exceptionally(ex -> {
          log("Exception is: " + ex.getMessage());
          return ""; // recoverable value in case of an exception
        })
        .thenCombine(world, (h, w) -> h + w)
        .exceptionally(ex -> {
          log("Exception after world is: " + ex.getMessage());
          return ""; // recoverable value in case of an exception
        })
        .thenCombine(hiCompletableFuture, (prev, curr) -> prev + curr)
        .thenApply(String::toUpperCase)
        .join();
  timeTaken();
  return helloWorld;
}
````

* The exceptionally() is one of the best way of exception handling because you don't have to have the additional logic that we had with handle() function where you need to check the exception for null and then explicitly, handle or provide a recovery for that exception if it's available, if not return the actual value. You don't need to deal with any of those things when it comes to exceptionally(). and the exceptionally() block itself is compact because we are just dealing with exceptions here. The nicest thing about exceptionally() is that it would only get invoked if there is an exception. Otherwise, even though it's part of the pipeline, it will be skipped because there are no exceptions. 
* In the success path, the key difference is that we just have the Completion Stage Functions not the exceptionally(). In the failure path, we have the exceptionally() functions which is going to be involved when there is an exception. 
* So any exception that happens in the success path is going to be forwarded to the failure path. In this case, it's the exceptionally() function. So the exceptionally() function is going to receive the exception and then it is going to provide a recoverable value and put the execution of the CompletableFuture back into the success path and continues the execution of the pipeline. This is equivalent to the try-catch way of handling the exceptions because the catch block will come into the picture only when there is an exception. This is almost similar to that but the syntax is compact and it's the functional way of handling the exceptions.

**Handling exceptions using whenComplete() function**
* This is an exception handler that's part of the CompletableFuture API. 
* This function catches the exception, but it doesn't have the capability to recover from an exception. In the success path, you have the completion stage functions and on top of it, the whenComplete() function is also part of the success path. This behavior is almost similar to the handle() function. The whenComplete() is also part of the failure part, which is really obvious. 
* Now, let's say when you have an exception that's happening in the success path and then the exception will be handed over to the whenComplete() function, which is in the failure path, but the key difference is that the whenComplete() exception handler doesn't have the capability to recover from an exception. So in this case, the exception is going to propagate to the next exception handler and there is no way for this exception handler to put this execution back to the success path. So in this case, it's going to throw the exception back to the caller, which means there is no way for this exception handler to recover from that exception. This is one of the least used exception handler in CompletableFuture API.

````
public String helloWorldMultipleAsyncCallsWhenComplete() {
  startTimer();
  CompletableFuture<String> hello =
      CompletableFuture.supplyAsync(() -> helloWorldService.hello());
  CompletableFuture<String> world =
      CompletableFuture.supplyAsync(() -> helloWorldService.world());
  CompletableFuture<String> hiCompletableFuture =
      CompletableFuture.supplyAsync(
          () -> {
            delay(1000);
            return " Hi CompletableFuture!";
          });

  // first argument represents the result of hello.
  // second argument represents the result of world.
  // join() will block the main thread and returns the result as a string.
  String helloWorld =
      hello
          .whenComplete((res, ex) -> {
            if (ex != null) {
              log("Exception is: " + ex.getMessage());
            }
          })
          .thenCombine(world, (h, w) -> h + w)
          .whenComplete((res, ex) -> {
            if (ex != null) {
              log("Exception after world is: " + ex.getMessage());
            }
          })
          .thenCombine(hiCompletableFuture, (prev, curr) -> prev + curr)
          .thenApply(String::toUpperCase)
          .join();
  timeTaken();
  return helloWorld;
}
````
