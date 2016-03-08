---
layout: post
title: "Test Driven Development Chapter 2"
date: 2016-03-07T21:16:17-07:00
comments: false
sharing: true
footer: true
categories: bookclub
---


## Chapter 2 Degenerate Objects

Kent Beck start off talking about three rules

1. Make a test - the test serves as design documentation. How a piece of code should be invoked, what setup it needs, what outputs should we expect, and hopefully not any but what side-effects it can produce.
I see it as I love to write code but I hate to write technical documentation. By writing tests I get to start off with documentation of the code using code!!! (Crazy right?!)
1. Make it run - now that the documentation is done we do as little as can be done to make the documentation true. The key to remember to do **as little as possible**. This is liking making the code work but hiding it in the basement far far far away from anyone else.
1. Make it right - this is where we polish up the code as if we want to show it off to the world. Clean up the horrors we created by making it run. The documentation is our guide letting us know instantly when we
strayed off the path.

In chapter one we focused on making a test and making it run. In doing so we created a side-effect, everytime we invoke the *times* method we mutate the *amount* Dollar contains. Since we have it running lets
make it right.

Here is our checklist with current working item in bold

* $5 + 10 CHF = $10 if rate is 2:1
* ~~$5 * 2 = $10~~
* Make "amount" private
* **Dollar side-effects**
* Money rounding?

Before we go making the code right we need to update the ~~documentation~~ test. We want something like

```java
public void testMultiplication() {
  Dollar five = new Dollar(5);
  Dollar product = five.times(2);
  assertEquals(10, product.amount);
  product = five.times(3);
  assertEquals(15, product.amount);
}
```

Notice that the test fails to compile with the above changes. We again revert to step two *Make it run*, this means we need to update our code just enough to get the test to compile and see that failure.

```java
class Dollar {
 int amount;
 public Dollar(int amount) { this.amount = amount; }
 public Dollar times(int multiplier) {
   amount *= multiplier;
   return null;
 }
}
```

This little stub gives us the ability to run the test and have a starting point of what to fix. Now it's time to make it work, making small steps of course.

```java
class Dollar {
 int amount;
 public Dollar(int amount) { this.amount = amount; }
 public Dollar times(int multiplier) {
   return new Dollar( amount * multiplier );
 }
}
```

And bam! we removed the side-effects and updated some documentation. It is interesting seeing how during the third phase of making it right we also had to update the documentation and make it run. Just because we
are on the last phase doesn't mean we can't go back to other phases as needed.

Kent Beck left off the chapter talking about the importants of our ~~documentation~~ tests. It gives us something to talk about. It gives future developers something to look at and maybe not understand the
decisions made but definitely understand the way some operation is meant to be called, what is expected of it, and what can be left behind by it. In other words this may not answer the why's of our software but it
answers the how's and what's of our software.
