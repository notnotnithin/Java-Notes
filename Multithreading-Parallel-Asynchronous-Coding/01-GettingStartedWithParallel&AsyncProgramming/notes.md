**The most common requirement in software programming today is that any code that we write needs to execute and perform faster. So what are the problems that the parallel and asynchronous programming styles are trying to solve?**

In a nutshell, the goal of both of these programming styles is to provide techniques to improve and increase the performance of the code that we write. Parallel and asynchronous programming styles are fundamentally used for totally different types of use cases.

#### **What's driving these concepts to be part of Java?**
We have significant technology advancements happening in both hardware and software side.

| Hardware      | Software |
| ------------- | ------------- |
| Number one is the advancements to the modern devices that we use day to day, whether it be a mobile device or a computer. All these machines or devices come up with multiple cores.  | Architecture style that we use in software development today. Microservices architecture is very popular and pretty much most of the enterprises have already migrated to this architecture or in the process of moving to this architecture.  |
| So as a developer, there is a need to learn programming patterns to maximize the use of multiple cores for faster code execution.  | Most of these interactions are Blocking IO calls, which basically exhibits the synchronous behavior. Any time we make a blocking call, it leads to waste of resources because we are being blocked and waiting for data. This also impacts the latency and performance of the application.  |
| To bring the technology advancements and the parallel and programming styles together and in order to maximize the use of CPU cores, all we got to do is apply the parallel programming concepts. So as a developer, we have a need to learn about programming patterns that's going to maximize the use of all the available CPU cores.  | To bring the technology advancements and the parallel and programming styles together and in order to maximize the use of CPU cores, all we got to do is apply the parallel programming concepts. So as a developer, we have a need to learn about programming patterns that's going to maximize the use of all the available CPU cores.  |
| To apply the parallel programming concepts, we will be exploring the Parallel Streams API.  | To apply the parallel programming concepts, we will be exploring the Parallel Streams API.  |

**NOTE:**

There are two common themes that comes with these two APIs. 
1. First, both of these programming styles involve threads to improve the performance of the code. 
2. Second, both of these programming styles or APIs use the functional style of programming.

## **Evolution of concurrency and parallelism APIs in JAVA**

Having the knowledge about the evolution will help you to relate why the concurrency APIs in Java continue to evolve with the change in technology and software architectures around it. 

1. The very first release of Java was JDK 1.0 in 1996, had threads in it. Any experienced veteran Java developers must have experience working with that, even if we have not. Threads gave Java the foundation to step into concurrency. Historically, threads were used to offload the work from the actual thread, executing a function and run those tasks as a background or tasks. Threads are pretty low level API and the code that we write with threads can get complex very easily. It had its own limitations. 
2. Around 2004 JDK 1.5 got released. This is a time where multi-cores became popular and pretty much all the devices and machines came with multiple cores. In order to fully utilize all the cores in the CPU, the Java language came up with some really cool advanced features like

   * ThreadPool
   * ExecutorService
   * Futures
   * BlockingQueues
   * Concurrent Collections and more. 

This enabled the developers to achieve a **task based concurrency model** where the developers need not have to work with thread APIs directly anymore. The basic idea with ThreadPool is that, submit a task to the ThreadPool and then the result is available once the task is executed. ThreadPools were great, but still it had some issues like deadlock and more. From a coding perspective, the code that you write can get tedious, actually.

1. In 2011, we got JDK 1.7 released. This is the time where the data parallelism became popular and Java introduced the **Fork/Join framework**, which eventually brought in the foundation for parallelism into the Java world. Even though Fork/Join framework was great in applying data parallelism, but it's still complex to code and work directly.
2. In 2014, we had a big release for Java, which is JDK 1.8. Probably, this is one of the biggest releases in Java, which had some excellent features like lambdas, streams and more. From a concurrent feature standpoint, we had Parallel Streams and CompletableFuture as part of Java8. This is when Java stepped its foot into the functional programming side. What this allows us to do is it enables a functional parallelism support in Java. These are the two modern APIs that we will use to write the asynchronous and parallel code. 
3. In 2017, JDK 1.9 got released. This release had a newer type, which is a Flow API, which enabled a Reactive Programming in Java, which is a pub/sub style of programming. This introduced a complete shift from the way we normally write code. 
4. The current version of Java that we are using is JDK 16.

