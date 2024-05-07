jpuddle
===

[![Maven Central](https://img.shields.io/maven-central/v/com.io7m.jpuddle/com.io7m.jpuddle.svg?style=flat-square)](http://search.maven.org/#search%7Cga%7C1%7Cg%3A%22com.io7m.jpuddle%22)
[![Maven Central (snapshot)](https://img.shields.io/nexus/s/com.io7m.jpuddle/com.io7m.jpuddle?server=https%3A%2F%2Fs01.oss.sonatype.org&style=flat-square)](https://s01.oss.sonatype.org/content/repositories/snapshots/com/io7m/jpuddle/)
[![Codecov](https://img.shields.io/codecov/c/github/io7m-com/jpuddle.svg?style=flat-square)](https://codecov.io/gh/io7m-com/jpuddle)
![Java Version](https://img.shields.io/badge/21-java?label=java&color=007fff)

![com.io7m.jpuddle](./src/site/resources/jpuddle.jpg?raw=true)

| JVM | Platform | Status |
|-----|----------|--------|
| OpenJDK (Temurin) Current | Linux | [![Build (OpenJDK (Temurin) Current, Linux)](https://img.shields.io/github/actions/workflow/status/io7m-com/jpuddle/main.linux.temurin.current.yml)](https://www.github.com/io7m-com/jpuddle/actions?query=workflow%3Amain.linux.temurin.current)|
| OpenJDK (Temurin) LTS | Linux | [![Build (OpenJDK (Temurin) LTS, Linux)](https://img.shields.io/github/actions/workflow/status/io7m-com/jpuddle/main.linux.temurin.lts.yml)](https://www.github.com/io7m-com/jpuddle/actions?query=workflow%3Amain.linux.temurin.lts)|
| OpenJDK (Temurin) Current | Windows | [![Build (OpenJDK (Temurin) Current, Windows)](https://img.shields.io/github/actions/workflow/status/io7m-com/jpuddle/main.windows.temurin.current.yml)](https://www.github.com/io7m-com/jpuddle/actions?query=workflow%3Amain.windows.temurin.current)|
| OpenJDK (Temurin) LTS | Windows | [![Build (OpenJDK (Temurin) LTS, Windows)](https://img.shields.io/github/actions/workflow/status/io7m-com/jpuddle/main.windows.temurin.lts.yml)](https://www.github.com/io7m-com/jpuddle/actions?query=workflow%3Amain.windows.temurin.lts)|

## jpuddle

Java classes to pool heavyweight objects.

## Features

* High coverage test suite.
* [OSGi-ready](https://www.osgi.org/)
* [JPMS-ready](https://en.wikipedia.org/wiki/Java_Platform_Module_System)
* ISC license.

## Motivation

Object pooling is a programming technique where, instead of creating new objects
to service requests, a small pool of objects is created and the objects within
the pool are reused repeatedly to service requests instead. This was
traditionally used by Java programs as a performance optimization in an
attempt to reduce memory allocations and therefore reduce the amount of
garbage collection that occurs. On modern Java virtual machines, however,
object pooling as a means to improve performance in this manner is strongly
contraindicated: Object allocations are extremely fast (on the order of a
few tens of nanoseconds), escape analysis often eliminates allocations
entirely, and modern garbage collectors are optimized to make short-lived
objects essentially free.

With this in mind, it may not be clear why the `com.io7m.jpuddle` package
should exist at all! The answer is that object pooling is still useful when
the objects represent external resources that may be very expensive to acquire
and/or the program should avoid acquiring too many of these resources at any
given time. An example of this sort of use case is allocating short-lived
framebuffer objects on a GPU. Graphics memory is typically in relatively
short supply and creating an object on the GPU is generally considered to be
an expensive and slow process (relative to simply allocating an object on the
CPU side). A pool of framebuffer objects can be created that the application
can reuse repeatedly without needing to create new objects, and the size of the
pool can be bounded so that the application does not try to exceed the
available GPU memory.

## Usage

Implement the `JPPoolableListenerType` interface for the object you want
to pool. The interface contains methods needed to estimate sizes for pool
management, and methods to create and delete objects.

Then, create a pool with a soft limit of `100` objects and a hard limit of
`200` objects:

```
JPPoolableListenerType<T> listener;

var p =
  JPPoolSynchronous.newPool(listener, 100L, 200L);
```

Use the `get()` method to retrieve objects from the pool.

