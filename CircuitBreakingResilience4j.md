Circuit Breaking - Resilience4j
===============================

# Circuit Breakers Overview
External services may occasionally go down and this may be beyond the control of the local program. Traditionally, the local program could repeatedly retry the external service multiple times before eventually failing. The problem with this is that if the external service is well and truly fucked and not coming back any time soon, by retrying we are simply putting extra load onto a system that is already struggling and delaying the inevitable error message.

A circuit breaker monitors a call to some service. If the call begins to fail and is deemed to be down, it will activate. When activated, instead of attempting to call the service, the circuit breaker will fallback to some contingency method to perform different activity. This activity could simply be to fail-fast or it could be an entirely different execution flow.

# Resilience4j

## Overview
Resilience4j is a Java circuit-breaker library which was inspired by the now deprecated Netflix Hystrix library. It offers circuit breaking and similar fault-tolerance capabilities to functional interface, lambda expression or method references.

## Usage
In its simplest form, a circuit breaker can be implemented on a method like this:

```Java
CircuitBreaker circuitBreaker = CircuitBreaker.ofDefaults("backendService");
circuitBreaker.executeSupplier(backendService::doSomething);
```

This makes a circuit breaker with the default settings (as pre-configured out of the box in the library) and then calls the `doSomething` method on the service wrapped with the circuit breaker.

### Spring
Resilience4j also has Spring support. Circuit breakers can be defined using the `application.yml` file:

```YAML
resilience4j.circuitbreaker:
    configs:
        default:
            slidingWindowSize: 100
            permittedNumberOfCallsInHalfOpenState: 10
            waitDurationInOpenState: 10000
            failureRateThreshold: 60
            eventConsumerBufferSize: 10
            registerHealthIndicator: true
        someCustomConfig:
            slidingWindowSize: 50
            permittedNumberOfCallsInHalfOpenState: 10
    instances:
        backendA:
            baseConfig: default
            waitDurationInOpenState: 5000
        backendB:
            baseConfig: someCustomConfig
```

In-code alterations to the circuit breaker can also be performed using a bean:

```Java
@Bean
public CircuitBreakerConfigCustomizer testCustomizer() {
    return CircuitBreakerConfigCustomizer
        .of("backendA", builder -> builder.slidingWindowSize(100));
}
```

Configs acts as a base set of configuration items which instances inherit from and can choose to then expand upon if needed.

The circuit breakers can then be applied to a method using an annotation:

```Java
@CircuitBreaker(name = backendA, fallbackMethod = "fallback")
public String doSomething(String param1) {
   throw new IllegalArgumentException("Service failed.");
 }

private String fallback(String param1, IllegalArgumentException e) {
  return "Circuit breaker has triggered the fallback method.";
}
```

Above, we have created a `fallback` method which has an identical method signature as the method it protects except for the name of the method and a single additional exception parameter. It is possible to have multiple fallback methods to handle different exceptions. To do this, overload the chosen fallback method and alter the exception each one has in its parameters. When a corresponding exception is thrown by the protected method, the correct fallback method will be called.