## **Concurrency vs Parallelism**
Developers normally think and understand concurrency and parallelism interchangeably, and some think both are the same.

#### **Concurrency**
1. Firstly, concurrency is a concept where two or more tasks can run simultaneously in Java. Concurrency is achieved out of the box using Threads. 
2. The definition of concurrency in itself is vague. What do we mean by that? I said two or more tasks can run simultaneously. Does it mean the tasks are running in interleaved fashion? Are whether the tasks run simultaneously at the same time? It's totally based on the underlying core that you're are trying to run this program. 
3. If you're running multiple tasks in a single core machine, then it means that the task are running in an interleaved fashion because there is only one core. CPU has a scheduler that takes care of scheduling these multiple threads to run on the core in an interleaved fashion.
4. If you are running multiple tasks in a multiple core machine, then it means the tasks are running simultaneously.
5. Let's quickly look at this using one simple example.

````
public class HelloWorldThreadExample {
  private static String result = "";

  private static void hello() {
    delay(500);
    result = result.concat("Hello");
  }

  private static void world() {
    delay(600);
    result = result.concat(" World");
  }

  public static void main(String[] args) throws InterruptedException {
    Thread helloThread = new Thread(() -> hello());
    Thread worldThread = new Thread(() -> world());

    // Starting the threads
    helloThread.start();
    worldThread.start();

    // Joining the thread (Waiting for the threads to finish)
    helloThread.join();
    worldThread.join();

    System.out.println("Result is: " + result); // OUTPUT: Hello World
  }
}
````

This is a simple Hello World example and it uses threads. As we can see here, we have two threads, a hello thread and the world thread, which invokes the hello method and the world thread respectively and then we joined the thread. Basically, this means it's going to wait until the threads complete the tasks and then the result "Hello World" will be printed.

One important thing I would like to call out is that in a real time app threads normally interact with each other to perform a computation via shared objects or messaging queues.

In this example, threads are interacting with each other via the shared variable named result. There is a popular term in the software industry where **"a shared object is the root of all evil"**. It's totally true because when you start to work on a shared object via multiple threads issues like race condition, deadlocks and a variety of many more issues can occur which results in bugs and errors in your program.

In order to solve these issues, Java have a variety of tools available. Some of the examples are synchronized statements, methods, reentrant locks, semaphores, concurrent collections and conditional objects and more. So the current state of concurrency is about making sure the shared resources are accessed correctly and efficiently. That's all about concurrency.

#### Parallelism:
1. Now let's switch gears and talk about parallelism. Parallelism is a concept in which two or more tasks are literally going to run in parallel. This is one of the important difference between concurrency and parallelism.

2. Parallelism involves these following steps. 
   1. Decomposing the tasks into multiple subtasks which is also known as **"Forking"**.
   2. Executing these tasks in sequential. 
   3. "Joining" the results of these individual tasks into a single result.

This whole process is also known as Fork/Join.

3. In Fork/Join, we have the task at the top. The first step of parallelism is to split the tasks into multiple subtasks. The tasks need to be broken down to a size where it cannot be split further. This whole process is called Forking and then execute the tasks. Once the tasks are executed, then we need to combine the tasks to produce the final result. This whole process is called **Fork/Join**. This whole concept got introduced as part of the JDK 1.7.

4. Let's quickly look at parallelism using a simple example.

The use case that we have here is to transform the string to uppercase, so we have a list of names - Bob, Jamie, Jill and Rick, and we are going to convert the names to uppercase string using the parallelism mechanism. The input here is a lowercase and the output here is the uppercase and this is the expected result.

5. The very first step is the fork step, meaning splitting the structure into its least possible size. Here, the data structure that we have is a list, so the least possible size is a data element of size one. At the end of step one, we have the list split into individual elements.

