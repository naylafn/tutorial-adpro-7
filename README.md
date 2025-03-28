# BambangShop Publisher App
Tutorial and Example for Advanced Programming 2024 - Faculty of Computer Science, Universitas Indonesia

---

## About this Project
In this repository, we have provided you a REST (REpresentational State Transfer) API project using Rocket web framework.

This project consists of four modules:
1.  `controller`: this module contains handler functions used to receive request and send responses.
    In Model-View-Controller (MVC) pattern, this is the Controller part.
2.  `model`: this module contains structs that serve as data containers.
    In MVC pattern, this is the Model part.
3.  `service`: this module contains structs with business logic methods.
    In MVC pattern, this is also the Model part.
4.  `repository`: this module contains structs that serve as databases and methods to access the databases.
    You can use methods of the struct to get list of objects, or operating an object (create, read, update, delete).

This repository provides a basic functionality that makes BambangShop work: ability to create, read, and delete `Product`s.
This repository already contains a functioning `Product` model, repository, service, and controllers that you can try right away.

As this is an Observer Design Pattern tutorial repository, you need to implement another feature: `Notification`.
This feature will notify creation, promotion, and deletion of a product, to external subscribers that are interested of a certain product type.
The subscribers are another Rocket instances, so the notification will be sent using HTTP POST request to each subscriber's `receive notification` address.

## API Documentations

You can download the Postman Collection JSON here: https://ristek.link/AdvProgWeek7Postman

After you download the Postman Collection, you can try the endpoints inside "BambangShop Publisher" folder.
This Postman collection also contains endpoints that you need to implement later on (the `Notification` feature).

Postman is an installable client that you can use to test web endpoints using HTTP request.
You can also make automated functional testing scripts for REST API projects using this client.
You can install Postman via this website: https://www.postman.com/downloads/

## How to Run in Development Environment
1.  Set up environment variables first by creating `.env` file.
    Here is the example of `.env` file:
    ```bash
    APP_INSTANCE_ROOT_URL="http://localhost:8000"
    ```
    Here are the details of each environment variable:
    | variable              | type   | description                                                |
    |-----------------------|--------|------------------------------------------------------------|
    | APP_INSTANCE_ROOT_URL | string | URL address where this publisher instance can be accessed. |
2.  Use `cargo run` to run this app.
    (You might want to use `cargo check` if you only need to verify your work without running the app.)

## Mandatory Checklists (Publisher)
-   [x] Clone https://gitlab.com/ichlaffterlalu/bambangshop to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [x] Commit: `Create Subscriber model struct.`
    -   [x] Commit: `Create Notification model struct.`
    -   [x] Commit: `Create Subscriber database and Subscriber repository struct skeleton.`
    -   [x] Commit: `Implement add function in Subscriber repository.`
    -   [x] Commit: `Implement list_all function in Subscriber repository.`
    -   [x] Commit: `Implement delete function in Subscriber repository.`
    -   [x] Write answers of your learning module's "Reflection Publisher-1" questions in this README.
-   **STAGE 2: Implement services and controllers**
    -   [x] Commit: `Create Notification service struct skeleton.`
    -   [x] Commit: `Implement subscribe function in Notification service.`
    -   [x] Commit: `Implement subscribe function in Notification controller.`
    -   [x] Commit: `Implement unsubscribe function in Notification service.`
    -   [x] Commit: `Implement unsubscribe function in Notification controller.`
    -   [x] Write answers of your learning module's "Reflection Publisher-2" questions in this README.
-   **STAGE 3: Implement notification mechanism**
    -   [x] Commit: `Implement update method in Subscriber model to send notification HTTP requests.`
    -   [x] Commit: `Implement notify function in Notification service to notify each Subscriber.`
    -   [x] Commit: `Implement publish function in Program service and Program controller.`
    -   [x] Commit: `Edit Product service methods to call notify after create/delete.`
    -   [x] Write answers of your learning module's "Reflection Publisher-3" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Publisher) Reflections

#### Reflection Publisher-1

1. If we expect multiple types of observers with different implementations, then using an interface makes more sense. Based on the current implementation, all subscribers behavior is only store url and name, so there is no need to use a trait unless there will be different types of subscribers with different behaviors.

2. Since url must be unique, using ```Vec``` would have extra checks for searching and deleting which is inefficient. So ```DashMap``` is the better choice because it automatically makes sure that url is unique.

3. Singleton makes sure that there is only one instance in the entire app, but it doesn't provide automatic thread safety for modifications so we need to manually handle it. On the other hand, ```DashMap``` can handle this more efficiently. ```DashMap``` ensures thread safety without requiring explicit locking, plus it is ALREADY a Singleton since ```lazy_static!``` ensures only one instance exists. So we still need ```DashMap```, since Singleton alone doesn't solve concurrency efficiently.

#### Reflection Publisher-2

1. We need to separate ```Service``` and ```Repository``` from a ```Model``` to implement SOLID principle, which makes the code easier to test, maintain, and flexible to change.

2. I think the app will be strongly coupled and become even more complex, it violates the SRP already because the program model is doing too much. If we want to change how notifications are handled, we will need to modify all models that interact with notifications. So if we only use the ```Model```,  it will lead to complex code, duplication, and maintainability issues.

3. Yes, I’ve explored Postman, and it’s a great tool for testing APIs efficiently. It is useful for my projects because it works for any backend (Django and Springboot), and it can improve my group project collaboration with shared API documentation.

#### Reflection Publisher-3

1. Our Observer Pattern implementation use the Push Model variation, because after every change (creating, deleting, publishing) done to a product, it calls the ```notify``` function. The ```notify``` function directly pushes the entire ```Notification``` payload directly to the subscriber's update method.

2. If we used Pull Model, when product has large data fields, instead of sending the entire product object to all subscribers like in Push Model, Pull Model only send an event notification, subscribers can fetch the full details only if they need them. So using Pull Model is more efficient if the product data is large or frequently updated.

3. If we remove multi-threading from the notification process, the program will still function, but there will be significant performance and responsiveness issues. Instead of notifying all subscribers at the same time, the program will process them one by one, which will make the execution time very slow, and the main execution flow will pause until all subscribers are updated. If this happens inside an API request, the entire server response will be delayed.