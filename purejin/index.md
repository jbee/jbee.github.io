---
layout: default
title: "purejin"
summary: "Java dependency injection through code"
categories: [project, java, "dependency injection"]
color: "#434"
bgcolor: "Gold"
links: { "issues": "https://github.com/jbee/purejin/issues", "github": "https://github.com/jbee/purejin" }
---

# purejin
* java
* dependency injection
* library
* refactoring

{% include links.html %}

## Dependency Injection with Pure Java Code
`purejin` is a Java dependency injection library that only uses vanilla Java code to define a container context using a fluent binding API.

It does not use annotations for injection guidance, avoids proxies, bytecode manipulation/interception or any other technique based on conventions that have to be learned.

It only uses vanilla Java code and reflection to build an applications object graph as if it was connected manually based on the bindings made using its fluent API.

Purejin is pure.
It does not depend on other languages, other Java libraries or mechanisms that work by convention.
It's just Java with a bit of reflection.
This has the added benefit that it can be debugged and understood like a standard Java application.

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
* tells you at **construction time** what is missing, ambigous or wrongly scoped.
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
Checkout the [sources](https://github.com/jbee/purejin).

Build system is currently changed to [bach](https://github.com/sormuras/bach/) - 
Details are comming soon to the project home page.

Declare some bindings in a module using a fluent API:
```java
class RobotLegsProblem extends BinderModule {

	@Override
	protected void declare() {
		construct(Robot.class);
		//...
		bind(left, Leg.class).toConstructor();
		bind(right, Leg.class).toConstructor();
		injectingInto(left, Leg.class)
			.bind(Foot.class).to(left, Foot.class);
		injectingInto(right, Leg.class)
			.bind(Foot.class).to(right, Foot.class);
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


## What about maven?
Maven is part of the complexity problem.
If you don't see that yet you will struggle to see why someone would prefer a library over a framework, code over annotations yadda yadda yadda. 

Take it as a hint.
Maybe you feel that something isn't right? 
Then open your mind to the possibility that maven is one of those things and you'll understand, eventually. 


## How to learn it
The [tests](https://github.com/jbee/purejin/tree/master/src/test.integration/test/java/test/integration/bind) for the binding API illustrate what can be done and how to do it. 
It's all ordinary [vanilla code](http://en.wikipedia.org/wiki/Vanilla_software).
Read it.

Not very handy but it doesn't lie.
A tour could be to look at

* [most basic](https://github.com/jbee/purejin/blob/master/src/test.integration/test/java/test/integration/bind/TestInstanceBinds.java) (wire this to that)
* [constants](https://github.com/jbee/purejin/blob/master/src/test.integration/test/java/test/integration/bind/TestConstantBinds.java)
* [primitives](https://github.com/jbee/purejin/blob/master/src/test.integration/test/java/test/integration/bind/TestPrimitiveBinds.java)
* [arrays](https://github.com/jbee/purejin/blob/master/src/test.integration/test/java/test/integration/bind/TestElementBinds.java)
* [primitive arrays](https://github.com/jbee/purejin/blob/master/src/test.integration/test/java/test/integration/bind/TestPrimitiveArrayBinds.java)
* [collections](https://github.com/jbee/purejin/blob/master/src/test.integration/test/java/test/integration/bind/TestCollectionBinds.java)
* [parent dependent binds](https://github.com/jbee/purejin/blob/master/src/test.integration/test/java/test/integration/bind/TestParentTargetBinds.java)
* [package dependent binds](https://github.com/jbee/purejin/blob/master/src/test.integration/test/java/test/integration/bind/TestPackageLocalisedBinds.java)
* [auto-binds](https://github.com/jbee/purejin/blob/master/src/test.integration/test/java/test/integration/bind/TestAutobindBinds.java) (bind supertypes)
* [multi-binds](https://github.com/jbee/purejin/blob/master/src/test.integration/test/java/test/integration/bind/TestMultibindBinds.java) (bind multiple values)
* [toggles](https://github.com/jbee/purejin/blob/master/src/test.integration/test/java/test/integration/bind/TestToggledBinds.java) and [choices](https://github.com/jbee/purejin/blob/master/src/test.integration/test/java/test/integration/bind/TestMultipleChoicesBinds.java) (or how to not end up with if's)
* [presets](https://github.com/jbee/purejin/blob/master/src/test.integration/test/java/test/integration/bind/TestModuleWithBinds.java) (or how not to pass values around in module code)
* [require-provide](https://github.com/jbee/purejin/blob/master/src/test.integration/test/java/test/integration/bind/TestRequiredProvidedBinds.java) (loose coupling like to OSGi requirements and capabilities)
* [loggers](https://github.com/jbee/purejin/blob/master/src/test.integration/test/java/test/integration/bind/TestLoggerBinds.java) (example of target specific injection)
* [parameter hinting 1](https://github.com/jbee/purejin/blob/master/src/test.integration/test/java/test/integration/bind/TestSpecificImplementationBinds.java)
* [robots legs problem](https://github.com/jbee/purejin/blob/master/src/test.integration/test/java/test/integration/bind/TestRobotLegsProblemBinds.java)
* [parameter hinting 2](https://github.com/jbee/purejin/blob/master/src/test.integration/test/java/test/integration/bind/TestConstructorHintBinds.java)
* [providers](https://github.com/jbee/purejin/blob/master/src/test.integration/test/java/test/integration/bind/TestProviderBinds.java) (think guice provider)
* [plug-ins](https://github.com/jbee/purejin/blob/master/src/test.integration/test/java/test/integration/bind/TestPluginBinds.java) (a basic extension mechanism)
* [actions](https://github.com/jbee/purejin/blob/master/src/test.integration/test/java/test/integration/action/TestActionBinds.java) (an extension to wire methods as services)

Looking at the other tests in the same folder will also be useful. 
There is more to discover.
Most likely purejin allows you to do what you want.
Otherwise just [ask me](http://jbee.github.io) for help.


## Curiosities

* [mocking](https://github.com/jbee/purejin/blob/master/src/test.integration/test/java/test/integration/bind/TestMockingBinds.java): "teach" the container to mock undefined dependencies in tests
* [state-switches](https://github.com/jbee/purejin/blob/master/src/test.integration/test/java/test/integration/bind/TestStateDependentBinds.java): inject different instances depending on runtime state
* [initialisers](https://github.com/jbee/purejin/blob/master/src/test.integration/test/java/test/integration/bind/TestInitialiserBinds.java): using "post construct" to automatically close `AutoCloseable`s