6. The second step is actually processing the elements sequentially. So here each and every element is processed sequentially. The processing logic is nothing but transforming the element to uppercase. So as a result of the second step, the data is converted to uppercase.

7. The third step is the join phase where we will combine the results step by step to produce the final result. In a nutshell, this is how the parallelism works.

## What happens behind the scenes from the execution standpoint?
Here, we have the list split into individual elements. Assume we have multiple cores in the machine, for example, 4 cores. The next step would be to execute the uppercase operation sequentially to produce the result given here. 

From a developer point of view, you might be wondering, how can we fork and join the list that's used in this example?

Let's quickly look at the code that's used in this example.

Here is a code that performs uppercase operation for the list of string.

````
public static List<String> getNamesList() {
  List<String> namesList = List.of("Bob", "Jamie", "Jill", "Rick");
  List<String> namesListUppercase = namesList.parallelStream()
    .map(String::toUpperCase)
    .collect(Collectors.toList());
  return namesListUppercase;
}
````

Here, we have used Streams API in this example. All you got to do to enable Fork/Join operation is you just have to invoke the parallelStream() operation. It takes care of splitting the data structure and run the uppercase operation and perform the join operation. It is as simple as that. This is the beauty of functional programming and all the modern APIs that are introduced in Java recently.

## **Summarizing concurrency and parallelism**
#### **Concurrency**
* Concurrency is a concept where two or more tasks can run simultaneously based on the number of cores that you have in your machine.
* Concurrency can be implemented in a single or multicore machines.
* Concurrency is about correctly and efficiently controlling the access to shared resources.

#### **Parallelism**
* Parallelism is a concept where two or more tasks are literally running simultaneously.
* Parallelism can only be implemented in multicore machine.
* Parallelism is about using more resources to get the answer faster.

## **Threads and its limitations**
1. Threads got released as part of JDK 1, and it's still part of the current version of Java.
2. Threads are normally used to offload blocking tasks as background tasks. Basically, threads allowed the developers to write asynchronous style of code.

```
public class ProductServiceUsingThread {
  private ProductInfoService productInfoService;
  private ReviewService reviewService;

  public ProductServiceUsingThread(
      ProductInfoService productInfoService, ReviewService reviewService) {
    this.productInfoService = productInfoService;
    this.reviewService = reviewService;
  }

  public static void main(String[] args) throws InterruptedException {

    ProductInfoService productInfoService = new ProductInfoService();
    ReviewService reviewService = new ReviewService();
    ProductServiceUsingThread productService =
        new ProductServiceUsingThread(productInfoService, reviewService);
    String productId = "ABC123";
    Product product = productService.retrieveProductDetails(productId);
    log("Product is " + product);
  }

  public Product retrieveProductDetails(String productId) throws InterruptedException {
    stopWatch.start();

    ProductInfoRunnable productInfoRunnable = new ProductInfoRunnable(productId);
    Thread productInfoThread = new Thread(productInfoRunnable);

    ProductReviewRunnable productReviewRunnable = new ProductReviewRunnable(productId);
    Thread productReviewThread = new Thread(productReviewRunnable);

    productInfoThread.start();
    productReviewThread.start();

    productInfoThread.join();
    productReviewThread.join();

    ProductInfo productInfo = productInfoRunnable.getProductInfo();
    Review review = productReviewRunnable.getReview();

    stopWatch.stop();
    log("Total Time Taken : " + stopWatch.getTime());
    return new Product(productId, productInfo, review);
  }

  private class ProductInfoRunnable implements Runnable {
    private ProductInfo productInfo;
    private String productId;

    public ProductInfoRunnable(String productId) {
      this.productId = productId;
    }

    public ProductInfo getProductInfo() {
      return productInfo;
    }

    @Override
    public void run() {
      productInfo = productInfoService.retrieveProductInfo(productId);
    }
  }

  private class ProductReviewRunnable implements Runnable {

    private Review review;
    private String productId;

    public ProductReviewRunnable(String productId) {
      this.productId = productId;
    }

    public Review getReview() {
      return review;
    }

    @Override
    public void run() {
      review = reviewService.retrieveReviews(productId);
    }
  }
}
```

