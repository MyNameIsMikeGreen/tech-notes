Spring Events
=============

# Overview
Spring events are objects that are published to be consumed by one or many listeners during the lifecycle of an application. Spring has several built-in events announcing various key stages in application startup and shutdown; but it is possible to also create and publish custom events during program execution to trigger custom behaviour.

# Structure
## Events
Events are simply POJOs that extend `ApplicationEvent`. They act as a DTO to transfer information about the event they represent. They must have a constructor which calls `super(source)` which represents the class where the event originated. Beyond this, there are no requirements for fields or methods to be included in the class. Similar to exceptions, a `message` field is common, but not necessary.

### Event Example
```java
public class MyEvent extends ApplicationEvent {

    public final String message;

    public MyEvent(Object source, String message) {
        super(source);
        this.message = message;
    }

}
```

## Publishers
Instances of `ApplicationEventPublisher` publish events using the `publishEvent()` method. If a class needs to publish events, it is possible to either extend `ApplicationEventPublisher` and call `publishEvent()` as a local method or to autowire the default `ApplicationEventPublisher` bean and call it's method.

### Publisher Example

By extension:
```java
public class MyService extends ApplicationEventPublisher {

    public void fireEvent() {
        MyEvent event = new MyEvent(this, "Event message goes here");
        publishEvent(event);
    }

}
```

By autowire:
```java
public class MyService {

    private final ApplicationEventPublisher applicationEventPublisher;

    public TestController(ApplicationEventPublisher applicationEventPublisher) {
        this.applicationEventPublisher = applicationEventPublisher;
    }

    public void fireEvent() {
      MyEvent event = new MyEvent(this, "Event message goes here");
      applicationEventPublisher.publishEvent(event);
    }

}
```

## Listeners
An event listener is a component method that is invoked when a certain event type has been published. Since Spring 4.2, registering a method as an event listener is as simple as adding `@EventListener` to it and ensuring the method accepts the event as a parameter. Once this is done, any time the associated event is published by the application, this method will execute.

It is possible to have many listeners which are all listening for the same event type. When the event is published, the associated methods will all be executed.

### Listener Example

```java
@EventListener
public void handleMyEvent(MyEvent event) {
    System.out.println("MyEvent received: " + event.message)
}
```

# Synchronous vs Asynchronous
By default, listeners are executed synchronously. As it shares the same thread as the publisher, this means that the publisher will block until the listener(s) have completed execution. It also means that if there were multiple listeners for the same event, the listeners would execute in series - one after another.

If we wish for the listeners to execute asynchronously when an event is published, the only change required is to add a new Spring config class. Then, events will not cause the publisher to block, and all listeners will execute in parallel.

### Asynchronous Config Example

```java
@Configuration
public class AsynchronousSpringEventsConfig {

    @Bean(name = "applicationEventMulticaster")
    public ApplicationEventMulticaster simpleApplicationEventMulticaster() {
        SimpleApplicationEventMulticaster eventMulticaster = new SimpleApplicationEventMulticaster();
        eventMulticaster.setTaskExecutor(new SimpleAsyncTaskExecutor());
        return eventMulticaster;
    }

}
```

# Resources
* [Minimalistic example of Spring events usage (MyNameIsMikeGreen GitHub)](https://github.com/MyNameIsMikeGreen/spring-events-example)
* https://www.baeldung.com/spring-events
