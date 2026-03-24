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
1. Based on the Observer Design Pattern, Subscriber is often defined as an interface (or trait in Rust), however, it is not needed in this Bambangshop case. In the Bambangshop project, the subscribers aren't local objects that have different behaviours, they are separate web services that is represented by a name and a given url (in model/subsriber.rs). This means that the publisher will interact with the subscriber by sending an HTTP payload to that given url. Since this interaction will be identical for every subscriber, then the use of a trait won't be necessary. However, if we plan to create different types of subscribers locally, such as SubscriberEmail, then we must implement a trait to establish a subscriber contract. 
2. Id and URL are intended to be unique identifiers for the related Objects. If we use a Vec (list), CRUD operations would require iteration through the whole Vec just to find a match or enforce uniqueness. This can negatively impact the speed if there are large amounts of data. By using a DashMap, which is a highly optimised HashMap that can run concurrently, we can make use of these unique Id and URLs. Their unique nature can act as a key for the DashMap. This means CRUD operations and uniqueness checks can be done much faster, since find, insertions, and deletions work in O(1) complexity in maps.  
3. Singleton pattern and DashMaps are both necessary, especially in Rust, where the compiler enforces thread safety. We want to make sure that the contents of the database stay consistent to prevent data discrepancies. Additionally, we must be able to handle situations where multiple services might try to add or delete a subscriber at the exact same time. The Singleton pattern ensures that only a single instance of the database is present globally, while DashMaps ensures that read and write operations can be done concurrently by multiple threads. 
#### Reflection Publisher-2
1. The separation of Service and Repository is tied to the concept of separation of concerns and single responsibility principle. In a traditional MVC compound pattern, the Model alone would be responsible for database connections and business logic at the same time. This means we have a Class that has multiple responsibilities. This could negatively impact development, such as difficulty in testing. Separating them can simplify responsibilities:
   - Model becomes a simple class that acts as a representation of the data in the database.
   - Repository is responsible for data accesses and data storing operations (CRUD) with the database.  
   - Services acts as the business logic core. It specifies what should happen in a certain condition. For example, when a product is added, get subscribers from the repository, then trigger notifications. 
This way, the code is much more easier to read, more modular, and easier to test, since we can use mocks.
2. If we only use Model for this case, the code would become more complex. For example, the already existing Product struct would need new functions to able to access the database. Then, it would also need the business logic of sending notifications to each subscriber. This means the models of Product, Subscriber, and Notification would become tightly coupled. A single bug could break the entire process and testing it would be very difficult, since we wouldn't exactly know where the source of the problem is. Other than that, if plan to add another type of product then we must redo or check all the logic and database access mechanisms so it would fit the new product type. 
3. I have used Postman, it has helped me test my work accurately. It basically simulates API requests to the project and we can see if it is responding correctly. Useful features in Postman that I am interested in are the Collections and Workspaces feature. This feature allows me to save API requests, JSONs, and such in a workspace. It is convenient to use, just like unit tests. This ensures that testing is done consistently with no mistakes such as forgetting to test case. This is surely going to be helpful during collaborative work and future software engineering projects, since each member can have consistent test cases. 
#### Reflection Publisher-3
1. This project uses the push model of the Observer pattern. This means that the publisher "pushes" notifications to the subscribers. For example, the case when a new product is created. As we can see from Product service, within the create function, that there is a Notificationservice call made to notify subscribers that a new product is created. The notification payload is pushed by making a HTTP POST request to subscriber's respective endpoints. In this case, the subscribers doesn't have to actively "pull" or ask the publisher for new notifications.
2. Imagine if we used pull model instad:\
    Advantages: 
    - The publisher doesn't have to notify each subscriber, the publisher just have to update its own internal state. 
    - The subscriber can choose when to get the notifications

    Disadvantages:
    - Subscribers doesn't know whether a new product appeared until they pull notifications. Meaning, they can pull even when there's no new product available. This makes pulls more frequent from subscribers.
    - Notifications aren't real time, subscribers only get it when they pull it.
3. If we don't use multi-threading, this can cause create a drastic decrease in performance. This happens because we must execute HTTP requests synchronously. This basically means that when the publisher sends the HTTP POST when "pushing" the notifications, it needs to wait for the OK response from the subscriber before we can continue sending notifications to the next subscriber. This can be a major performance flaw, resulting in slow response time. This is why multi-threading is needed, so the publisher can send notifications asynchronously without having to wait responses before sending notifications to the next subscriber. 