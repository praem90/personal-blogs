---
title: "Unlocking Laravel's Secret Weapon"
sub_title: The Manager Pattern
author: Mohan Raj M (@praem90)
theme:
  name: terminal-dark
---

<!--
speaker_note: |
  Good morning/afternoon everyone.
  Today, we're going to delve into a fundamental design pattern that underpins much of Laravel's elegance and power:
  the Manager Pattern. You've likely interacted with it countless times through familiar facades like Auth,
  Cache, and Queue. This pattern isn't just about switching configurations; it's about unlocking the true potential
  of Laravel's architecture and significantly enhancing our development experience.

  By the end of this presentation, you'll not only understand what the Manager Pattern is but also how you can
  leverage its extensibility to build more flexible and maintainable applications.
-->


What is the Manager Pattern?
===


At its core, the Manager Pattern provides a centralized mechanism for creating and managing different
implementations, often referred to as drivers, for a particular service or functionality.

Think of it as an abstraction layer that allows you to interact with a service without being tightly coupled to a
specific implementation.


In Laravel, the Cache Manager provides a consistent API whether you're using the file, redis, or memcached driver.

<!-- end_slide -->

Laravel's Adoption of the Manager Pattern:
===

Laravel's core is heavily reliant on the Manager Pattern. You see it in action with:

- **Authentication (Auth)**: Drivers like database, eloquent.
- **Caching (Cache)**: Drivers like file, redis, memcached, apc.
- **Sessions (Session)**: Drivers like file, database, cookie, redis.
- **Databases (Database)**: Drivers like mysql, pgsql, sqlite, sqlsrv.
- **Queues (Queue)**: Drivers like sync, database, redis, beanstalkd, sqs.
- **Filesystems (Storage)**: Drivers like local, s3, ftp, sftp.
- **Mail (Mail)**: Drivers like smtp, mailgun, ses, sendmail.
... and more!

This pervasive use highlights how fundamental the Manager Pattern is to Laravel's design philosophy, enabling
flexibility and adaptability across various core functionalities.

<!-- end_slide -->

Benefits of the Manager Pattern in Laravel:
===

The widespread adoption of the Manager Pattern in Laravel yields significant advantages:

#### Driver Flexibility
#### Extensibility
#### Decoupling and Smooth Initial Setup
#### Improved Developer Experience

<!-- end_slide -->

Driver Flexibility:
===

Laravel's configuration-driven approach makes it incredibly easy to switch between different service implementations.


For instance, changing your application's caching mechanism from storing files to using Redis is often as simple as modifying a single configuration value in `config/cache.php`:

<!-- end_slide -->

Driver Flexibility:
===

```php {4|6-19|7-10|12-17}
<?php
return [
    'default' => env('CACHE_DRIVER', 'file'),

    'stores' => [
        'file' => [
            'driver' => 'file',
            'path' => storage_path('framework/cache/data'),
        ],

        'redis' => [
            'driver' => 'redis',
            'host' => env('REDIS_HOST', '127.0.0.1'),
            'password' => env('REDIS_PASSWORD', null),
            'port' => env('REDIS_PORT', 6379),
            'database' => env('REDIS_DB', 0),
        ],
    ],
];
```

This allows you to adapt your application to different environments and scale as needed without significant code
changes.

<!-- end_slide -->

Extensibility:
===

The Manager Pattern isn't limited to the drivers Laravel provides out-of-the-box.

It empowers developers to create their own custom drivers for existing managers.

This is achieved through the `extend()` method available on the Manager classes (often accessible via facades).

By registering a custom driver, you can seamlessly integrate your own implementations into Laravel's core
functionalities. This opens up a world of possibilities for tailoring Laravel to your specific project requirements.

Service Providers play a crucial role here, as they are the typical place where you'd register these custom extensions.
<!-- pause -->
https://laravel.com/docs/12.x/authentication#adding-custom-guards

<!-- end_slide -->

Decoupling and Smooth Initial Setup:
===

The provision of sensible default drivers is a key aspect of the Manager Pattern's benefit.

Take the Cache Manager, for example. The file driver serves as a robust default.
This means that even if you haven't set up a dedicated caching server like Memcached or Redis,
your Laravel application can still function correctly from the moment you install it.

This decoupling of dependencies is crucial for a smooth initial setup and allows developers to get their
applications running without immediately wrestling with external service configurations.
It also makes testing and local development much simpler.

<!-- end_slide -->

Improved Developer Experience:
===

Ultimately, the flexibility and extensibility offered by the Manager Pattern contribute to a significantly
improved developer experience. It allows us to:

- Choose the best tool for the job without rewriting core logic.
- Integrate custom solutions seamlessly.
- Avoid being locked into specific technologies.
- Write cleaner, more maintainable code.


<!-- end_slide -->

Other Potential Use Cases for Custom Managers:
===

The power of custom Managers extends beyond Laravel's core features. Consider these scenarios:

* Payment Gateways: You could create a PaymentGatewayManager with drivers for Stripe, PayPal, Braintree, etc. This would allow you to easily switch or add payment providers with minimal code changes.
* Notification Systems: A NotificationChannelManager could manage different notification channels like SMS (via Twilio, Nexmo), push notifications (Firebase, APNs), and custom in-app notifications.
* File Storage (Beyond Default): While Laravel's FilesystemManager is excellent, you might have very specific internal storage requirements. A custom manager could handle these uniquely.
* API Integrations: If your application interacts with multiple external APIs, a custom manager could provide a consistent way to interact with them, with drivers for each specific API.
In each of these cases, a custom Manager provides a structured and extensible way to handle different implementations of a related service.

<!-- pause -->
Seolve

<!-- end_slide -->
Conclusion:
===

The Laravel Manager Pattern is a cornerstone of the framework's flexibility and extensibility.

It empowers us to seamlessly switch between implementations, extend core functionalities with our own drivers, and ensures a smooth initial setup by providing sensible defaults.

By understanding and leveraging this powerful pattern, we can build more robust, adaptable, and maintainable Laravel applications, ultimately leading to a better developer experience.

Don't be afraid to explore how you can apply the Manager Pattern to your own custom services and unlock the full potential of Laravel's architecture.