## **Limitations of using low level threads**
1. The problem with the Runnable implementation is that it is verbose and it is a lot of code. We have written a lot of code in order to achieve this performance and you need to have an instance of a thread and runnable for every asynchronous operation that you have to perform.

2. Also, Runnable interface doesn't take any input and doesn't return any output and for that specific purpose we need to introduce some additional properties or parameters to that particular class.

3. The next thing is we have to manually start the thread and we have to manually join the threads. It is pretty evident that threads are a very low level API. It is very easy to introduce complexity in our code when trying to implement the asynchronous operations using threads. So, we can clearly say that threads are not developer friendly.

4. Threads in general are expensive reason being threads have their own runtime-stack, memory and registers and more. This is primarily the reason why creating and destroying the thread is an expensive process and to solve this problem primarily, the ThreadPool was created.

## **Introduction to ThreadPool, ExecutorService and Future**
**ThreadPool** You can think of ThreadPool primarily as a group of threads that are created and readily available to handle any work submitted to them. The ThreadPool typically will have a work queue and it pulls a new work from that queue.

There are a couple of things you need to know. If we have any CPU intensive task, basically some complex mathematical calculation or any algorithm related computation, then the number of threads should be equal to the number of cores in your machine. Because at any given time, the number of tasks that can run in parallel is equal to the number of cores.

If you have IO intensive tasks such as a database call or a REST API call, then your ThreadPool size should be greater than the number of cores in your machine. The reason for this is because the thread executing the task will be waiting for the most part. That's one of the reasons why we need to have more threads than the number of cores.

## **Benefits of a ThreadPool**
With ThreadPool, we don't have to manually create, start or join threads. This is a big win as a developer. This also helps in achieving concurrency for your application. Basically, with four threads, you can have four tasks that can run in parallel. So that's all about ThreadPool, but we cannot do much by having ThreadPool alone because we need tasks in order for these threads to execute.

#### **ExecutorService**
ExecutorService got released as part of a JDK 1.5. If I have to explain about ExecutorService in a single statement, then ExecutorService is an **Asynchronous Task Execution Engine**.

This provides a way to asynchronously execute the tasks and provide the result in a much simpler way compared to threads. ExecutorService also enabled coarse-grained task based parallelism in Java.

#### **Different components of ExecutorService**
Inside the ExecutorService, we have the

1. **ThreadPool**
2. **Work Queue** - A blocking queue. Any time the client submits a task for the ExecutorService, the task will be placed in the work queue.
3. **Completion Queue** - Where the tasks that are completed by the threads in a ThreadPool will be placed in the completion queue.

In a nutshell, these are the components that are part of the ExecutorService.

## **How does all these components work together to get the work done?**

When a client (nothing but our code interacting with the ExecutorService) submits a task, it gets placed in the **Work Queue**. As soon as the work is placed in the work queue, in return you would get a **Future**. Think of Future as **a proxy or a reference to the result that will be available later**. Once the task is available in the queue, one of the threads from the ThreadPool is going to take that work and execute the task and place the result in the completion queue and then the client reads the result from it using the Future reference. This is how the ExecutorService, ThreadPool and the Future work together to provide task based parallelism in Java.

````
public class ProductServiceUsingExecutor {
  static ExecutorService executorService =
      Executors.newFixedThreadPool(Runtime.getRuntime().availableProcessors());
  private ProductInfoService productInfoService;
  private ReviewService reviewService;

  public ProductServiceUsingExecutor(
      ProductInfoService productInfoService, ReviewService reviewService) {
    this.productInfoService = productInfoService;
    this.reviewService = reviewService;
  }

  public static void main(String[] args) throws ExecutionException, InterruptedException, TimeoutException {

    ProductInfoService productInfoService = new ProductInfoService();
    ReviewService reviewService = new ReviewService();
    ProductServiceUsingExecutor productService =
        new ProductServiceUsingExecutor(productInfoService, reviewService);
    String productId = "ABC123";
    Product product = productService.retrieveProductDetails(productId);
    log("Product is " + product);
  }

