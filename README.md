# BambangShop Receiver App
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
4.  `repository`: this module contains structs that serve as databases.
    You can use methods of the struct to get list of objects, or operating an object (create, read, update, delete).

This repository provides a Rocket web framework skeleton that you can work with.

As this is an Observer Design Pattern tutorial repository, you need to implement a feature: `Notification`.
This feature will receive notifications of creation, promotion, and deletion of a product, when this receiver instance is subscribed to a certain product type.
The notification will be sent using HTTP POST request, so you need to make the receiver endpoint in this project.

## API Documentations

You can download the Postman Collection JSON here: https://ristek.link/AdvProgWeek7Postman

After you download the Postman Collection, you can try the endpoints inside "BambangShop Receiver" folder.

Postman is an installable client that you can use to test web endpoints using HTTP request.
You can also make automated functional testing scripts for REST API projects using this client.
You can install Postman via this website: https://www.postman.com/downloads/

## How to Run in Development Environment
1.  Set up environment variables first by creating `.env` file.
    Here is the example of `.env` file:
    ```bash
    ROCKET_PORT=8001
    APP_INSTANCE_ROOT_URL=http://localhost:${ROCKET_PORT}
    APP_PUBLISHER_ROOT_URL=http://localhost:8000
    APP_INSTANCE_NAME=Safira Sudrajat
    ```
    Here are the details of each environment variable:
    | variable                | type   | description                                                     |
    |-------------------------|--------|-----------------------------------------------------------------|
    | ROCKET_PORT             | string | Port number that will be listened by this receiver instance.    |
    | APP_INSTANCE_ROOT_URL   | string | URL address where this receiver instance can be accessed.       |
    | APP_PUUBLISHER_ROOT_URL | string | URL address where the publisher instance can be accessed.       |
    | APP_INSTANCE_NAME       | string | Name of this receiver instance, will be shown on notifications. |
2.  Use `cargo run` to run this app.
    (You might want to use `cargo check` if you only need to verify your work without running the app.)
3.  To simulate multiple instances of BambangShop Receiver (as the tutorial mandates you to do so),
    you can open new terminal, then edit `ROCKET_PORT` in `.env` file, then execute another `cargo run`.

    For example, if you want to run 3 (three) instances of BambangShop Receiver at port `8001`, `8002`, and `8003`, you can do these steps:
    -   Edit `ROCKET_PORT` in `.env` to `8001`, then execute `cargo run`.
    -   Open new terminal, edit `ROCKET_PORT` in `.env` to `8002`, then execute `cargo run`.
    -   Open another new terminal, edit `ROCKET_PORT` in `.env` to `8003`, then execute `cargo run`.

## Mandatory Checklists (Subscriber)
-   [x] Clone https://gitlab.com/ichlaffterlalu/bambangshop-receiver to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [x] Commit: `Create Notification model struct.`
    -   [x] Commit: `Create SubscriberRequest model struct.`
    -   [x] Commit: `Create Notification database and Notification repository struct skeleton.`
    -   [x] Commit: `Implement add function in Notification repository.`
    -   [x] Commit: `Implement list_all_as_string function in Notification repository.`
    -   [x] Write answers of your learning module's "Reflection Subscriber-1" questions in this README.
-   **STAGE 3: Implement services and controllers**
    -   [x] Commit: `Create Notification service struct skeleton.`
    -   [x] Commit: `Implement subscribe function in Notification service.`
    -   [x] Commit: `Implement subscribe function in Notification controller.`
    -   [x] Commit: `Implement unsubscribe function in Notification service.`
    -   [x] Commit: `Implement unsubscribe function in Notification controller.`
    -   [x] Commit: `Implement receive_notification function in Notification service.`
    -   [x] Commit: `Implement receive function in Notification controller.`
    -   [x] Commit: `Implement list_messages function in Notification service.`
    -   [x] Commit: `Implement list function in Notification controller.`
    -   [x] Write answers of your learning module's "Reflection Subscriber-2" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Subscriber) Reflections

#### Reflection Subscriber-1
1. In this tutorial, we used RwLock<> to synchronise the use of Vec of Notifications. Explain why it is necessary for this case, and explain why we do not use Mutex<> instead?

We used `RwLock<Vec<Notification>>` in this tutorial to synchronize access to the notification list while allowing multiple readers to access it concurrently. This is crucial because multiple parts of the program may need to read notifications simultaneously without blocking each other. `RwLock` allows multiple threads to hold read access (`read()`), but only one thread can write (`write()`) at a time. If we had used `Mutex<Vec<Notification>>` instead, every access (both read and write) would require locking the entire data structure, even for read operations. This would result in unnecessary blocking and reduced performance, especially in a scenario where reads are more frequent than writes.


2. In this tutorial, we used lazy_static external library to define Vec and DashMap as a “static” variable. Compared to Java where we can mutate the content of a static variable via a static function, why did not Rust allow us to do so?

Rust does not allow direct mutation of static variables like Java because of its strict memory safety guarantees and ownership model. In Java, static variables are shared globally and can be modified directly, which may lead to race conditions in a multithreaded environment. Rust, on the other hand, enforces thread safety at compile time. Static variables in Rust must be immutable by default, and if mutability is required, they need to be wrapped in synchronization primitives like `RwLock`, `Mutex`, or `lazy_static!`. This ensures that any mutation of shared state is explicitly synchronized, preventing data races and ensuring safe concurrent access.


#### Reflection Subscriber-2

1. Have you explored things outside of the steps in the tutorial, for example: src/lib.rs? If not, explain why you did not do so. If yes, explain things that you have learned from those other parts of code.

Yes, I explored additional parts of the code outside the main tutorial steps, such as src/lib.rs. From this exploration, I learned that lib.rs serves as the entry point for defining the module structure of the project. It helps in organizing different components like models, services, controllers, and repositories. Additionally, understanding how modules are structured in Rust gave me better insight into how dependencies are managed and how different parts of the application interact.

2. Since you have completed the tutorial by now and have tried to test your notification system by spawning multiple instances of Receiver, explain how Observer pattern eases you to plug in more subscribers. How about spawning more than one instance of Main app, will it still be easy enough to add to the system?

The Observer pattern simplifies adding more subscribers because each subscriber (Receiver instance) can register dynamically without modifying the core logic of the Main app. This makes it easy to scale and integrate new notification handlers. However, spawning multiple instances of the Main app introduces complexity, as each instance would need proper synchronization mechanisms to ensure consistent state across all instances. If not managed correctly, there could be issues such as duplicate notifications or missed updates.

3. Have you tried to make your own Tests, or enhance documentation on your Postman collection? If you have tried those features, tell us whether it is useful for your work (it can be your tutorial work or your Group Project).

I launched multiple instances of the Receiver app, each assigned to different ports (8001, 8002, and 8003) by adjusting the `.env` file to modify the `ROCKET_PORT` and `APP_INSTANCE_NAME` variables. After starting each instance separately using `cargo run` in different terminals, I also ran a single instance of the Main app. Using the provided Postman collection, I tested the subscription functionality by registering different Receiver instances for various product types. Then, I triggered the `create Product`, `publish Product`, and `delete Product` endpoints in the Main app and confirmed that each Receiver instance correctly received its corresponding notifications. This experiment validated that the Observer pattern efficiently supports multiple subscribers receiving updates dynamically. Additionally, by testing different product categories, I ensured that notifications were properly routed to the intended Receiver instances. Postman proved valuable in streamlining the testing process, making it easy to send requests and verify responses.
