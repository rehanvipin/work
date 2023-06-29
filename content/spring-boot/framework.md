---
title: "Spring Framework"
weight: 1
# bookFlatSection: false
# bookCollapseSection: false
# bookComments: true
---
# Spring Framework

## What? Why would you use it?
It is a dependency injection framework that helps you simplify your code and make it more testable.
It started as an alternative to Java 2 EE and has now grown beyond it.
It even includes a web framework (Web MVC) within it.
Baked in with best practices so you can focus on the business problem.

It encourages you to use interfaces and helps minimize configuration code.

That should make your app more flexible to different implementations and make it easier to test.

It's popular as it encourages developers to write POJOs rather than Enterprise Java Beans and stuff.

Another selling point of the Spring Framework is that it makes configuration changes easier.
You don't have to recompile your app in case you want to use a different implementation of some interfaces.

In Spring v5 (not sure since when), almost all configurations can be done with just Java.
No XML necessary.

## ApplicationContext

## Java configuration
One of the main thing Spring does is, it constructs the dependent objects for your class.
Rather than you creating new objects. This is done through a bunch of configurations

### @Bean

### @Configuration

### Setter injection

### Constructor injection

## Scope

### Singleton

### Prototype

### Web scopes

## Autowiring
Convention over configuration

### @Autowired

### @ComponentScan

Then, there are the stereotypes.

### @Component

### @Repository

### @Service

### @Controller

## XML Configuration
It was the original way to do it. Why would you still use XML configuration?
It provides a separation of concerns where wiring up dependencies can be done away from the app code.

## Bean lifecycle

## Advanced stuff
* Bean profiles - don't do it, try to write code that works in all environments
* SPring Expression Language - used in @Value