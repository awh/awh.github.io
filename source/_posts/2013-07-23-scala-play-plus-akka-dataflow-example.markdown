---
published: false
layout: post
title: "Scala Play + Akka DataFlow Example"
date: 2013-07-23 08:01
comments: true
categories: 
---
In this article we will develop a sample application that demonstrates the use of Akka DataFlow to make web service calls from a Play Action in a non-blocking fashion. Before we begin let's take a quick look at the circumstances in which we might want to do this.

## The New Normal Architecture

Historically a bog standard web application would consist of an MVC framework where the business logic (e.g. controllers) talk to the database in a fairly direct fashion. With the rising popularity of native apps and third party integrations, it has become desirable to decouple the views and presentation oriented controller logic from the data oriented logic and persistent storage by interposing a network accessible API, most often in the form of RESTful services:

This clean separation of concerns allows apps and third parties to reuse the core business logic; however, now the controllers in the web application tier must access these network APIs instead of talking to the database directly. Whilst it's possible to continue to do this in the same blocking style that most database client libraries offer, it's not particularly scalable, and since we're using Play, non blocking is the name of the game.

In the following sections we'll develop the solution incrementally, committing each part to git so you can follow along. Finally we'll see if we can get the sample included in the official Play repository!

## Carousel Begin

Having forked playframework on GitHub, we first use `play` to create a new application named `dataflow` in `playframework/samples/scala` and select Scala as our application type - a necessity due to the fact that Akka DataFlow relies on language support for _continuations_.

## Contiwhatnow?

If you're already familiar with the concept of continuations feel free to skip to the next section - if not, read on. 

Essentially a 'continuation' encapsulates the current state of a thread of execution (typically the call stack) and reifies it into an object which can be stored, manipulated and resumed at will. This powerful capability allows us to suspend a computation when it might otherwise block, and re-use the underlying thread for something else in the meantime. When the suspended computation is able to make progress (for example because more data has been received from the network) it can be resumed on a free thread.

In low level languages which support pointer arithmetic and direct memory access (such as C) this is readily achieved, and the standard library contains functions such as `setjmp` and `longjmp` to store and resume thread state. In a managed code environment such as the JVM things are more difficult; see the paper '[Implementing First-Class Polymorphic Delimited Continuations by a Type-Directed Selective CPS-Transform](http://lampwww.epfl.ch/~rompf/continuations-icfp09.pdf)' for details.

## Enabling Scala Continuations in Play

Fortunately this is as straightforward as adding a dependency on the appropriate compiler plugin and enabling it with a command line switch; in a Play application, we can do this by modifying `project/Build.scala` in the following way. Whilst we're here, we'll add a dependency on Akka's dataflow component as we'll need that later:

``` scala project/Build.scala
import sbt._
import Keys._
import play.Project._

object ApplicationBuild extends Build {

  val appName         = "dataflow"
  val appVersion      = "1.0-SNAPSHOT"

  val appDependencies = Seq(
      "com.typesafe.akka" %% "akka-dataflow" % "2.1.0" 
  )

  val main = play.Project(appName, appVersion, appDependencies).settings(
    autoCompilerPlugins := true, 
    libraryDependencies <+= scalaVersion { v => 
      compilerPlugin("org.scala-lang.plugins" % "continuations" % v) }, 
    scalacOptions += "-P:continuations:enable" 
  )

}
```

## Scenario

I was in a presentation by Amazon's CTO Werner Vogels at QCon in 2007 in which he stated that over a hundred internal service calls were required to build the front page of amazon.com - consequently we're going to imagine a fairly common scenario where a Play controller has to make several REST calls to gather the information it needs to build a view. To make things a bit more interesting we're going to assume that there is some conditional behaviour involved in the access of three imaginary services: alpha, beta and gamma.

The purpose of our controller is to first obtain a resource from the alpha service API and then based on the content of that result call either the beta or gamma services for additional details. Before we get to that we'll need some way of simulating these APIs.

## A Quick Diversion

Because I want this example to be self contained I'm going to do something a bit weird and _host some stub controllers representing the three back end APIs in the same Play instance as the new controller we're going to build._ This means that our new controller is going to be connecting back to it's hosting HTTP server, which is something you would rarely do in practice; if it bothers you then you can easily move the JSON returned by the stubs to a separate server to prove that there's no slight of hand going on.

## First Steps

To start with let's assume we want to do something really simple with our controller: make a call to GET a resource from a different server, and return the response received from it directly back to our client. Our controller method must return an `Action` yielding a `Result`; moreover it must do this as quickly as possible which rules out the use of a blocking HTTP client library to make RESTful calls to our services. Fortunately Play contains `play.api.libs.ws.WS` for making non-blocking calls that yield a `Future[Response]`, and a constructor for making an `AsyncResult` to wrap it:

``` scala Controller
  import play.api.libs.ws.WS

  ...

  def get = Action {
    Async {
      WS.url("http://example.com/alpha/resource").get()
    }
  }
```

