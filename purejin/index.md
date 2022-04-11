---
layout: default
title: PURE Java INjection
summary: "Java dependency injection through code"
categories: [project, java, "dependency injection"]
color: "#434"
bgcolor: "Gold"
links: { "issues": "https://github.com/jbee/purejin/issues", "github": "https://github.com/jbee/purejin" }
tags: [ java, dependency injection, library, refactoring]
---

# PURE Java INjection

Java dependency injection through code.

## Dependency Injection with Pure Java Code
`purejin` is a Java dependency injection library that only uses vanilla Java code to define a container context using a fluent binding API.

It does not use annotations for injection guidance, avoids proxies, bytecode manipulation/interception or any other technique based on conventions that have to be learned.

It only uses vanilla Java code and reflection to build an applications object graph as if it was connected manually based on the bindings made using its fluent API.

Purejin is pure.
It does not depend on other languages, other Java libraries or mechanisms that work by convention.
It's just Java with reflection (no unsafe).
This has the added benefit that it can be debugged and understood like a standard Java application.

Purejin's programming style is inspired by pure functional style:
Users declare a set of declarative statements about the (container) world.
These statements are transformed to a fixed and final container model.
Alongside the immutable model there is a pool of managed instances.
Calling resolution functions on the container is like a computation on its model state that might create new managed instances in the pool as a "side-effect".
This is easy to reason about and allows for state validation at the end of bootstrapping a container.

Its goal is to organise and modularise what otherwise would be manual wiring without imposing restrictions on the application developer.

## Outdated Documentation
The detailed documentation provided earlier -- as is so often the case -- got outdated when the code evolved.
I decided not to explain in words what the code can say so much better.