  public Product retrieveProductDetails(String productId)
      throws ExecutionException, InterruptedException, TimeoutException {
    stopWatch.start();

    Future<ProductInfo> productInfoFuture =
        executorService.submit(() -> productInfoService.retrieveProductInfo(productId));
    Future<Review> reviewFuture = executorService.submit(() -> reviewService.retrieveReviews(productId));

    // ProductInfo productInfo = productInfoFuture.get();
    ProductInfo productInfo = productInfoFuture.get(2, TimeUnit.SECONDS);
    Review review = reviewFuture.get();

    stopWatch.stop();
    log("Total Time Taken : " + stopWatch.getTime());
    return new Product(productId, productInfo, review);
  }
}
````

The primary difference is that we should accept the fact that the above code is much better compared to the thread code because in the thread code, we are directly creating the thread instance and we are directly starting the thread and joining the thread. It requires a lot of code in order to offload the work as an asynchronous task and that's not the case with ExecutorService. So, in a nutshell you just submit the task to an ExecutorService, it takes care of executing the task for you, once the task is complete it saves the completed tasks in a completed queue and using the Future reference we get back the results.

## **Limitations of ExecutorService**
1. Now, ExecutorService is designed to block the threads. The primary reason for this is we are making the get() call. What does get() call going to do is it is going to block the caller. That's one of the reasons why Future is designed to block. But we do have an option of timeout. In that case, you will be timing out the task, but ultimately you won't be getting that result.

2. The next limitation is that there is no better way to combine the Future. Here we have 2 futures, but the only way to retrieve the result and build the object is making the get() call. Again your code is going to block the caller thread. The second get() call will be blocked until the first get() call is returned. So that's again, another problem.

But with the recent functional programming advancements in the Java programming language, this API is still behind in adapting to the functional style of writing the code.

**HINT**: CompletableFuture is an extension of Future.

## **Fork/Join Framework**
Fork Join Framework, is the next new enhancement that's related to the Java Concurrency and Parallelism APIs. 

It's important to have the understanding of Fork/Join Framework because the parallel streams API uses this framework behind the scenes. This got released as part of JDK 1.7.

Fork/Join framework extends the ExecutorService. The primary difference between the Fork/Join framework and the ExecutorService is that the **Fork/Join framework is designed for Data Parallelism** and the **ExecutorService is designed for Task Based Parallelism**.

#### **What does that mean?**
If you recall this code, 

````
Future<ProductInfo> productInfoFuture =
    executorService.submit(() -> productInfoService.retrieveProductInfo(productId));

Future<Review> reviewFuture = executorService.submit(() -> reviewService.retrieveReviews(productId));
````

The tasks that we are submitting here is the method invocation itself and this whole method gets executed inside the ExecutorService by the thread in the ThreadPool and the output will be returned as part of the Future.

#### **What is data parallelism?**
Data Parallelism is a concept where a given task is recursively split into subtasks to its least possible size and run those tasks in parallel. Basically, the Fork/Join framework uses that divide and conquer approach in order to improve the overall performance of the code.

#### **How does this Fork/Join framework work?**
Fork/Join framework has a dedicated pool named **Fork/Join pool** to support parallelism.

