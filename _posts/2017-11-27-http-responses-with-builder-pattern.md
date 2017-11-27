---
layout: post
title:  "HTTP Responses with Builder Pattern"
date:   2017-11-27 10:00:00 -0600
categories: jekyll update
---

For my current project, I am working on an HTTP server in Java.  While I started reading about design patterns a few months ago, working on this project has really brought them to the forefront. As I’ve encountered different issues in my server, it has been pretty nifty to apply different patterns as possible solutions and get a sense of their potential.

One particular problem I have encountered is creating different responses from my server to client requests. At first, creating responses was quite simple. I only needed to respond to GET requests with either a 200 or 404 response. I created an interface with implementations for a 404 response, a 200 response for a file, and a 200 response for a directory to meet project requirements. My responses always looked about the same:

{% highlight html %}
  HTTP/1.1 *code*
  Content-Type: *type*

  *body*
{% endhighlight %}

However, my server needed to start handling other types of requests. The first request to create a need for change was a redirect. To redirect a client request, my server needed to respond with a 302 Found status code. While every other response to this point used a simple Content-Type header, the redirect would need a Location header. It also wouldn’t have a body. None of my response implementations were quite right for this, and it was getting rather unsustainable to create different implementations of this interface. Additionally, I don’t know all combinations of codes, header, and bodies which might be needed in the future. I wanted to handle any combination easily.

I decided to try out the builder pattern.

The builder pattern is designed to separate the construction of an object from its representation. So, in my case, the builder pattern would replace this `Response` interface and its implementations with a `Response` and `ResponseBuilder` class.

The `Response` class could be a bare bones representation of a response. My class looks like this:

{% highlight java %}
  public class Response {
      private Header header;
      private String body;

      public Response(Header header, String body) {
          this.header = header;
          this.body = body;
      }

      public Header getHeader() {
          return header;
      }

      public byte[] getBody() {
          return body;
      }
  }
{% endhighlight %}

Using the `ResponseBuilder` class, we can add a status code, add a body, and add as many headers as we need before constructing a response and returning it with the `build` method.

{% highlight java %}
  public class ResponseBuilder {
      String body;
      Header header;

      public ResponseBuilder() {
          this.header = new Header();
      }

      public ResponseBuilder addBody(String body) {
          this.body = body;
          return this;
      }

      public ResponseBuilder addStatus(int code) {
          header.addStatus(code);
          return this;
      }

      public ResponseBuilder addHeader(String title, String content) {
          header.addHeader(title, content);
          return this;
      }

      public Response build() {
          return new Response(header, body);
      }
  }
{% endhighlight %}

This allows us to create different responses using code like:
{% highlight java %}
  new ResponseBuilder()
      .addStatus(404)
      .addHeader("Content-Type", "text/html")
      .addBody("404: Page not found.")
      .build();
{% endhighlight %}

{% highlight java %}
  new ResponseBuilder()
      .addStatus(302)
      .addHeader("Location", "/")
      .build();
{% endhighlight %}

We can have a body or not.  We can add whatever status code we want.  We can add as many headers as we need using the same underlying `Response` class.

So far it's working out pretty well and feels much easier to use.
