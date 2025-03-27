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
    -   [ ] Commit: `Create Notification service struct skeleton.`
    -   [ ] Commit: `Implement subscribe function in Notification service.`
    -   [ ] Commit: `Implement subscribe function in Notification controller.`
    -   [ ] Commit: `Implement unsubscribe function in Notification service.`
    -   [ ] Commit: `Implement unsubscribe function in Notification controller.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-2" questions in this README.
-   **STAGE 3: Implement notification mechanism**
    -   [ ] Commit: `Implement update method in Subscriber model to send notification HTTP requests.`
    -   [ ] Commit: `Implement notify function in Notification service to notify each Subscriber.`
    -   [ ] Commit: `Implement publish function in Program service and Program controller.`
    -   [ ] Commit: `Edit Product service methods to call notify after create/delete.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-3" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Publisher) Reflections

#### Reflection Publisher-1
1. In the Observer pattern, the `Subscriber` is typically defined as an interface (or a trait in Rust) so that multiple concrete implementations of the subscriber can exist. This allows flexibility where different types of observers (subscribers) can define their own behavior when receiving notifications. However, in the current BambangShop case, where all subscribers just receive HTTP notifications and behave the same way (just storing a `URL` and `name`, then sending an HTTP request when notified), then a simple `struct` like the existing `Subscriber` is enough. On the


2. Using a `DashMap` (map/dictionary) is necessary in this case rather than a `Vec` (list) because `id` in `Program` and `url` in `Subscriber` are intended to be unique. A `Vec` would require iterating through all elements to check for uniqueness before inserting or deleting a subscriber, leading to inefficient `O(n)` operations. In contrast, `DashMap` provides `O(1)` average-time complexity for inserts, lookups, and deletions, making it more efficient for managing unique keys like `url`. Additionally, `DashMap` is concurrent, which ensures safe and efficient access in a multi-threaded environment, making it well-suited for a real-time notification system where multiple operations may happen simultaneously.

3. The `Singleton` pattern ensures that only one instance of a data structure exists throughout the application's lifecycle, which is useful for global state management. However, in Rust, using a `Singleton` alone does not guarantee thread safety. Since the `SUBSCRIBERS` list is accessed and modified concurrently, a `DashMap` is necessary because it provides fine-grained locking at the bucket level, allowing multiple threads to read and write efficiently. If we only used the `Singleton` pattern with a standard `HashMap` inside a `Mutex` or `RwLock`, it would introduce performance bottlenecks due to global locking. Therefore, while `Singleton` ensures a single instance, `DashMap` is still required for safe and efficient concurrent access.

#### Reflection Publisher-2
1. Separating `Service` and `Repository` from the `Model` in the `MVC` pattern aligns with the principles of `Single Responsibility` and `Separation of Concerns`. The `Repository` abstracts data access logic, ensuring that the `Model` does not directly interact with the database, making it easier to switch data sources without modifying business logic. The `Service` layer handles business logic separately from the `Model`, preventing models from becoming bloated with logic that is unrelated to data representation. This separation enhances maintainability, testability, and scalability by allowing changes in business rules or data access to be managed independently without affecting other layers of the application.

2. If we only use the `Model` without separating `Service` and `Repository`, each `Model` (e.g., `Program`, `Subscriber`, `Notification`) would be responsible for handling data persistence, business logic, and interactions with other models. This leads to tight coupling, where changes in one `Model` can create a ripple effect across others, increasing code complexity. For example, if `Program` needs to notify `Subscriber`, it must contain logic for retrieving subscribers, creating `Notification` instances, and handling delivery—resulting in bloated models with multiple responsibilities. Additionally, testing becomes harder since unit tests would require database interactions. Without clear separation, maintaining and scaling the application becomes increasingly difficult as dependencies between models grow, making refactoring risky and introducing potential bugs.

3. Postman is extremely useful for testing API endpoints efficiently, this tool helps me test my current work by testing a `POST` request to subscribe a user to a notification service. It allows me to send structured JSON payloads, inspect server responses, and validate the correct API behavior. In this case, I sent a subscription request to `http://localhost:7777/notification/subscribe/APPLIANCES` with a JSON body containing a `url` and `name`. The successful response with a `201 Created` status confirms that the request was processed correctly. Postman’s ability to display responses in a formatted JSON view helps verify if the API returns the expected data. Additionally, the tool’s features like request history, automated tests, and environment variables simplify debugging and managing different API configurations. For my Group Project and future software engineering tasks, using Postman ensures that API functionalities work as expected before integrating them into the application, reducing errors and saving development time.

#### Reflection Publisher-3