#### **The internals of a Fork/Join pool**
1. Fork/Join Pool has a **Shared Work Queue** to which the clients submit the task. The next thing in the Fork/Join pool is the **Worker Threads**. Let's assume there are 4 threads - T1, T2, T3 and T4 and each thread has a **Double-Ended Work Queue** which is also named as **Deck** in Java.
2. Client submits a ForkJoinTask to the shared work queue because the Fork/Join pool can only work or accept ForkJoinTask because a ForkJoinTask is different from a regular task as it has the ability to split the task into subtask and join them up for execution.
3. Now the task is submitted into the shared work queue and each and every thread in the double-ended work queue continuously poll the shared work queue for new tasks and the task gets taken by one of the thread that's available in the shared work queue. Assume, T1 thread picks up the task and it gets placed under T1 double-ended work queue.
4. The task in the deck are always processed in the LIFO order. The reason being, it improves the locality and cache performance for the execution of these tasks. If the task can still be further divided into subtask, then it will be divided into subtask and placed in the same work queue. 
5. Assume, if in our pool of 4 threads all the tasks piled up in T1's work queue alone for some reason. This is definitely not efficient. This is where a new concept named **Work Stealing** comes into picture. This feature makes the Fork/Join framework a standout compared to the others. 
6. Work Stealing is a concept where the other threads in the pool check each other's work queue for tasks. In our assumption, the T1 work queue is filled with tasks. So all the other threads are going to steal the work from the other end of the queue such that the actual work that's being executed by the thread T1 is not impacted. This is one of the reason we have a double-ended queue. Once the tasks are executed, the results will be shared to the client. 

As a whole, this is how the Fork/Join pool works. The underlying working of Fork/Join framework is really complex compared to the ExecutorService.

## **ForkJoinTask**
#### **What is a ForkJoinTask?**

A ForkJoinTask represents part of the data and its computation.

In our example, a fine-grained ForkJoinTask would be the individual elements of the list along with its computation which is the uppercase operation. Here, ***Bob, Jamie, Jill and Rick*** will be split into 4 different ForkJoinTask and it's computation, which is the uppercase operation.

From a developer perspective, we need to write code that's going to perform this splitting operation into individual elements and perform the uppercase operation and join the result. If we want to work with Fork/Join pool, then you can only submit the ForkJoinTask. 

#### **What are the types of tasks that we can submit?**

1. **ForkJoinTask:**
   This class is specifically created to work with the Fork/Join pool. In reality, the application normally does not work with ForkJoinTask class. This is mostly used by the Java creators for internal purposes. There are two separate classes which are normally used by the developers to interact with the Fork/Join pool. 

2. **RecursiveTask:** 
   This is a class which is specifically used in scenarios where the task is going to return a value. In our example, we pass in a name and it performs the uppercase operation and returns the result. In this case, we would use the RecursiveTask. 
   
3. **RecursiveAction:** 
   This is a type of class which is basically used when you don't have anything to return but still you would like to apply the data parallelism on it. In a nutshell, this is how you will choose between the RecursiveTask and RecursiveAction.

````
public class ForkJoinUsingRecursion extends RecursiveTask<List<String>> {

  private List<String> inputList;

  public ForkJoinUsingRecursion(List<String> inputList) {
    this.inputList = inputList;
  }

  public static void main(String[] args) {

    stopWatch.start();
    List<String> resultList;
    List<String> names = DataSet.namesList();
    ForkJoinPool forkJoinPool = new ForkJoinPool();
    ForkJoinUsingRecursion forkJoinUsingRecursion = new ForkJoinUsingRecursion(names);
    resultList = forkJoinPool.invoke(forkJoinUsingRecursion);

    stopWatch.stop();
    log("Final Result : " + resultList);
    log("Total Time Taken : " + stopWatch.getTime());
  }

  private static String addNameLengthTransform(String name) {
    delay(500);
    return name.length() + " - " + name;
  }

  @Override
  protected List<String> compute() {
    if (inputList.size() <= 1) {
      List<String> resultList = new ArrayList<>();
      inputList.forEach(name -> resultList.add(addNameLengthTransform(name)));
      return resultList;
    }
    int midPoint = inputList.size() / 2;
    ForkJoinTask<List<String>> leftInputList =
        new ForkJoinUsingRecursion(inputList.subList(0, midPoint)).fork();
    inputList = inputList.subList(midPoint, inputList.size());
    List<String> rightResult = compute();// recursion
    List<String> leftResult = leftInputList.join();
    leftResult.addAll(rightResult);
    return leftResult;
  }
}
````

#### **Drawbacks of Fork/Join framework**
First and foremost, we should agree to the fact that whatever code we have written above is really complex and it is not developer friendly. But this is what we had in JDK 1.7 before Streams API was released as part of JDK 1.8 for data parallelism.