---
layout: post
title:  "Abstract Classes, Template Pattern"
date:   2017-11-28 10:00:00 -0600
categories: jekyll update
---

As part of my Java server, I needed to validate parameters from the user for a port number and root directory.  The initial validator I created was inflexible, tied very closely to the validation of these parameters with no easy means to incorporate any additional validation.

In an attempt to remedy the situation, I began breaking validation into a structure like this:

* A validator interface with implementations for a port validator and directory validator.

* A validation class which iterates over a list of validators (port and directory in our case) and runs the validation methods of each.

While this strategy worked, studying each validator implementation, I found that the code was very [unDRY](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself).  The port and directory validators shared a substantial amount of code.  The use of an interface was not working and I would need to figure out another strategy.  I began experimenting with abstract classes.

## What is an abstract class?

* An abstract class is declared with the `abstract` keyword.

Regular class:
{% highlight java %}
  public class RegClass {
    //...
  }
{% endhighlight %}

Abstract class:
{% highlight java %}
  public abstract class AbsClass {
    //...
  }
{% endhighlight %}

* An abstract class cannot be instantiated.  Subclasses must be created which extend the abstract class.

Extending:
{% highlight java %}
  public class AbsSubclass extends AbsClass {
    //...
  }
{% endhighlight %}

Recall that interfaces contain method signatures without implementation.  Abstract classes allow for methods with and without implementation.

In my case, instead of the interface, I created this abstract class:

{% highlight java %}
  public abstract class Validator {
      final ArrayList errors;
      private final String flag;
      private final String argError;

      Validator(String flag, String argError) {
          this.errors = new ArrayList<>();
          this.flag = flag;
          this.argError = argError;
      }

      Final ArrayList validate(List args) {
          int flagIndex = args.indexOf(flag);
          int argIndex = flagIndex + 1;

          if (flagProvided(flagIndex)) {
              if (argProvided(argIndex, args)) {
                  String arg = args.get(argIndex);
                  checkForErrors(arg);
              } else {
                  errors.add(argError);
              }
          }

          return errors;
      }

      private boolean flagProvided(int flagIndex) {
          return flagIndex != -1;
      }

      private boolean argProvided(int argIndex, List args) {
          return argIndex < args.size();
      }

      protected abstract void checkForErrors(String arg);
  }
{% endhighlight %}

`validate`, `flagProvided`, and `argsProvided` are the methods where logic was shared between validators.  Notice the following line:

{% highlight java %}
  protected abstract void checkForErrors(String arg);
{% endhighlight %}

We provide the signature for a method with no implementation.  `checkForErrors` is a method used by the `validate` method of the abstract class, but which contained different implementation for each validator.

With this abstract class structure in place, we can create validators which extend the `Validator` class, providing implementation for `checkForErrors` (and also additional methods needed).

Our port validator:

{% highlight java %}
  public class PortValidator extends Validator {
      public PortValidator() {
          super("-p", "Valid port option not provided. Please include '-p' followed by a valid port.");
      }

      @Override
      protected void checkForErrors(String portArg) {
          if (isNotNumeric(portArg) || isNotInRange(portArg)) {
              errors.add("Port must be a number between 1 and 65535.");
          }
      }

      private boolean isNotNumeric(String portArg) {
          return !portArg.matches("[-+]?\\d*\\.?\\d+");
      }

      private boolean isNotInRange(String portArg) {
          Integer portNum = Integer.parseInt(portArg);

          return 1 > portNum || portNum > 65535;
      }
  }
{% endhighlight %}

Our directory validator:

{% highlight java %}
  public class DirectoryValidator extends Validator {
      private final String home;
      private String separator = FileSystems.getDefault().getSeparator();

      public DirectoryValidator(String userHome) {
          super("-d", "Valid directory option not provided.  Please include '-d' followed by a valid directory.");
          this.home = userHome;
      }

      @Override
      protected void checkForErrors(String dirArg) {
          Path dir = Paths.get(home + separator + dirArg);

          if (!Files.exists(dir) && !Files.isDirectory(dir)) {
              errors.add("Valid directory not provided in options.");
          }
      }
  }
{% endhighlight %}

Notice how super is called in each of the constructors with arguments for our `flag` and `argError`.
Notice the `@Override` and implementation provided in each for `checkForErrors`.

The abstract class was immensely useful in achieving what I set out to do.  It turns out that I was also using another design pattern without realizing it at the time: template pattern.

## What is template pattern?

Template pattern is a behavioral design pattern in which an abstract class defines templates to execute its methods.  Subclasses can override method implementation as needed, but the invocation remains the same.

In the case of our `Validator`, the `validate` method is final and cannot be changed.  `checkForErrors` provides a template for execution and the port and directory validators override the implementation (or lack thereof).
