---
layout: post
title: Java Async : Part 1
subtitle: For us procrastinators!
tags: [tech]
comments: true
mathjax: true
author: Utsav
---

_This article is Part 1 of a series of articles about Async Programming in Java. More of personal notes but would recommend to go through these things in depth on your own_

Java is an OLD language. Every new Java release has taken its own sweet time.

- **[Java SE 8](https://www.google.com/search?rlz=1C5OZZY_enIN1136IN1136&cs=1&sca_esv=41cbd256792e7ece&sxsrf=AE3TifP5Sc3_rNtqXpi15tk3rnyYqxpfog%3A1749307929618&q=Java+SE+8&sa=X&ved=2ahUKEwig6NHtx9-NAxUpwTgGHUd-NxIQxccNegQIBhAB&mstk=AUtExfBFeMWQgBQk2hG0quTRDAfXbzkwQ7zHHsap8JDLMF9x0R_c7OruhJ6DEPSVL5AGvXBgmrh496A5xHa5AeZmBvOl01kVCxSmixmpQwm4lvbrIsTAfdBxjy10joJ4ckv_1ryN5QeXz6M_sBRNm-iKnzFVraiMa5M8qXaK41dhXc0q65Q&csui=3):** Officially released in March 2011. Extended support for commercial users was available until December 2030. 
- **[Java SE 11](https://www.google.com/search?rlz=1C5OZZY_enIN1136IN1136&cs=1&sca_esv=41cbd256792e7ece&sxsrf=AE3TifP5Sc3_rNtqXpi15tk3rnyYqxpfog%3A1749307929618&q=Java+SE+11&sa=X&ved=2ahUKEwig6NHtx9-NAxUpwTgGHUd-NxIQxccNegQIChAB&mstk=AUtExfBFeMWQgBQk2hG0quTRDAfXbzkwQ7zHHsap8JDLMF9x0R_c7OruhJ6DEPSVL5AGvXBgmrh496A5xHa5AeZmBvOl01kVCxSmixmpQwm4lvbrIsTAfdBxjy10joJ4ckv_1ryN5QeXz6M_sBRNm-iKnzFVraiMa5M8qXaK41dhXc0q65Q&csui=3):** Released in September 2018. 
- **[Java SE 17](https://www.google.com/search?rlz=1C5OZZY_enIN1136IN1136&cs=1&sca_esv=41cbd256792e7ece&sxsrf=AE3TifP5Sc3_rNtqXpi15tk3rnyYqxpfog%3A1749307929618&q=Java+SE+17&sa=X&ved=2ahUKEwig6NHtx9-NAxUpwTgGHUd-NxIQxccNegQICRAB&mstk=AUtExfBFeMWQgBQk2hG0quTRDAfXbzkwQ7zHHsap8JDLMF9x0R_c7OruhJ6DEPSVL5AGvXBgmrh496A5xHa5AeZmBvOl01kVCxSmixmpQwm4lvbrIsTAfdBxjy10joJ4ckv_1ryN5QeXz6M_sBRNm-iKnzFVraiMa5M8qXaK41dhXc0q65Q&csui=3):** Released in September 2021. 
- **[Java SE 21](https://www.google.com/search?rlz=1C5OZZY_enIN1136IN1136&cs=1&sca_esv=41cbd256792e7ece&sxsrf=AE3TifP5Sc3_rNtqXpi15tk3rnyYqxpfog%3A1749307929618&q=Java+SE+21&sa=X&ved=2ahUKEwig6NHtx9-NAxUpwTgGHUd-NxIQxccNegQICBAB&mstk=AUtExfBFeMWQgBQk2hG0quTRDAfXbzkwQ7zHHsap8JDLMF9x0R_c7OruhJ6DEPSVL5AGvXBgmrh496A5xHa5AeZmBvOl01kVCxSmixmpQwm4lvbrIsTAfdBxjy10joJ4ckv_1ryN5QeXz6M_sBRNm-iKnzFVraiMa5M8qXaK41dhXc0q65Q&csui=3):** Released in September 2023. 
- **[Java SE 25](https://www.google.com/search?rlz=1C5OZZY_enIN1136IN1136&cs=1&sca_esv=41cbd256792e7ece&sxsrf=AE3TifP5Sc3_rNtqXpi15tk3rnyYqxpfog%3A1749307929618&q=Java+SE+25&sa=X&ved=2ahUKEwig6NHtx9-NAxUpwTgGHUd-NxIQxccNegQIBxAB&mstk=AUtExfBFeMWQgBQk2hG0quTRDAfXbzkwQ7zHHsap8JDLMF9x0R_c7OruhJ6DEPSVL5AGvXBgmrh496A5xHa5AeZmBvOl01kVCxSmixmpQwm4lvbrIsTAfdBxjy10joJ4ckv_1ryN5QeXz6M_sBRNm-iKnzFVraiMa5M8qXaK41dhXc0q65Q&csui=3):** Planned as the next LTS release in September 2025.

JDK 1.0 was released in 1996. Ooooof

Now, dont get me wrong. Java solves a lot of things. And yet, it is no secret that it is one of the most verbose languages on the internet. I will be covering Java basics in another blog post. This particular post is going to be about Async programming - the bad boy of Java. 

**Why is Async Programming such a big deal?**
Every language has support for async calls. This becomes important for 
- To process requests faster
- To efficiently use the resources of the host machine

### Future interface in Java 5
Java introduced Future interface in Java 5. Before Java 5, we had to do 

- Step 1: create a thread
- Step 2: run thread
- Step 3: Poll or Wait for the result

Major cons: No exception handling, No cancellation

```
class Task implements Runnable {
    private int result;

    public void run() {
        result = computeSomething();
    }

    public int getResult() {
        return result;
    }
}
```

**Enter** : 
- `ExecutorService`: 
- `Callable`
- `Future`

`Future` features
- Get result using `get()`
- Check if its done using `isDone()`
- Cancel is  using `.cancel()`
- Timeouts were introduced 

```

ExecutorService executor = Executors.newSingleThreadExecutor();

Callable<Integer> task = () -> {
    Thread.sleep(2000); // simulate work
    return 42;
};

Future<Integer> future = executor.submit(task);

// Do other things...

Integer result = future.get(); // blocks until result is ready
System.out.println("Result: " + result);

executor.shutdown();
```

### Completable Future
If Future was so revolutionary, then why was CompletableFuture introduced in Java8? Future had certain limitations. 

| Problem with `Future`           | Explanation                                                                               |
| ------------------------------- | ----------------------------------------------------------------------------------------- |
| **Blocking calls**              | `future.get()` blocks the thread until the result is ready.                               |
| **No chaining**                 | You couldn’t say “when this finishes, do that next.”                                      |
| **No built-in async callbacks** | You had to manually check if the task was done.                                           |
| **No easy exception handling**  | Handling errors was cumbersome.                                                           |
| **Poor composition**            | Combining multiple futures (like running tasks in parallel and waiting for all) was hard. |

CompletableFuture comes in the picture with the tagline : Fear me O plebs - I am a functional style, non-blocking feature, not seen before in Java. 

1. Non blocking calls 
2. Async execution
3. Chaining tasks
4. Combining multiple futures
5. Exception handling


CompletableFuture has about 50 different methods for composing, combining and executing asynchronous computation and handling errors.

> Static methods like `runAsync` and `supplyAsync` allow us to create a CompletableFuture instance out of `Runnable` and `Supplier` functional types respectively

_Runnable_ and _Supplier_ are functional interfaces that allow passing their instances as lambda expressions thanks to the new Java 8 feature.

#### Processing results of Async execution

- **`thenApply()`** – Transforms the result of a future **synchronously**, like mapping `T → R`.  
    _“When done, turn the result into something else.”_
    
- **`thenCompose()`** – Flattens and chains dependent futures where the next step returns another future.  
    _“When done, trigger another async task that depends on this result.”_
    
- **`thenRun()`** – Runs a **Runnable** when the future completes, but ignores its result.  
    _“When done, just do something — no result needed.”_

#### Combining Futures
- `thenCompose()` - Very similar to FlatMap. Receives a function that returns another object of the same type. 
- `thenCombine()` - If we want to execute two CompletableFuture independently, we can use thenCombine to combine the result and take an action on both outputs. 
- `thenAcceptBoth()` - Same as thenCombine but we dont need to pass any value down the chain. 

#### Running futures in parallel

`CompletableFuture.allIOf(future1, future2, future3)`
Static method allows to wait for completion of all futures provided as a var-arg. 

Limitation: This method cant return the combined result of the futures. 

Solution: `CompletableFuture.join()` through Java 8 Streams API

```
String combined = Stream.of(future1, future2, future3)
  .map(CompletableFuture::join)
  .collect(Collectors.joining(" "));

assertEquals("Hello Beautiful World", combined);
```

### Real World Scenarios

**Scenario1: Get a userId by UserName using API1. User userId to fetch UserDetails through API2**

-  Call the first API asynchronously.
- Once the userId is available, call the second API, also asynchronously.
- Then print the result.

```
getUserId("utsav")
    .thenCompose(userId -> getUserDetails(userId))
    .thenAccept(details -> System.out.println("User Details: " + details))
    .exceptionally(ex -> {
        System.out.println("Failed: " + ex.getMessage());
        return null;
    });

static CompletableFuture<String> getUserId(String username) {
        return CompletableFuture.supplyAsync(() -> {
            sleep(1); //simuating API call delay
            System.out.println("Fetched userId for " + username);
            return "user-123";
        });
    }

static CompletableFuture<String> getUserDetails(String userId) {
	return CompletableFuture.supplyAsync(() -> {
		sleep(2); //simuating API call delay
		System.out.println("Fetched details for " + userId);
		return "{ name: 'Utsav', id: '" + userId + "' }";
	});
}
```

**Scenario 2: Fetch Multiple APIs in Parallel and Combine the Results**

```
CompletableFuture<String> profileFuture = fetchUserProfile("user-123");
CompletableFuture<String> ordersFuture = fetchOrderHistory("user-123");

CompletableFuture<String> dashboard = profileFuture.thenCombine(ordersFuture, (profile, orders) -> {
    return "Dashboard:\n" + profile + "\nOrders:\n" + orders;
});

dashboard.thenAccept(System.out::println);

```

**Scenario 3: Wait for Multiple Tasks to Complete (Bulk APIs)**
```
List<String> productIds = List.of("p1", "p2", "p3");

List<CompletableFuture<String>> priceFutures = productIds.stream()
    .map(Api::getPriceForProduct)
    .collect(Collectors.toList());

CompletableFuture<Void> allDone = CompletableFuture.allOf(priceFutures.toArray(new CompletableFuture[0]));

CompletableFuture<List<String>> allPrices = allDone.thenApply(v ->
    priceFutures.stream().map(CompletableFuture::join).collect(Collectors.toList())
);

allPrices.thenAccept(prices -> prices.forEach(System.out::println));

```

Important points
- `allOf` static method will make all the CompletableFuture in parallel.
- allDone CompletableFuture only completes when _all the futures inside the list are complete_.
- By the time we are inside `thenApply()`,  all individual futures are completed and hence `CompletableFuture::join` is safe to do, it wont block anything. 
- In the `thenApply()`, we are streaming and using `CompletableFuture::join` which will wait for 

**Scenario 4: Use Fallback if Main API Fails**

```
getWeatherFromPrimary()
    .exceptionally(ex -> {
        System.out.println("Primary failed: " + ex.getMessage());
        return getWeatherFromBackup().join(); // Blocking fallback
    })
    .thenAccept(System.out::println);

```

**Scenario 5: Retry Logic with CompletableFuture**

```
public CompletableFuture<String> fetchWithRetry(int attemptsLeft) {
    return callApi()
        .handle((result, ex) -> {
            if (ex != null && attemptsLeft > 1) {
                return fetchWithRetry(attemptsLeft - 1).join(); // retry
            } else if (ex != null) {
                throw new RuntimeException("All retries failed");
            } else {
                return result;
            }
        });
}

```

