---
layout: post
title: "FastAPI Dependency Injection: 21 Examples Using Depends() with AI Answers"
date: 2024-10-18 23:32:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2024/10/fastapi-dependency-injection-21.html
---

### Why ?

Once I wanted to understand how [Depends](https://en.wikipedia.org/wiki/Dependency_injection) works in [FastAPI](https://fastapi.tiangolo.com/) more thoroughly to understand what's under the hood. I started communicating with the AI. The answer was a question, and I got hooked.

I decided to test it in real code and created a training project and wrote this post for it based on my questions to the AI and its answers.  
Some answers were wrong, some I had already optimized a bit. But all the results shown are real and created by me personally.

The result was 21 training examples written in Python, where I checked the answers and suggestions from the AI.  

### 

### Questions:

* Prepare for use FastAPI project
* How does injection Depends work in FastAPI?
* The decision of who will run when depends on the finish route. The question is, will the yield continue?
* But the response was wrong!! We were expecting the output "Cleaning up the resource" after the error.
* So the dependency function must always be used with a try: finally?
* Okay, if depends is a class, then its instance is created once, like
  a singleton? How can information be saved between requests if the
  instance is dead after the request?
* In the redis example, should the database initialization ideally also be in the form of dependencies?
* With the asynchronous yeld for the asynchronous database initialization, is there a lot of complexity involved?

### Prepare for use FastAPI project

```
poetry init
poetry shell
poetry add "fastapi[standard]"
```

### Hello World

[![](/assets/images/blog/27e7829c7f95d1e7-4e74325d2fef379d.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiQlLF2TWN4Ztb_QZYtSvr2Mkb7owb6Sp_uxZPQ5OX3f_dhKbw9OALxD_SX3eSTC17xQEetGwqzGiOhpU0eSJ0Vx2SJmQqrrR0ryxRDzFrV-i8p31YfFpxpmvhxrLeP3t7Z9K_VbPMf3Iiwz7BRTCevZevZR7nVpGOkbvR6UTQiLGs_MsrRxzCo6ZP85kFH/s1138/%D0%97%D0%BD%D1%96%D0%BC%D0%BE%D0%BA%20%D0%B5%D0%BA%D1%80%D0%B0%D0%BD%D0%B0%202024-10-18%20%D0%BE%2017.26.10.png)  
*ex\_01.py*

  Run the app in developer mode.   

[![](/assets/images/blog/f0b07ae48a929c39-891a3221b799833a.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEix5OAKbMP6lKw26l_GzdU4YX7JNArS5hhfQA4GnAbEleZEVenLIE6TIMna7IcMWgk7LlUwrhhuRDgoHnraGZIT0zFBr1oqkSFkMpkaQYXo5RCeLFK7qjzJw0-P8nn897nurUSL9G3O0B6rAgzCsawI6suP2Pg6QzH7YYA3yqqnFG_eXcsAIUptUfBN8sRK/s1562/%D0%97%D0%BD%D1%96%D0%BC%D0%BE%D0%BA%20%D0%B5%D0%BA%D1%80%D0%B0%D0%BD%D0%B0%202024-10-18%20%D0%BE%2017.12.04.png)  
*fastapi dev ex\_01.py*

```
% curl -L http://127.0.0.1:8000 
{"Hello":"World"}
```

[![](/assets/images/blog/a2761af1516dc411-9a268c5113bb1d1b.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhJiTw4FgCuZWsCZWeQQYvIDeYruQjbzD1qya7AMcR7enc2yseLIi9P8ysqSLPR_ZmzZIQfHhvXaee9DkItl3NxRHUuM4iqe8edhH4wugY-6uQEK-DQgPkGRl29ZScLBEuRhHzQl9NG3b-SEAj3MolfUUOZnMia5rDtovTg7_3YnsMHTAltQP77xEGK9c1G/s1504/%D0%97%D0%BD%D1%96%D0%BC%D0%BE%D0%BA%20%D0%B5%D0%BA%D1%80%D0%B0%D0%BD%D0%B0%202024-10-18%20%D0%BE%2017.14.27.png)

#### 

#### Q: How does injection Depends work in FastAPI?

A: In FastAPI, dependency injection is a powerful feature that allows you to
provide dependencies for your functions in a clean, modular way. Dependencies
in FastAPI can be used for tasks such as database connections, security
checks, authentication, or anything else that is reusable across your app.  
  
Here's
how dependency injection works in FastAPI:

### 1. Defining a Dependency

A dependency is typically a function or class that performs some action or
returns a value that will be injected into your FastAPI route handlers.
Dependencies can have their own parameters, which can also be injected.

[![](/assets/images/blog/4abf328fb5617a47-4a61da1a320b9dd6.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjlAs_N8Qe5crstxqOMjRyX1URcY1t9ymNJILSH9dMUo_uCqBjeg6H73REr-h2SqXkq_x0q1a1_WGXRkXLWvSZ7Q-pjT5_xxys3CxpdSbPsHkSJK_5pClkbb60DLDxkJTAku0baCXfNXfUIkjrOEN-3UnF3ED1ie6p5G2SC3f8ZII2LdFZUvDh69vDRRvD1/s1516/%D0%97%D0%BD%D1%96%D0%BC%D0%BE%D0%BA%20%D0%B5%D0%BA%D1%80%D0%B0%D0%BD%D0%B0%202024-10-18%20%D0%BE%2016.30.34.png)  
*ex\_02.py*

```
$ curl -L http://127.0.0.1:8000 
{"dependency":"dependency_value"}
```

In this example:

* `get_dependency` is a dependency function that simply returns
  `"dependency_value"`.
* The `Depends()` function in the route handler's argument
  indicates that FastAPI should call the `get_dependency` function
  and inject its result into the `dependency_value` parameter of
  the route.

### 2. Injecting Dependencies into Routes

When FastAPI sees Depends(), it knows to call the dependency function (in this case, get\_dependency()) and pass its return value into the corresponding route function argument (dependency\_value). This happens automatically before the request is processed.

### 3. Class-based Dependencies

You can also use classes as dependencies, allowing you to maintain internal
state across requests if necessary:

[![](/assets/images/blog/e6c7a16f7915a312-52ff583bb6a54a0f.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjfx6ylSZAdIhQKgSqusZLdnIAuZiPhn3_cR-TwdWUHZXjzBN96gRZSEQtJRrTZmdCxPqBjBPh6oguf3y9vBZTMgZlkr-VaPJ6FaGYHcet60XiyHouX-l2xWTKVWnl4eF73FhUGWnCoG7Q4nrryJnPXod-4-anjj9q1aCczrqZahLpfIXJCZ0IY-wak4nhf/s1600/%D0%97%D0%BD%D1%96%D0%BC%D0%BE%D0%BA%20%D0%B5%D0%BA%D1%80%D0%B0%D0%BD%D0%B0%202024-10-18%20%D0%BE%2019.11.26.png)  
*ex\_03.py*

```
% curl -L http://127.0.0.1:8000/class
{"class_dependency":"class_dependency_value"}
```

In this example:

* DependencyClass is instantiated, and its get\_value method is called in the route handler.

### 4. Request Lifespan Dependencies

Some dependencies may require setup and teardown, such as opening and closing a database connection. FastAPI supports "yield-based" dependencies for these cases:

[![](/assets/images/blog/ab9e3c5e014db15e-efffba788a742369.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjKGDxo2SORE0WHVLuwNY9phZi6-ZH2pQrPqBeowRZTmgAOynWMRsaaae3t1BWOCIgliKtuY5L-coZ99r5lB1SGPFtcCFeJsYnGFb4MucD7oI9GgPOUjn3rWVVsQiqND8wPOHP8AgPTsQ3JoWi6oUsm1h01sl2F24zmEl6FuN0ZEvbFvXcq_AovLDhNGgQc/s1634/%D0%97%D0%BD%D1%96%D0%BC%D0%BE%D0%BA%20%D0%B5%D0%BA%D1%80%D0%B0%D0%BD%D0%B0%202024-10-18%20%D0%BE%2019.17.38.png)  
*ex\_04.py*

```
% curl -L http://127.0.0.1:8000/teardown
{"connection":"Opened connection"}
```

In this pattern:

* You yield the value for the dependency.
* After the request completes, any code after yield runs (e.g., closing the connection).

### 5. Sub-dependencies

Dependencies can depend on other dependencies. FastAPI will resolve the entire chain of dependencies automatically:

[![](/assets/images/blog/26258eca00e6b946-092020982660d6f8.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj5nYxeFtJvtFUjTGYtx3stpk2O2c5j_dNVqkRzZRDPJOfGGEwJn4Z0td-yfdd2ORiKK7Y6n9LnlTq88afReky6kA3omLUlCG0s49D9PNBij95AXrrO15M8YMsuQELFt1EL6g_3Lgr-kPkVIqiKKHNGvZP7IL6FlO57CFCATtlYnjjXQZrljDbTKGlCaMYj/s1634/%D0%97%D0%BD%D1%96%D0%BC%D0%BE%D0%BA%20%D0%B5%D0%BA%D1%80%D0%B0%D0%BD%D0%B0%202024-10-18%20%D0%BE%2019.20.47.png)  
*ex\_05.py*

```
% curl -L http://127.0.0.1:8000/sub     
{"dependency":"main_sub_dependency_value"}
```

  
In this case, main\_dependency relies on sub\_dependency, and both are injected into the route.

### Summary

* **Dependencies**: Functions or classes that return data or perform logic that can be reused across routes.
* **Injection**: FastAPI automatically resolves dependencies for each request.
* **Lifespan Management**: FastAPI handles the entire lifecycle of dependencies, including any setup/teardown when needed.

#### 

#### Q: The decision of who will run when depends on the finish route. The question is, will the yield continue?

A: When using a yield in FastAPI dependencies, FastAPI manages the lifecycle of the request and the dependency. Here's an explanation of who runs the dependency after the route finishes and whether the yield continues afterward.

### 1. **When Does FastAPI Run the Dependency?**

* Before the route: The code before the yield statement in the dependency is executed before the route handler is called. This part is usually used for setup, like opening a database connection.
* After the route: The code after the yield statement in the dependency is executed after the response is returned. This is often used for teardown operations, like closing a database connection or cleaning up resources.

### 2. **Who Runs the Dependency Code After the Route Finishes?**

FastAPI's request handling mechanism runs the code after `yield` automatically, as part of the dependency lifecycle. When the route handler finishes processing the request and returns a response, FastAPI will:

* Call the part after the yield in the dependency.
* This happens regardless of the success or failure of the route handler. Even if an exception occurs in the route, the code after yield will run (similar to a finally block in a try-except-finally statement).

### 3. Does the yield Continue After the Route Finishes?

Yes, the execution continues after the yield when the route finishes. Here’s a breakdown:

* The code before yield is executed when the dependency is first invoked (before the route handler).
* The code after yield is executed once the route handler completes its execution (whether successfully or with an error).

This is useful for handling cleanup or teardown tasks that need to happen after the response is sent to the client.

### Example 06

Let's look at a practical example of how this works:

[![](/assets/images/blog/707063012d7624d3-fd0b8eae39fb854e.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjMAQVV_1AvMqUE2s4HYP5n7k5IUUqISJYgHXxl4zyhjgWUEdU81BL7Sm2BwUcPfTx9F0_o_weuQ3gs432hSwxaGEgtWa4kiUngdeXfNRM_tVQjpRQQLiWZ6JdYp7xe6uk7NqcGUuBR98x5NQmKF24R3vmd9-Gm6a6Nze2WJyDfAiaAw_CJdeQ6x5uF7wzo/s1576/%D0%97%D0%BD%D1%96%D0%BC%D0%BE%D0%BA%20%D0%B5%D0%BA%D1%80%D0%B0%D0%BD%D0%B0%202024-10-18%20%D0%BE%2019.34.18.png)  
*ex\_06.py*

```
% curl -L http://127.0.0.1:8000/example_06
{"message":"Request handled"}
```

```
INFO:     Application startup complete.
Setting up the resource
Handling request with resource: Resource is ready
Cleaning up the resource
INFO:     127.0.0.1:53391 - "GET /example_06 HTTP/1.1" 200 OK
```

### Execution Flow

1. Setup Phase: When a request is made to /example\_06, FastAPI calls dependency\_with\_yield. It runs the code before the yield, printing "Setting up the resource". The resource value ("Resource is ready") is passed to the route handler.
2. Route Handler Phase: The route handler (read\_example) runs next. It prints "Handling request with resource: Resource is ready", and returns the response.
3. Teardown Phase: After the route has finished executing, FastAPI goes back to the dependency and runs the code after yield. This prints "Cleaning up the resource". This happens after the response is sent to the client.

### Important Notes:

* The teardown code (after yield) always runs, even if there's an error in the route or dependency.
* This behavior is similar to Python's context managers or try-finally blocks, where cleanup happens automatically.

### Error Handling

If an exception occurs inside the route handler, FastAPI will still run the code after the yield. Here's an example to illustrate this:

[![](/assets/images/blog/ef0c8e866107f01d-ba88301c7acd7f99.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhzF9_HFGEULLe5c-IyfvFKaB5L0cUJ_vjl7GARuXL8rT84W0VAWt_QaKl7Qybd-GDaSaHgg7h0a5uv_W3hWVhr-_tSvYDBOQSMMIsTdoCiKIsFb6XBeE1vvqJUHz51Zqfw1bS7fj_Mnsu8TSvA4IHAGD_uKKZOWBaZOF2-s7UpgLZFcQ-LgsF4K20tZLkr/s1576/%D0%97%D0%BD%D1%96%D0%BC%D0%BE%D0%BA%20%D0%B5%D0%BA%D1%80%D0%B0%D0%BD%D0%B0%202024-10-18%20%D0%BE%2019.56.50.png)  
*ex\_07.py*

```
% curl -L http://127.0.0.1:8000/error
Internal Server Error
```

```
INFO:     Application startup complete.
+++++++ Setting up the resource +++++++++++++++++++++++

Handling request with resource: Resource is ready

INFO:     127.0.0.1:54933 - "GET /error HTTP/1.1" 500 Internal Server Error
ERROR:    Exception in ASGI application
Traceback (most recent call last):
....
  File "..../fastapi_learn/ex_07.py", line 16, in route_with_error
    raise Exception("Something went wrong!")  # Simulate an error
    ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Exception: Something went wrong!
```

In this case:

1. The setup ("Setting up the resource") runs.The route throws an exception.
2. Despite the error, the teardown ("Cleaning up the resource") still runs.
3. This ensures proper resource management, even in failure scenarios.

#### Q: But the response was wrong!! We were expecting the output  "Cleaning up the resource" after the error.

A:  It looks like the cleanup code after yield in your dependency is not running, which can happen due to a subtle issue with how exception handling and async dependencies work in FastAPI. Let's fix this behavior.

### Cause:

The issue arises because you are raising an exception, and FastAPI uses an internal ASGI error handler to manage it. This might sometimes prevent the cleanup code from running, especially with async functions or dependencies.

### 

### Solution

You can fix this by ensuring the correct context management in your dependency using a try/finally block around the yield statement, which ensures the cleanup code always runs, even if the route handler raises an exception.

Here's the modified code:

[![](/assets/images/blog/e42bf1c3a64d59d1-5fdea7c8518ff74c.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEggTNYd7dosEjiRayjj6NZuGJh2gdUtWtGq2LGMexyOOi_7YwPciYtn0N18dtmEen9WDtbyAt6saHgDN30Pt05-imAeWC-J8NY2qikRHpaGFhXCq8VL_4C6zrU0m8p9uTJlYiOIXh7liyE5_2kTnq66samwnftQmbfwMpZ4_oM8uGZT8rNi9hTzsIvi_nuI/s1604/%D0%97%D0%BD%D1%96%D0%BC%D0%BE%D0%BA%20%D0%B5%D0%BA%D1%80%D0%B0%D0%BD%D0%B0%202024-10-18%20%D0%BE%2020.13.40.png)  
*ex\_08.py*

### Explanation:

* try/finally block: Now, the finally block ensures that the cleanup code (the part after yield) will always be executed, even if an exception is raised during the request handling.
* yield: The yield statement allows the resource to be passed to the route. After the route finishes (successfully or with an error), the code in the finally block is executed.

### Behavior:

When you run this and make a request to /error, you should see the following log sequence:

```
% curl -L http://127.0.0.1:8000/error_finally
Internal Server Error
```

```
INFO:     Waiting for application startup.
INFO:     Application startup complete.
+++++++ Setting up the resource +++++++++++++++++++++++

Handling request with resource: Resource is ready

******  Cleaning up the resource ***********************


INFO:     127.0.0.1:55704 - "GET /error_finally HTTP/1.1" 500 Internal Server Error
ERROR:    Exception in ASGI application
Traceback (most recent call last):
...
  File "...../fastapi_learn/ex_08.py", line 18, in route_with_error
    raise Exception("Something went wrong!")  # Simulate an error
    ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Exception: Something went wrong!
```

This ensures that the cleanup code always runs, regardless of whether an exception occurs.   
In summary, the final block ensures the proper cleanup after the route execution, even in the event of an error. This behavior is important for managing resources like database connections, files, or external services reliably.

#### 

#### Q: So the dependency function must always be used with a try: finally?

A: In FastAPI, a dependency function does not always need to use a try/finally block, but it should use it when there are resources that require proper setup and teardown, such as:

* Database connections
* File handles
* External service connections (like Redis, message queues, etc.)
* Any resource that requires explicit cleanup (closing, releasing, etc.)

### When to use try/finally in a dependency:

* When you need to ensure cleanup: If your dependency involves acquiring and releasing resources (e.g., opening a database connection, file, or network connection), you should use try/finally to ensure that the resource is properly released even if an exception occurs in the request handler.
* For yield-based dependencies: If your dependency uses yield, it typically involves some form of setup before yield and cleanup afterward. In this case, try/finally is useful to ensure that the cleanup happens after the route handler finishes, regardless of the outcome (success or error).

### When you don't need try/finally:

* Simple logic dependencies: If your dependency is just providing a simple value (e.g., fetching a configuration value, calculating a limit), you don't need try/finally. In such cases, there's no resource that requires special cleanup.

### Example 1: With try/finally (when resource management is required)

[![](/assets/images/blog/5cba0e96430548b5-5fdea7c8518ff74c.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjcjCqFt7mTR0z73UGE5jDqGfeVzDtQY4DdEN7IyxP1vN6dRVQc3IILc8As2GXwNW_aQPjS3-x3xjaSSU6Lu5arOtSbElsJPT7H0TTrN24N4Qac9xYUKXfb5oIBUrwTx1c9lzUObm1BifE38uJaJRCYn-5MCOxJAiUQ7c1C4fgPSh6GeMwpxUuhd-IwpZ7o/s1604/%D0%97%D0%BD%D1%96%D0%BC%D0%BE%D0%BA%20%D0%B5%D0%BA%D1%80%D0%B0%D0%BD%D0%B0%202024-10-18%20%D0%BE%2020.13.40.png)  
*ex\_09.py*

```
% curl -L http://127.0.0.1:8000/items/
{"message":"Success"}
```

```
INFO:     Application startup complete.
+++++++ Opening database connection ++++++++++++++++++
Using resource: Database connection
****** Closing database connection ******************
INFO:     127.0.0.1:56398 - "GET /items/ HTTP/1.1" 200 OK
```

In this example, the `try/finally` ensures that the database connection is closed, even if an error occurs during request processing.

### Example 2: Without try/finally (simple logic)

[![](/assets/images/blog/b8242761921bebfb-82f14078ec727eca.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhnyJpMqP-LqVCihxUsdji_BoKT8VOvzfpTngEuMNL4t96ThaQLVxVcHVSkctyEclh1K-4zY-k4MuRojk80QvvglWoZTV23SfGBpb8XYdnSmxFR33Roi8UnSGSNBR53YOw4C9ZEbZc4Hv-0x7A27-IuqNpju15Kcs-5sFWxLmEY5Ds8KmQdpusoChvvJG66/s1392/%D0%97%D0%BD%D1%96%D0%BC%D0%BE%D0%BA%20%D0%B5%D0%BA%D1%80%D0%B0%D0%BD%D0%B0%202024-10-18%20%D0%BE%2020.41.17.png)  
*ex\_10.py*

```
% curl -L http://127.0.0.1:8000/items/
{"limit":10}
```

  
In this case, no try/finally is needed because there’s no resource that requires cleanup. The function just returns a simple value.

### 

### Summary:

* Use try/finally when dealing with resources that need to be properly cleaned up.
* For simpler logic, where no resources are involved, you don't need try/finally.

#### Q: Okay, if depends is a class, then its instance is created once, like a singleton? How can information be saved between requests if the instance is dead after the request?

A: In FastAPI, class-based dependencies are not treated like singletons by default. Instead, they are instantiated on every request, meaning that the class instance does not persist between requests unless you explicitly manage state outside of the dependency system.  

### 1. Class-based Dependencies Behavior

When using a class as a dependency in FastAPI, each time a request is made, FastAPI will create a new instance of the class for that specific request. Once the request finishes, the instance is discarded. This ensures that each request gets its own fresh instance, which prevents unintended data sharing between requests.  
  
Here’s an example of a class-based dependency:  

[![](/assets/images/blog/e34fc85bd755e57b-1ce830f31a358ad8.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhGxVFmLL8bFYrUmQkzmccFZcZpIez_UOjvlfu5_j1Pf-YGib3XJtn1etc9oomVqx80QsEV7Q_Y4X0rEafm9phdPT3L6r4GFGSjsarzdHb4R1FkuRAofxPHb9xPC1Ax6uuMzvksf96xi2_Fkdwc7lAGHborw8ThvRmvuzttdZqS8SffJzqh8vgAcf7Tk4Hm/s1458/%D0%97%D0%BD%D1%96%D0%BC%D0%BE%D0%BA%20%D0%B5%D0%BA%D1%80%D0%B0%D0%BD%D0%B0%202024-10-18%20%D0%BE%2021.04.07.png)  
*ex\_11.py*

```
% curl -L http://127.0.0.1:8000/counter 
{"count":1}
  
% curl -L http://127.0.0.1:8000/counter 
{"count":1}
```

In this case:  

* The MyDependency class is instantiated on every request, so the self.value attribute is always reset to 0 for each new request.
* The increment() method increments the value for that instance, but the state won’t persist between requests.

### 2. Maintaining State Between Requests

If you need to persist state between requests, you have several options. Common approaches include:  
   
**a. Use a Global Singleton or a Shared Object**  
  
You can create a singleton or shared object that stores state globally in your application. This can be done using a Python class or a data structure (like a dictionary or database) that holds the state.  
  
Here’s an example using a shared global object:

[![](/assets/images/blog/130f570d56644961-e264d1ae3a351c85.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhoPEbUlzwx3WcdiIMXn4B5ZO8BgMVXeWwl4eG9b6zRJzr9VoV023YBY2vw9ul1vL3ox6W-tkLI90ffISrXplu9EB1OFczVIlPU_o9xWyyHrgbVUnEkikUphzpfYFNII3xF_f-qfXRSGmynAtnSuuRuDvF2AK6i-N8YtZTsxPm35g-1iCAemBmUIlyShO6Q/s1506/%D0%97%D0%BD%D1%96%D0%BC%D0%BE%D0%BA%20%D0%B5%D0%BA%D1%80%D0%B0%D0%BD%D0%B0%202024-10-18%20%D0%BE%2022.04.14.png)  
*ex\_12.py*

```
% curl -L http://127.0.0.1:8000/global-counter
{"count":1}
  
% curl -L http://127.0.0.1:8000/global-counter
{"count":2}
  
% curl -L http://127.0.0.1:8000/global-counter
{"count":3}
```

```
INFO:     Application startup complete.
INFO:     127.0.0.1:61763 - "GET /global-counter HTTP/1.1" 200 OK
INFO:     127.0.0.1:61767 - "GET /global-counter HTTP/1.1" 200 OK
INFO:     127.0.0.1:61769 - "GET /global-counter HTTP/1.1" 200 OK
```

In this case:  

* GlobalCounter is a class that stores a count attribute shared across requests.
* MyDependency modifies this shared state by incrementing GlobalCounter.count.
* The state persists between requests because it’s stored globally, not in the dependency class instance.

**b. Use a Cache or External Storage (e.g., Database, Redis)**  
  
Another approach to persist state between requests is using external storage like a database or a cache (e.g., Redis). This ensures data consistency and persistence across requests, regardless of the app's lifecycle.  
  
Here’s an example using Redis:

```
% redis-server &  
% poetry add redis
Using version ^5.1.1 for redis
 - Installing redis (5.1.1)
```

[![](/assets/images/blog/6ec10c9861e00da8-83b6db6ee4e1b7ee.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiEYCeJCR-Fog_pzz_emFOC9Ha-YIqHx0SjdhWEVJrSHwXsIwtWlIMNsoBVhZWAwI_JCfifHRxvGo0FGFeZhClzNoU-iRLaDjVoIu8w697eUw_-oKgwuiMLL7r3yPEG9rwzxS_aAbjjnshdArfs575jcpQxoX5uC_Y3xL_ozow0eXzZvg50Lj2NO3fkP2G8/s1506/%D0%97%D0%BD%D1%96%D0%BC%D0%BE%D0%BA%20%D0%B5%D0%BA%D1%80%D0%B0%D0%BD%D0%B0%202024-10-18%20%D0%BE%2022.15.41.png)  
*ex\_13.py*

In this case:  

* The state (counter) is stored in Redis and persists across requests.
* Each time the route is accessed, RedisDependency interacts with Redis to increment the counter key.

**c. Application Lifecycle State**  
  
You can use FastAPI’s application state to store and share data across requests using the app.state attribute. This is useful for cases where you need lightweight, in-memory state.

[![](/assets/images/blog/572fd27f2925cce3-cc20b0bbb3ef948b.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhJj-a9JFQciDuiJdtsG7ubHfpb60_6FuNQwOk2VNu0OGa82KMNEUGmS5REN45p7KSGRVO5STj7xf00TzURZ_CO055AqO6N1pd3RTJwt3sJrx1rClt7I7GmWPzfB0HMVEHhoKOcK19Mq2-Qu4qgqSoOSoBzjlWXR5pJDqVZLiBUpTvNvx033C5GVzYoBedG/s1436/%D0%97%D0%BD%D1%96%D0%BC%D0%BE%D0%BA%20%D0%B5%D0%BA%D1%80%D0%B0%D0%BD%D0%B0%202024-10-18%20%D0%BE%2022.31.54.png)  
*ex\_14.py*

```
% curl -L http://127.0.0.1:8000/state-counter
{"count":1}
  
% curl -L http://127.0.0.1:8000/state-counter
{"count":2}
  
% curl -L http://127.0.0.1:8000/state-counter
{"count":3}
```

```
INFO:     Started server process [18916]
INFO:     Waiting for application startup.
Initialize on startup app.state.counter=0
INFO:     Application startup complete.
INFO:     127.0.0.1:63498 - "GET /state-counter HTTP/1.1" 200 OK
INFO:     127.0.0.1:63501 - "GET /state-counter HTTP/1.1" 200 OK
INFO:     127.0.0.1:63518 - "GET /state-counter HTTP/1.1" 200 OK
```

In this example:  

* The counter is initialized once during the app startup event and stored in app.state.
* StateDependency modifies the shared counter, and its value persists across requests.

### 3. When Singleton Behavior is Needed

If you need singleton-like behavior for class-based dependencies (i.e., you want the class to be instantiated once and reused across requests), you can implement it manually by controlling the instantiation outside of the dependency system. Here’s how you could do it:

[![](/assets/images/blog/83b0137de2c90a9c-a4638473bc377971.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhOhn090VXTgOchfz4SwsUwFO4bFNQ3Mwuj-qVRZVlfa7ysejQ5zVy8FYKrGuzanvmr9kSmvrEdAid0rlUqLJ8iClko10wSXGy1OrpVJag1j9VkN2oDWZbB6j9HC8tB8DndAUXOCOe9ysJJDLvjXRVQr3lbPBlo18322fDYE38CsDM1dl2KOBoGkUt8waBc/s1708/%D0%97%D0%BD%D1%96%D0%BC%D0%BE%D0%BA%20%D0%B5%D0%BA%D1%80%D0%B0%D0%BD%D0%B0%202024-10-18%20%D0%BE%2023.06.06.png)  
*ex\_16.py*

```
% curl -L http://127.0.0.1:8000/singleton
{"count":1}
% curl -L http://127.0.0.1:8000/singleton
{"count":2}
% curl -L http://127.0.0.1:8000/singleton
{"count":3}
```

```
INFO:     Application startup complete.
INFO:     127.0.0.1:65178 - "GET /singleton HTTP/1.1" 200 OK
INFO:     127.0.0.1:65180 - "GET /singleton HTTP/1.1" 200 OK
INFO:     127.0.0.1:65181 - "GET /singleton HTTP/1.1" 200 OK
```

Using Depends(SingletonDependency) directly (without a separate function like get\_singleton\_dependency) is valid and works because SingletonDependency itself acts like a factory function.  
  
Here’s a breakdown of why it works and when you might still prefer to use an explicit dependency function (get\_singleton\_dependency):

* Why Depends(SingletonDependency) Works:

* Class Constructor as Dependency: In FastAPI, when you pass Depends(SingletonDependency), FastAPI will automatically call SingletonDependency() and inject the result into the route. Since your SingletonDependency class has a singleton pattern implemented in \_\_new\_\_(), it ensures that only one instance of the class is ever created.
* No Need for an Explicit Dependency Function: If the class constructor (\_\_new\_\_()) has all the necessary logic (like in this case, handling the singleton creation), there’s no need for an intermediate function like get\_singleton\_dependency(). FastAPI can directly resolve the dependency using the class itself.

* When to Use a Dependency Function:

* Additional Initialization Logic: If you needed additional logic to run before or after the creation of the dependency, it would make sense to use a separate function like get\_singleton\_dependency(). For example, if you needed to open a database connection or perform setup before returning the singleton.
* Parameter Handling: If the creation of the singleton required parameters or if you wanted to customize the singleton behavior based on the incoming request, a function like get\_singleton\_dependency() could handle this.
* Testing and Mocking: Having a function like get\_singleton\_dependency() can be useful for testing or swapping out dependencies. You could easily mock or replace the dependency function without modifying the class itself.

**Which One to Use:**  

* Simple Singleton: If your class manages everything internally (like in your current example), you can use Depends(SingletonDependency) directly. It simplifies the code by removing the extra dependency function.
* Complex Dependency Management: If your dependency involves more complex setup, cleanup, or parameters, then it’s better to use a function (like get\_singleton\_dependency) to handle that.

#### Q: In the redis example, should the database initialization ideally also be in the form of dependencies?

A: Yes, ideally, the Redis connection initialization should also be handled as a dependency in FastAPI. This ensures that the Redis client is properly managed and can be easily replaced or configured as needed, rather than being a global variable. By using a dependency, you can also handle connection pooling, cleanup, and error handling more effectively.  
  
Here’s how you can refactor the example to initialize the Redis client as a dependency:

[![](/assets/images/blog/9c294de210da4d98-3b5eb90b5393bc8a.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhVMNX_p-Cd7PfXwOzs4_u7ZOSUBvLCmMCtaVXxSMgsJJMZplLHNhr2Nuq6sbs6-9FembJWUEQKCcKrK3ROYHfXgjpwFO8yeXbXpaYhNcGwNgRR79wrXCpjBvX6T_U0tcUUxbCchpcTuptjZVqjzt8WD92Jh7mLMDEo5MqTCTXqjNgfxPcrTkO7zCt1gP1w/s1380/%D0%97%D0%BD%D1%96%D0%BC%D0%BE%D0%BA%20%D0%B5%D0%BA%D1%80%D0%B0%D0%BD%D0%B0%202024-10-18%20%D0%BE%2023.32.32.png)  
*ex\_17.py*

```
% curl -L http://127.0.0.1:8000/redis-counter
{"error":"Redis error: Error 61 connecting to localhost:6379. Connection refused."}                       
% curl -L http://127.0.0.1:8000/redis-counter
{"count":4}%                                                                                                                                   
% curl -L http://127.0.0.1:8000/redis-counter
{"count":5}%                                                                                                                                   
% curl -L http://127.0.0.1:8000/redis-counter
{"count":6}%
```

```
INFO:     Application startup complete.
Opening Redis connection
Try increment Redis value
Redis error: Error 61 connecting to localhost:6379. Connection refused.
Close Redis connection
INFO:     127.0.0.1:50198 - "GET /redis-counter HTTP/1.1" 200 OK
Opening Redis connection
Try increment Redis value
Close Redis connection
INFO:     127.0.0.1:50303 - "GET /redis-counter HTTP/1.1" 200 OK
Opening Redis connection
Try increment Redis value
Close Redis connection
INFO:     127.0.0.1:50306 - "GET /redis-counter HTTP/1.1" 200 OK
Opening Redis connection
Try increment Redis value
Close Redis connection
```

**Key Points:**

1. get\_redis Dependency: The get\_redis function initializes and yields a Redis client. The yield allows FastAPI to manage the lifecycle of the connection (though closing the connection is optional since Redis handles connections efficiently).
2. RedisDependency Class: The RedisDependency class now accepts the Redis client as an argument, keeping the logic clean and reusable.
3. Route: The route handler uses Depends(get\_redis) to get the Redis client, which is then passed to the RedisDependency class.

By doing this, you ensure the Redis client initialization is flexible and properly managed, while maintaining clean, testable code.

More:  [Dependencies with yield](https://fastapi.tiangolo.com/tutorial/dependencies/dependencies-with-yield)

#### 

#### Q: Why not use cascading dependencies? Or two dependencies in the parameters?

A:  You can absolutely use cascade dependencies or multiple dependencies directly in the route parameters. This can help you create more modular, reusable, and flexible code.  
  
Here’s a quick overview of both options:

### 1. Cascade Dependencies (Chaining Dependencies)

Cascade dependencies (also called dependency injection chains) allow you to define one dependency that depends on another. This creates a chain where dependencies are passed from one to another, giving you more control over initialization and reusability.  
  
In the Redis example, you can make the RedisDependency depend on the Redis connection directly.

[![](/assets/images/blog/37e872592deb96c4-e2b01b7b14896ef2.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgw4aJoxlMDPYnPycZ9hsQojkdSXjuxzlh_msMRPx-PhEYXbLVtnwDn3mV-DgSD0_e0C6kRyhKIgL7JX7ZcNBljxcHt3GYgBnzT6ZEBRe-XVyuwbRKF-9nnqEMbsAca7iixZfqLSQRGlsojZ5KVCv-1fa6X4ALR6yZEGv7VMl7dnGeFC_RvmdmQH9b8dWkE/s1244/%D0%97%D0%BD%D1%96%D0%BC%D0%BE%D0%BA%20%D0%B5%D0%BA%D1%80%D0%B0%D0%BD%D0%B0%202024-10-18%20%D0%BE%2023.46.25.png)  
*ex\_18.py*

```
% curl -L http://127.0.0.1:8000/redis-counter
{"count":7}%                                                                                                                                   
% curl -L http://127.0.0.1:8000/redis-counter
{"count":8}%                                                                                                                                   
% curl -L http://127.0.0.1:8000/redis-counter
{"count":9}%
```

**Cascade Dependency Workflow:**

* get\_redis: Initializes the Redis client.
* get\_redis\_dependency: Takes the Redis client (from get\_redis) and returns an instance of RedisDependency.
* Route: The route directly receives RedisDependency, which already has the Redis client.

This approach keeps things modular. The Redis client is initialized in one place, and the class-based logic (RedisDependency) is cleanly separated.  
  
More: [Sub-dependencies](https://fastapi.tiangolo.com/tutorial/dependencies/sub-dependencies/)

#### Q: With the asynchronous yeld for the asynchronous database initialization, is there a lot of complexity involved?

A:  When working with async dependencies in FastAPI, such as when initializing an async database connection, the concept of yield combined with try-finally is similar to the synchronous approach but comes with a few key differences due to the nature of asynchronous programming. Using an async dependency with yield is still quite manageable and is essential for handling proper cleanup of resources like database connections or network clients in asynchronous code.  
  
Here’s a breakdown of how it works and a more complex example:

### Key Differences with async and yield

1. Asynchronous Resource Handling: If the database initialization and connection handling are asynchronous (e.g., using asyncpg, Tortoise ORM, or SQLAlchemy with async), you need to use async def functions with yield.
2. Async Cleanup: The cleanup (like closing a connection) also needs to be asynchronous. That’s where the combination of yield and try-finally becomes useful in ensuring the resource is properly cleaned up.

Example: Async Database Connection as Dependency  
  
Let’s assume you’re using an async database client like SQLAlchemy (with the new async support) - async engine:

```
% poetry add sqlalchemy  
Using version ^2.0.36 for sqlalchemy  
  - Installing greenlet (3.1.1)  
  - Installing sqlalchemy (2.0.36)  
% poetry add asyncpg  
Using version ^0.29.0 for asyncpg  
  - Installing asyncpg (0.29.0)
```

```
% docker pull postgres:latest  
% docker run -d --name postgres_fastapi \  
  -p 5432:5432 \  
  -e POSTGRES_PASSWORD=123 \  
  -e POSTGRES_DB=fs \  
  -e POSTGRES_USER=fs \  
  postgres:latest  
% docker exec -it postgres_fastapi su postgres -c 'psql -U fs -d fs -c "  
CREATE TABLE items (  
    id SERIAL PRIMARY KEY,  
    name VARCHAR(100),  
    description TEXT,  
    price DECIMAL(10, 2),  
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP  
);"'  
% docker exec -it postgres_fastapi su postgres -c 'psql -U fs -d fs -c "INSERT INTO items (name, description, price) VALUES (111, 222, 333);"'  
% docker exec -it postgres_fastapi su postgres -c 'psql -U fs -d fs -c "INSERT INTO items (name, description, price) VALUES (911, 922, 933);"'   
% docker exec -it postgres_fastapi su postgres -c 'psql -U fs -d fs -c "SELECT * FROM items;"'
```

```
 id | name | description | price  |         created_at           
----+------+-------------+--------+----------------------------  
  1 | 111  | 222         | 333.00 | 2024-10-18 22:00:47.300743  
  2 | 911  | 922         | 933.00 | 2024-10-18 22:01:03.883602  
(2 rows)
```

[![](/assets/images/blog/b3164b15b61234fa-413ad1229b25a416.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEibjFaTnA6wX0PEHsjV6WcfL7tE9iaDlYspx6kegGpcyZyahwhuAV5QpkSBNdJF5QKgVlRt_s9u1QJuRt9iUmOia8NnTJvWhPN_lmxcfP1HmGomNro5GBXkhrK8Y21uexh-3lKT5tHey6IoD9efWHJ71Y93XKSprWTo5G2ngn0nIwsNqr9sKscvRv7CskFo/s1248/%D0%97%D0%BD%D1%96%D0%BC%D0%BE%D0%BA%20%D0%B5%D0%BA%D1%80%D0%B0%D0%BD%D0%B0%202024-10-19%20%D0%BE%2001.13.14.png)  
*ex\_19.py*

```
% curl -L http://127.0.0.1:8000/items
{"items":[{"id":1,"name":"111","description":"222","price":333.0,"created_at":"2024-10-18T22:00:47.300743"},{"id":2,"name":"911","description":"922","price":933.0,"created_at":"2024-10-18T22:01:03.883602"}]
```

### Key Points of the Async Example:

1. async def get\_db(): The function is asynchronous, and it opens a session using an async with block. This makes sure the session is properly managed even in async context.
2. yield session: The yield keyword gives control to the route handler, allowing it to use the session.
3. finally with await: After the request is processed (or if an exception occurs), the session is closed asynchronously with await session.close(). The await is necessary because closing the session is an asynchronous operation.
4. Route Handler: The route uses async def, allowing the entire flow (including database operations) to be asynchronous, which improves scalability for I/O-bound tasks.

### How try-finally Works in Async Context:

* yield with async: The yield passes control to the route handler, similar to how it works in synchronous functions. But in an async function, FastAPI can await any asynchronous operations inside the handler.
* finally with await: The finally block ensures the cleanup (like closing the database session) is performed asynchronously using await. This is crucial for any resource that requires asynchronous cleanup, such as database connections, file handles, or network resources in async code.

### More Complex Dependency with Async Cascade

For more complex scenarios, you can also chain dependencies, even in an async context:

[![](/assets/images/blog/142833c162eaea80-2d7df5abd8697d88.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgz8ewnXASrkkkfjQ1yuqkywYJK-D5Lg6bijjkJcwIXglyQ2nRBDjhOd8vmilhbB0BEELMHLvSwvsbfhJCOrVmYrXiptRC9EynhJ1-tnC8tcKWUHcSb6hRwS2RCNKD4l9yfDHln8yu4jOCeRRS808mXEwhKHFP13tpKqYL4Tzl5lEvNs90NUDLiaToAebpW/s2029/code-snapshot.png)  
*ex\_20.py*

```
% curl -L http://127.0.0.1:8000/complex_something
{"result":"CustomService.do_something: class sqlalchemy.orm.session.AsyncSession"}                                                      
% curl -L http://127.0.0.1:8000/complex_query    
{"result":[{"id":1,"name":"111","description":"222","price":333.0,"created_at":"2024-10-18T22:00:47.300743"},{"id":2,"name":"911","description":"922","price":933.0,"created_at":"2024-10-18T22:01:03.883602"}]}
```

With this approach, fetch from asyncpg is used directly, and it returns a list of Record objects that you can easily convert to dictionaries.

[![](/assets/images/blog/fcf582574a748781-2d7df5abd8697d88.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjBUjUnUb0t8FPr8F18tnyc6Winu094Jw1nAcAFweQqr_J44A-wRCzZuCXIelUf0sc7PxPXbPe7w1NCud_moUZdiRrCaigOIfg_bBt_ruoUsX7V3V2u7QPndTSMOxNdZmEv-3pexJgzTkSgYoYcR-CL2Fcz_hs0dZfhxj1YEbtutRzm1Rioks_f4T3QXauk/s2011/code-snapshot.png)  
*ex\_21.py*

```
% curl -L http://127.0.0.1:8000/complex_something
{"result":"CustomService.do_something: class 'asyncpg.connection.Connection'"}%                                                              
% curl -L http://127.0.0.1:8000/complex_query    
{"result":[{"id":1,"name":"111","description":"222","price":333.0,"created_at":"2024-10-18T22:00:47.300743"},{"id":2,"name":"911","description":"922","price":933.0,"created_at":"2024-10-18T22:01:03.883602"}]}%
```

### In summary:

* If you want to keep using SQLAlchemy's ORM features, use AsyncSession and avoid mixing it with asyncpg directly.
* If you prefer using asyncpg for raw queries, don't use SQLAlchemy's text() or session objects.

Source code on Github: <https://github.com/lexxai/FastAPI_Depends>

Deployed version: <https://fastapi-depends.onrender.com/docs>
