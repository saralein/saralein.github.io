---
layout: post
title:  "Java equals and hashCode"
date:   2018-02-23 10:00:00 -0600
categories: jekyll update
---

I spent a little bit of time this week learning about `equals` and `hashCode` and trying to implement them on custom classes I created for my server. In this blog post, I want to recap some of what I've learned in the course of this (mostly about `equals`).

### `equals`

In the Java source code, `Object` serves as the root of class hierarchy, the superclass for all classes in Java. `Object` class contains the `equals` method, which subclasses can override.

`equals` indicates whether the object using the method and some other object are equal. The [documentation](http://grepcode.com/file/repository.grepcode.com/java/root/jdk/openjdk/6-b14/java/lang/Object.java?av=f) in `Object` outlines these principals for equality:

* It is *reflexive*: for any non-null reference value `x`, `x.equals(x)` should return `true`.
* It is *symmetric*: for any non-null reference values `x` and `y`, `x.equals(y)` should return `true` if and only if `y.equals(x)` returns `true`.
* It is *transitive*: for any non-null reference values `x`, `y`, and `z`, if `x.equals(y)` returns `true` and `y.equals(z)` returns `true`, then `x.equals(z)` should return `true`.
* It is *consistent*: for any non-null reference values `x` and `y`, multiple invocations of `x.equals(y)` consistently return `true` or consistently return `false`, provided no information used in equals comparisons on the objects is modified.
* For any non-null reference value `x`, `x.equals(null)` should return `false`.


In `Object` the implementation is simple:

```java
public boolean equals(Object obj) {
    return (this == obj);
}
```

This default implementation uses shallow comparison to see if the two object references are the same.

In creating my implementation for `equals` in my custom class, I took at look at other implementations of the method in Java. I remember when I started Java, I would often forget that `hello == hello` is not the same as `hello.equals(hello)`. I realized `==` was the shallow comparison see above, but hadn't taken a look at `equals` on `String`.

So, for comparison to `Object` `equals`, the `String` implementation is this:

```java
public boolean equals(Object anObject) {
    if (this == anObject) {
        return true;
    }
    if (anObject instanceof String) {
        String anotherString = (String)anObject;
        int n = value.length;
        if (n == anotherString.value.length) {
            char v1[] = value;
            char v2[] = anotherString.value;
            int i = 0;
            while (n-- != 0) {
                if (v1[i] != v2[i])
                    return false;
                i++;
            }
            return true;
        }
    }
    return false;
}

```

We see that this implementation first does a shallow comparison and returns `true` if the references are equal. If not, it then goes on to first check the type and then compare each character of the two objects to establish equality (or not).

Custom implementations of `equals` generally follow a similar pattern.  Imagine you had a class like the one below:

```java
public class Cat {
    private final String name;
    private final String breed;
    
    public Cat(String name, String breed) {
        this.name = name;
        this.breed = breed;
    }
    
    ...
}
```

You might compare each field (name and breed).  Your implementation would look something like this:

```java
public boolean equals(Object o) {
    if (this == o) return true;
    if (o == null || getClass() != o.getClass()) return false;

    Cat cat = (Cat) o;

    if (!name.equals(cat.name)) return false;
    return breed.equals(cat.breed);
}
```

### Shortcut

In the course of my reading, I had read a recommendation for `hashCode` implementation that looks something like below.

Imagine the same class again:

```java
public class Cat {
    private final String name;
    private final String breed;
    
    public Cat(String name, String breed) {
        this.name = name;
        this.breed = breed;
    }
}
```

In the recommendation I saw, the hashCode implement would be this:

```java
public class Cat {
    
    ...
    
    public int hashCode() {
        int result = 17;
        result = 31 * result + name.hashCode();
        result = 31 * result + breed.hashCode();
        return result;
    }
    
    ...
}
```

After asking my mentors about this, one of my mentors pointed me to the Intellij [equals/hashCode wizard](https://www.jetbrains.com/help/idea/generate-equals-and-hashcode-wizard.html). With the press of a few buttons and a few selections in a menu, Intellij can implements both methods for you. It is pretty snazzy. And Intellij's implementation looks very similar to the recommedation I read:

```java
public int hashCode() {
    int result = name.hashCode();
    result = 31 * result + breed.hashCode();
    return result;
}
```
