---
layout: default
title: "purejin"
summary: "Java dependency injection through code"
categories: [project, java, "dependency injection"]
color: "#434"
bgcolor: "Gold"
links: { "issues": "https://github.com/jbee/purejin/issues", "github": "https://github.com/jbee/purejin" }
---

# Java Dependency Injection Through Code
* java
* dependency injection
* library
* refactoring

{% include links.html %}

## What happened?
The detailed documentation provided earlier -- as so often -- got outdated when the code
evolved. I decided to not explain something in words again that code can say so much better.

The following is not much but it will be accurate. 
If something is not covered [ask me](http://jbee.github.io).


## What it is
Dependency management through **code**. 
No XML. No annotations. No code dependencies in the wrong direction. 
Application code is written as if there is no DI library. 
Indeed, this is the goal: wiring up the application had become so simple that no 
sophisticated library is needed to aid it. 
Purejin will make sophisticated wiring easier until it is no longer needed and
gracefully disappears as simplicity emerges.


## Why it came to be
The classic struggle: How hard could it be to write something better than 
the so called "mature" traps mostly used in the enterprise? 
As often it turned out: not that complicated.
More importantly: while the major players are all addictive frameworks the purejin
library is intended as a substitute that in the end gets rid of itself.
Confused? It's just saying: [Small is beautiful](http://www.infoq.com/presentations/small-large-systems).
We don't need a library for that. So I'm not using it.


## Why use it?
If you have already decided to use a container you're most likely better off 
with purejin. It's tiny, debuggable, straight forward stand alone library that 
makes common things easy and uncommon ones easy to add.
It avoids anything not refactoring-safe, confusing, complicated or otherwise 
hard to maintain like XML or annotations, aspects, bytecode rewirting, classloader magic etcetera.
It really is just plain old boring code. 
However, it makes some strong decisions to keep dependency injection sane.
Don't fight them. Ask why. Learn.


## What it does
* provide a type safe fluent API for...
* basic semantic: bind this to that under those circumstances
* binds types or instances (named instances of a type)
* provide full generic support (even different binds depending on different generics)
* binds any class/interface (no exceptions or special handling)
* binds multiple values together or in different modules and receives them as array, list, set
* use your list/set implementations instead of javas (is really easy)
* binds different configurations without a single `if`
* binds in different scopes
* use your own scopes/lifecycles (is really easy)
* use your own provider interface to escape scoping problems (is really easy) 
* restrict bindings to packages, types, instances or type/instance hierarchies or parents
* tell you at **construction time** what is missing, ambigous or
wrongly scoped.
* ask you how to find and identify "things of interest" (instead of telling you how to mark them)
* ...

## What it doesn't do
* capitalise on familiarity or proclaimed "ease of use" to get you hooked, then make itself indispensable; Later..
* give you headaches (through grotesque limitations, awkward patterns or cryptic error messages)
* have you google for workarounds and write a lot of really ugly code to do what you want
* have you clutter your code with DI hints
* have you waiting at every start of the application to do ... something obviously too slow
* unload half of maven central (and therby <a href="#what-about-maven">maven</a>) onto you
* use techniques that -- while being fancy or "modern" -- cause any of the above


## How to use it
Checkout the [sources](https://github.com/jbee/purejin).

Build system is currently changed to https://github.com/sormuras/bach/ - 
Details are comming soon to the project home page.

Declare some bindings in a module using a fluent API:
```java
class RobotLegsProblem extends BinderModule {

	@Override
	protected void declare() {
		construct( Robot.class );
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
Maven is part of the complexity problem. I you don't see that yet you will struggle to see
why someone would prefer a library over a framework, code over annotations yadda yadda yadda. 
Take it as a hint. Maybe you feel that something isn't right? 
Then open your mind for the possibility that maven is one of those things and you'll understand eventually. 


## How to learn it
The [tests](https://github.com/jbee/purejin/tree/master/src/test.integration/test/java/test/integration/bind)
for the binding API do illustrate what can be done and how to do it. 
It's all ordinary [vanilla](http://en.wikipedia.org/wiki/Vanilla_software) code. Read it.

Not very handy but it doesn't lie. A tour could be to look at

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
There is more to discover. Most likely purejin allows to do what you want.
Otherwise just ask [me](http://jbee.github.io) for help.


## Curiosities

* [mocking](https://github.com/jbee/purejin/blob/master/src/test.integration/test/java/test/integration/bind/TestMockingBinds.java): "teach" the container to mock undefined dependencies in tests
* [state-switches](https://github.com/jbee/purejin/blob/master/src/test.integration/test/java/test/integration/bind/TestStateDependentBinds.java): inject different instances depending on runtime state
* [initialisers](https://github.com/jbee/purejin/blob/master/src/test.integration/test/java/test/integration/bind/TestInitialiserBinds.java): using "post construct" to automatically close `AutoCloseable`s