The following is not much but it will be accurate. 
If something is not covered [ask me](http://jbee.github.io).

## What it is
Dependency management through **code**. 
No XML.
No annotations.
No code dependencies in the wrong direction. 
Application code is written as if there is no DI library. 

This is the goal: wiring up the application had become so simple that no sophisticated library is needed to aid it. 
Purejin will make sophisticated wiring easier until it is no longer needed and gracefully disappears as simplicity emerges.
It is particularly useful for highly configurable and/or modular applications or for application developers that need a small simple tool to make the task of wiring easier.

## Why it came to be
Existing libraries and frameworks felt like double-edged swords that way too often got in the way of the application developer instead of making their lives easier.

The major frameworks have the tendency to bend the application development into their style which leads to applications becoming highly dependent on the framework.
The purejin library is intended as a substitute that in the end gets rid of itself.
Confused?
It's just saying: [Small is beautiful](http://www.infoq.com/presentations/small-large-systems).
We might not need a library for that, but if we decide to use one it should support us, not get in our way or dictate how to code.

## Why use it?
If you have already decided to use a container but dislike the way popular frameworks drive you in certain directions and limit you in others purejin is worth a try.

It's a small, debuggable, straightforward and standalone library that makes common things easy and uncommon ones easy to add or change.

It avoids anything not refactoring-safe, confusing, "invisible" or otherwise hard to maintain like XML, annotations, aspects, bytecode rewirting, classloader magic, code generation, special build tools, etcetera.

It really is just plain old boring code. 
However, it makes a few strong decisions to keep dependency injection sane.
It would be wise to not fight them but ask why and learn how to stay out of trouble especially with large, complex applications.


## What it does
* provides a type safe fluent API
* binds this to that under those injection scenarios
* binds types or instances (named instances of a type)
* provides full generic support (`List<A>` != `List<B>` etcetera)
* binds any class/interface (no exceptions or special handling)
* binds multiple values together or in different modules and receives them as array, list, set
* use your list/set implementations instead of javas (is really easy)
* binds different configurations without `if`s everywhere (built-in concepts)
* binds in different scopes
* can use your own scopes/lifecycles (easy to setup)
* can use your own provider interface to escape scoping problems (easy to setup)
* can restrict bindings to packages, types, instances or type/instance hierarchies or parents
* tells you at **bootstrapping time** what is missing, ambigous or wrongly scoped.
* asks you how to find and identify "things of interest" (instead of telling you how to mark them in your code)
* ...

## What it doesn't do
* build magic
* proxy magic
* interceptors
* requiring annotations and thus access to the target type
* capitalise on familiarity or proclaimed "ease of use" to get you hooked, then make itself indispensable
* give you headaches (through grotesque limitations, awkward patterns or cryptic error messages)
* have you google for workarounds and write a lot of really ugly code to do what you want
* have you clutter your code with hints for the library
* have you waiting at every start of the application to bootstrap
* unload half of maven central (and therby <a href="#what-about-maven">maven</a>) onto you
* use techniques that -- while being fancy or "modern" -- cause any of the above


## How to use it
Declare some bindings in a module using a fluent API:
```java
class RobotLegsProblem extends BinderModule {

	@Override
	protected void declare() {
		construct(Robot.class);
		//...
		bind("left", Leg.class).toConstructor();
		bind("right", Leg.class).toConstructor();
		injectingInto("left", Leg.class)
			.bind(Foot.class).to("left", Foot.class);
		injectingInto("right", Leg.class)
			.bind(Foot.class).to("right", Foot.class);
	}
}
```

Bootstrap the container:
```java
Injector injector = Bootstrap.injector(RobotLegsProblem.class);
```

Resolve the root instance:
```java
Robot robot = injector.resolve(dependency(Robot.class)); 
```
(or any other instance for that matter)

## How to learn it
The [tests](https://github.com/jbee/purejin/tree/master/test.integration/test/java/test/integration/bind) for the binding API illustrate what can be done and how to do it. 
It's all ordinary [vanilla code](http://en.wikipedia.org/wiki/Vanilla_software).
Read it.

Not very handy but it doesn't lie.
Keep in mind that purejin is a library that is designed as a toolbox. 
You pick what you need and adjust it to your case. 
This means there is no such thing as THE way to do things. There are often many ways to reach a goal each having their own pros and cons.
The more concepts you know the more possibilities you will see. 

Basics

* [basic bind](https://github.com/jbee/purejin/blob/master/test.integration/test/java/test/integration/bind/TestBasicInstanceBinds.java) (wire this to that)
* [constants](https://github.com/jbee/purejin/blob/master/test.integration/test/java/test/integration/bind/TestBasicConstantBinds.java)
* [primitives](https://github.com/jbee/purejin/blob/master/test.integration/test/java/test/integration/bind/TestBasicPrimitiveValueBinds.java)
* [arrays](https://github.com/jbee/purejin/blob/master/test.integration/test/java/test/integration/bind/TestBasicArrayElementBinds.java)
* [primitive arrays](https://github.com/jbee/purejin/blob/master/test.integration/test/java/test/integration/bind/TestBasicPrimitiveArrayBridgeBinds.java)
* [collections](https://github.com/jbee/purejin/blob/master/test.integration/test/java/test/integration/bind/TestBasicCollectionBinds.java)
* [parent dependent binds](https://github.com/jbee/purejin/blob/master/test.integration/test/java/test/integration/bind/TestBasicParentLocalBinds.java)
* [package dependent binds](https://github.com/jbee/purejin/blob/master/test.integration/test/java/test/integration/bind/TestBasicPackageLocalBinds.java)
* [API dependent binds](https://github.com/jbee/purejin/blob/master/test.integration/test/java/test/integration/bind/TestBasicSuperbindBinds.java) (bind to supertypes that are concidered an API)
* [API dependent binds 2](https://github.com/jbee/purejin/blob/master/test.integration/test/java/test/integration/bind/TestExampleLocalPublishesByBinds.java) (local overrides to what is considered API)
* [multi-binds](https://github.com/jbee/purejin/blob/master/test.integration/test/java/test/integration/bind/TestBasicMultibindBinds.java) (compose a set/list/array by binding same type at multiple places)
* [hints](https://github.com/jbee/purejin/blob/master/test.integration/test/java/test/integration/bind/TestBasicHintsBinds.java) (select which exact instance to inject elsewhere)
* [providers](https://github.com/jbee/purejin/blob/master/test.integration/test/java/test/integration/bind/TestFeatureProviderBinds.java) (think guice provider)

Configuration/Modularity

* [Env dependent binds](https://github.com/jbee/purejin/blob/master/test.integration/test/java/test/integration/bind/TestBasicModuleWithBinds.java) 
* [Env dependent installation](https://github.com/jbee/purejin/blob/master/test.integration/test/java/test/integration/bind/TestBasicBundleForBinds.java) (or how to not end up with if's in the bundles and modules)
* [require-provide](https://github.com/jbee/purejin/blob/master/test.integration/test/java/test/integration/bind/TestBasicRequiredProvidedBinds.java) (loose coupling like a simple version to OSGi requirements and capabilities)

Usage Examples

* [loggers](https://github.com/jbee/purejin/blob/master/test.integration/test/java/test/integration/bind/TestBasicLoggerBinds.java) (example of target specific injection)
* [robots legs problem](https://github.com/jbee/purejin/blob/master/test.integration/test/java/test/integration/bind/TestExampleRobotLegsProblemBinds.java)

There are many more examples and many more concepts. Have a look at other tests in the same folder. 
Otherwise just [ask me](http://jbee.github.io) for help.


## Curiosities

* [mocking](https://github.com/jbee/purejin/blob/master/test.integration/test/java/test/integration/bind/TestExampleAutoStubbingInterfacesBinds.java): "teach" the container to mock undefined dependencies in tests
* [state-switches](https://github.com/jbee/purejin/blob/master/test.integration/test/java/test/integration/bind/TestExampleStateDependentInjectionBinds.java): inject different instances depending on runtime state
* [Lifts](https://github.com/jbee/purejin/blob/master/test.integration/test/java/test/integration/bind/TestExampleLiftAutoCloseableBinds.java): using "post construct" to automatically close `AutoCloseable`s
