---
layout: post
title:  "Middleware"
date:   2018-02-02 10:00:00 -0600
categories: jekyll update
---

I've been trying to incorporate middleware into my current project, a Java server. When I learned JavaScript/Express, middleware was a bit of magic. Provide a callback and Express takes care of everything. I could see the results of using this and had some understanding of what middleware was, but I realized in attempting to write my own middleware that my understanding wasn't very deep. So what is middleware in terms of an HTTP server.

Middleware is a series of functions between a raw HTTP request and its final route. Suppose you wanted to do something with each HTTP request coming in, perhaps log each one. You might also want to log each response coming out. Middleware provides the shared functionality to accomplish something like this. In addition to logging, some other common uses for middleware you might see include authentication, serving static client-side assets, handling cookies, dealing with errors, etc.

Middleware is often referred to as the middleware stack. To see why, we can consider the middleware below.

1. Authorization
2. Routing
3. Static files
4. Final (for errors and unknown URLs)

Imagine you are a request coming in for an unknown URL. You would pass into authorization. You are indeed authorized. You move on to routing. You find no match here and move on. You keep moving on until the final middleware. The final middleware recognizes you as an unknown URL. A 404 response is triggered. The response moves back up, out of the final middelware and keeps going back the way it came.

This is of course an overly simplied version. Often times the middleware receives a request and a response/response writer, or some combined structure. For example, Elixir's [Plug](https://github.com/elixir-plug/plug) operates on a connection struct called `conn`. A plug takes a `conn` in and returns a `conn`, manipulating whatever it needs to in the struct to achieve the goal of that particular middleware. The idea remains the same though. Each plug has a `call` method which is passed `conn`. The series of plugs keep modifying the request in `conn` until a response can be formed.

Again, a similar idea exists in [Rack](https://rack.github.io/). Rack middleware takes a `env` hash and returns a three element array of the following:

1. The HTTP response code;
2. A Hash of headers; and
3. The response body, which must respond to each.

Like plug, each Rack middleware also has a `call` method which is passed information in a uniform manner.

The uniformity of middleware allows middleware to be placed on the middleware stack in any order, again with the stipulation that middleware is called in the order it is added.
