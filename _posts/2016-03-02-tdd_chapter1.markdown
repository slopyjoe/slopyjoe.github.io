---
layout: post
title: "Test Driven Development Intro/Chapter 1"
date: 2016-03-02T21:16:17-07:00
comments: false
sharing: true
footer: true
categories: bookclub
---

# About 
There are two things I want to get better, 1) writing more and 2) reading more. So since we all love efficiency why not combine the two needs.
This series of posts will be target at summarizing a chapter read to how I interpreted it. The book of interest at the moment is Kent Beck's
Test Driven Development. As a note some of these posts will be short and sweet and some will be in more detail, all of which are for me and how I interpreted that chapter.
Without further ado lets begin.

# Part 1 - The Money Example

I will be trying out any examples with code and will post said code on GitHub at [bookclub-source](https://github.com/slopyjoe/bookclub-source.git)
I will try to keep it broken up by chapter then by language. Yes I want to experiment with different languages if time is permitting.

## Chapter 1 Multi-Currency Money
Ah the beginning, it always seems the hardest just to get the ball rolling but once it's in motion you can't stop it!
Before any code/test examples are given we first see what we are trying to achieve. This is an important tool that I seem to forget a lot or it gets lost in my ambition to code.

The requirement given to us is the ability to add multiple amounts in different currencies and convert the end result to a certain currency using exchange rates.
Which then implies we need to be able to convert an amount in one currency to another using an exchange rate. 

Kent Beck then writes up a todo list with the two requirements which looks like

* $5 + 10 CHF = $10 if rate is 2:1
* __$5 * 2 = $10__

Notice we tackle the second item as it is the simplest thing we can do to get the ball rolling. The main requirement is the first item but looking at it seems daunting. Not only that
but can tempt me to over optimize or over engineer a solution because I'll focus on too much too soon. The idea is finding the smallest thing I can do to get the ball rolling.

Now we can start writing some ~~code~~ tests. 

```java
public void testMultiplication() {
  Dollar five = new Dollar(5);
  five.times(2);
  assertEquals(10, five.amount);
}
```

Some of us might look at this simple test and say everything looks awesome! While some of us (me included) will look at this and curse, stating things like ew public members(amount) or OMG how
can you be mutating Dollar's amount that's sacrilegious. 
The thing to remember is if we(I) focus on making the cleanest, best looking code out there(seems like some utopia I try to always reach...) we loose site of our true requirement MAKE IT WORK. 
All we need working is to multiply two numbers within our domain (Currency or in our test's case Dollar).
Now we shouldn't forget about what we want done to this code, like making amount private but we shouldn't focus on it, instead we just add it to our list and get the test to **green**.
So our todo list will look like
* $5 + 10 CHF = $10 if rate is 2:1
* __$5 * 2 = $10__
* Make "amount" private
* Dollar side-effects
* Money rounding?

So my head is clear now and I can move on to getting the test Green which means I have to get the code to compile. To get the test to compile we will need the following 
* class Dollar
* Dollar can be constructed with an integer
* Dollar has a method *times* that takes an integer
* Dollar has a member field *amount*

With that said the code should look something like 
```java
class Dollar {
 int amount;
 public Dollar(int amount) {}
 void times(int multiplier) {}
}
```

Notice that there isn't a default value for amount or that the constructor or times methods are implemented. This is fine we just want to get the test to compile so we can see that RED. 

So now I have a failing test, and can start implementing functionality to get that test GREEN. I immediately want to start assigning amount to the amount given during construction and then mutating
amount to itself * the multiplier given when the *times* method is invoked. This might seem legit in this case but I should focus on the smallest thing I can do to get the test to pass. 
What Kent ended up with is 
```java
class Dollar {
 int amount;
 public Dollar(int amount) {}
 void times(int multiplier) { amount = 5 * 2; }
}
```

Well the test is green, woo! Wait a second we have dependencies and duplication between the test and code. The test will easily turn red if we did something like 
```java
public void testMultiplication() {
  Dollar five = new Dollar(5);
  five.times(3);
  assertEquals(15, five.amount);
}
```
I also have duplication by having the numbers 5 and 2 in both my test code and my production code. This is where the refactoring phase comes in. I keep having to remember to take small steps, so to 
help myself out I will focus on removing 5 first. The code should look something like 
```java
class Dollar {
 int amount;
 public Dollar(int amount) { this.amount = amount; }
 void times(int multiplier) { amount = amount * 2; }
}
```

Now I re-run the test and validate that I'm still passing! If it is I can move onto removing the 2 to look like 
```java
class Dollar {
 int amount;
 public Dollar(int amount) { this.amount = amount; }
 void times(int multiplier) { amount = amount * multiplier; }
}
```

Again I will re-run and validate, if all is well I can move onto making the code pretty
```java
class Dollar {
 int amount;
 public Dollar(int amount) { this.amount = amount; }
 void times(int multiplier) { amount *= multiplier; }
}
```

Re-run and yep it's still passing! Now I can mark off one thing on my list
* $5 + 10 CHF = $10 if rate is 2:1
* ~~$5 * 2 = $10~~
* Make "amount" private
* Dollar side-effects
* Money rounding?

## Summary
Creating lists helps keep my goals in line and allows me to focus on one thing at a time and just append my distractions to that list.
While I focus on one item in that list I have the ability to take the smallest steps I can to scratch off that item. Then once I get a green test I have the ability to take small steps to making the
code right and pretty(time permitting of course).
This is helpful for me as well as for people that suffer from the curse of too many options and paths. The above code could be implemented in any number of ways all of which are right as long as they
meet the requirement. The goal is to get the ball rolling!